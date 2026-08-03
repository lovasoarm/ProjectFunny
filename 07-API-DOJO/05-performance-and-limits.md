# Pagination, rate limiting, cache HTTP, latence perçue

## La scène

L'API du cabinet vétérinaire expose `GET /patients/{clinicId}/appointments`, qui retourne
tout l'historique de rendez-vous d'une clinique. Au lancement, une clinique a douze rendez-
vous archivés, la réponse fait 3 kilooctets, tout va bien. Deux ans plus tard, la plus grosse
clinique cliente a accumulé quatorze mille rendez-vous. Le même endpoint, jamais retouché,
renvoie maintenant onze mégaoctets de JSON à chaque appel, l'appli mobile met neuf secondes à
afficher un écran qui devrait s'afficher en une seconde, et un cron nocturne du logiciel de
comptabilité partenaire, qui appelle cet endpoint toutes les cinq minutes pour "vérifier s'il
y a du nouveau", commence à saturer la bande passante du serveur au point de ralentir tous les
autres clients au même moment. Rien n'a changé dans le code. Le succès a suffi à transformer
un endpoint honnête en incident.

## Ce qui se passe vraiment

### Pagination : ne jamais promettre de retourner "tout"

Un endpoint de liste sans pagination fonctionne à la démo et casse à l'échelle, toujours au
même rythme : lentement puis d'un coup. La pagination n'est pas une optimisation à ajouter
plus tard, c'est une garantie de contrat à poser dès le premier jour, même quand elle semble
inutile sur douze rendez-vous.

```text
Pagination par offset (page + taille) :
  GET /appointments?page=3&size=50
  + Simple à comprendre, à implémenter, à naviguer ("aller à la page 7")
  - Coûteuse en base sur de gros volumes (SKIP doit quand même parcourir les lignes sautées)
  - Instable si des lignes sont insérées/supprimées entre deux appels : un même rendez-vous
    peut apparaître deux fois ou disparaître d'une page à l'autre pendant la pagination

Pagination par curseur (basée sur une clé stable, ex: dernier ID vu) :
  GET /appointments?after=apt_9931&size=50
  + Stable même si des lignes sont insérées/supprimées pendant la navigation
  + Performance constante quelle que soit la profondeur de pagination
  - Pas de "aller directement à la page 7" : uniquement navigation séquentielle
  - Le curseur doit être un identifiant opaque et stable, jamais recalculable par le client
```

```json
{
  "data": [
    /* 50 rendez-vous */
  ],
  "pagination": {
    "nextCursor": "apt_9981",
    "hasMore": true
  }
}
```

Le choix par défaut raisonnable pour la plupart des API à volume croissant est le curseur —
l'offset devient un piège de performance et de cohérence dès que le volume dépasse quelques
milliers de lignes actives. La taille de page doit avoir un maximum imposé côté serveur
(`size` plafonné, ex. 100), sinon un client peut demander `size=999999` et recréer
exactement le problème que la pagination devait résoudre.

### Rate limiting : protéger le système d'un client, même honnête

Le cron du logiciel comptable de la scène n'est pas malveillant : il est juste mal
configuré. Le rate limiting protège le système de ce cas bien plus fréquent que l'attaque
délibérée : un partenaire en boucle infinie accidentelle, un script de test oublié en
production, une resynchronisation trop agressive après une panne.

```text
Fenêtre fixe :
  100 requêtes par minute, compteur remis à zéro à chaque minute pile
  - Effet de bord : un client peut envoyer 100 requêtes à 59s puis 100 à 61s,
    soit 200 requêtes en 2 secondes autour de la frontière de fenêtre

Fenêtre glissante / seau de jetons (token bucket) :
  Un seau se remplit d'un jeton toutes les 600ms, chaque requête en consomme un
  + Lisse le trafic dans le temps, pas d'effet de bord de frontière
  + Autorise des pics courts (burst) sans punir un usage normal légèrement irrégulier
```

Réponse attendue en cas de dépassement, exploitable par un client automatique :

```text
HTTP 429 Too Many Requests
Retry-After: 42
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1735689600
```

Un client bien écrit lit `Retry-After` et attend ce délai précis avant de retenter, au lieu
de deviner un backoff arbitraire qui pourrait retomber encore dans la fenêtre limitée.

### Cache HTTP : réduire le travail refait pour rien

La consommation d'énergie d'hier ne change plus une fois le jour terminé. Pourtant, sans
cache, chaque appel au comparateur de prix recalcule et retransmet la même réponse identique
des dizaines de fois par heure.

```text
Cache-Control: max-age=3600          → le client (ou un cache intermédiaire) peut réutiliser
                                        la réponse pendant 1h sans rappeler le serveur

ETag: "a1b2c3d4"                     → empreinte du contenu ; le client renvoie
If-None-Match: "a1b2c3d4"              cette empreinte au prochain appel, le serveur répond
                                        304 Not Found Modified (corps vide) si rien n'a changé,
                                        évitant de retransmettre un contenu identique
```

```text
Flux avec ETag :

Client                          Serveur
  │  GET /consumptions/site_1     │
  │ ───────────────────────────> │  calcule, renvoie 200 + ETag: "a1b2c3d4"
  │ <─────────────────────────── │
  │  (60 secondes plus tard)      │
  │  GET /consumptions/site_1     │
  │  If-None-Match: "a1b2c3d4"    │
  │ ───────────────────────────> │  compare, rien n'a changé
  │ <─────────────────────────── │  304 Not Modified, corps vide
```

Le piège du cache n'est jamais technique, il est humain : décider une durée de fraîcheur
(`max-age`) exige de répondre à "à quel point puis-je tolérer une donnée légèrement
périmée ?" : une décision métier, pas un réglage arbitraire copié d'un tutoriel.

### Charges utiles : ne transmettre que ce qui sert

```text
Champ renvoyé mais jamais utilisé par aucun client connu = coût réseau payé pour rien,
répété à chaque appel, à chaque client, indéfiniment.

Solutions, du plus simple au plus coûteux à maintenir :
  - Champs optionnels sélectionnables (?fields=id,date,statut) : le client choisit
  - Endpoints distincts pour vue "résumé" vs vue "détail complet"
  - Compression HTTP (gzip/brotli) : gratuite à activer, réduit la taille sur le fil
    sans changer le contrat, mais ne corrige pas un design de réponse trop lourd
```

### Latence perçue : ce que l'utilisateur ressent n'est pas ce que le serveur mesure

Le serveur peut répondre en 200ms et l'utilisateur ressentir une lenteur insupportable, ou
l'inverse. La latence perçue dépend de la structure de l'attente, pas seulement de sa durée
brute.

```text
Techniques qui réduisent la latence PERÇUE sans réduire la latence RÉELLE :

  - Réponse immédiate + traitement asynchrone : POST /deliveries répond 202 Accepted
    tout de suite avec un identifiant de suivi, le traitement long se fait en arrière-plan,
    le client interroge ou reçoit un webhook au lieu d'attendre bloqué
  - Pagination + affichage progressif : afficher les 20 premiers résultats pendant que
    les suivants se chargent, au lieu d'attendre le total avant d'afficher quoi que ce soit
  - Retour d'état intermédiaire explicite plutôt qu'un silence total pendant l'attente
    (statut "en cours de traitement" visible, pas une roue qui tourne sans information)
```

Analogie : Analogie : pagination, rate limiting et cache HTTP, c'est une cuisine de restaurant en
service qui refuse d'envoyer toute la carte d'un coup et régule les commandes acceptées par
le pass, et un coureur de montagne qui rationne son effort et ses ravitaillements pour ne pas
craquer avant l'arrivée.
Où l'analogie casse : un chef ou un coureur ajustent leur rythme en sentant leur propre
fatigue. Un serveur ne perçoit sa charge qu'à travers des métriques déclarées à l'avance, et
un client mal limité peut continuer de taper dessus sans qu'aucune sensation ne l'arrête.

## Compromis

| Option                        | Coût                                                                                  | Bénéfice                                                                                    | Quand choisir                                                       |
| ----------------------------- | ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| Pagination par curseur        | Pas de "aller à la page N" direct, un peu plus complexe à implémenter                 | Stable et performant à tout volume                                                          | Listes à croissance non bornée (historique, journal d'événements)   |
| Pagination par offset         | Instable et coûteuse à grand volume                                                   | Simplicité, navigation directe par numéro de page                                           | Petites listes bornées, tableaux de bord internes à faible volume   |
| Rate limiting en token bucket | Implémentation et état à maintenir (compteurs, expiration)                            | Absorbe les pics courts sans punir un usage normal, protège des boucles accidentelles       | Toute API exposée à plus d'un client non totalement maîtrisé        |
| Cache HTTP avec ETag          | Complexité de calcul d'empreinte, risque de servir une donnée périmée si mal invalidé | Réduction drastique du trafic redondant, réponses quasi instantanées sur données inchangées | Données lues souvent, modifiées rarement (historique, référentiels) |

## Pièges classiques

- **L'endpoint sans pagination "parce qu'il n'y a jamais beaucoup de données".** Symptôme :
  un endpoint qui devient un incident de performance des mois après son lancement, sans
  qu'aucune ligne de code n'ait changé entre-temps.
- **Le rate limiting absent jusqu'au premier incident.** Symptôme : un client honnête mais
  mal configuré (boucle, cron trop fréquent) dégrade le service pour tous les autres clients
  en même temps, sans qu'aucune alerte n'ait prévenu avant la panne complète.
- **Le cache jamais invalidé.** Symptôme : un client affiche une donnée obsolète après une
  mise à jour, parce que la durée de cache a été choisie arbitrairement longue sans réflexion
  sur la fraîcheur métier réellement tolérable.
- **L'offset de pagination qui saute ou duplique des lignes.** Symptôme : un client qui
  parcourt toutes les pages d'une liste active manque certains éléments ou en voit d'autres
  deux fois, parce que des insertions ont eu lieu pendant la pagination.
- **La confusion entre latence réelle et perçue.** Symptôme : une équipe optimise agressivement
  le temps de réponse serveur (passe de 300ms à 150ms) alors que l'utilisateur perçoit
  toujours l'attente comme longue faute de tout retour d'état intermédiaire.

## Ce que tu dois savoir défendre

- Explique pourquoi la pagination par offset devient un piège de performance et de
  cohérence à mesure que le volume de données grandit, avec l'exemple de la clinique.
- Pourquoi le rate limiting protège autant, sinon plus, contre des clients honnêtes mal
  configurés que contre des attaques délibérées.
- Donne un exemple de technique qui réduit la latence perçue sans réduire la latence réelle,
  et explique pourquoi ça compte quand même pour l'utilisateur.
