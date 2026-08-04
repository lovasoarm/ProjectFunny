# Erreurs exploitables et idempotence

## La scène

Le chauffeur d'une tournée de livraison confirme la remise d'un colis depuis son téléphone,
dans une zone où le réseau passe de la 4G à rien en permanence. L'appli terrain envoie
`POST /deliveries/482/confirm`. La requête part, le réseau coupe avant que la réponse ne
revienne. L'appli, programmée pour ne jamais laisser un chauffeur bloqué, retente
automatiquement trente secondes plus tard. Cette fois la requête passe et reçoit un `200`.
Résultat côté serveur : deux enregistrements de confirmation, un appel automatique au client
final ("votre colis a été livré") envoyé deux fois, et une anomalie dans le calcul de la
prime du chauffeur qui compte le colis deux fois. Personne n'a rien fait de faux : le réseau
a fait exactement ce que fait un réseau mobile réel, et l'appli a fait exactement ce qu'on
lui demande de faire pour rester utilisable sur le terrain. Le trou est ailleurs : le serveur
n'avait aucun moyen de savoir que la deuxième requête était une répétition de la première, et
pas une deuxième confirmation légitime.

## Ce qui se passe vraiment

### Un réseau ne garantit jamais "exactement une fois"

Sur un réseau réel, un appelant qui envoie une requête et n'obtient pas de réponse ne sait
jamais laquelle de ces trois choses s'est produite :

```text
1. La requête n'est jamais arrivée au serveur.        → il faut réessayer
2. La requête est arrivée, traitée, mais la réponse    → réessayer duplique l'effet
   s'est perdue sur le chemin du retour.
3. La requête est arrivée et est en cours de           → réessayer peut créer une
   traitement au moment où la connexion a coupé.         concurrence avec elle-même
```

Le seul choix qui existe vraiment n'est pas "retenter ou pas" : sans retry, une coupure
réseau anodine devient un échec permanent pour l'utilisateur, ce qui est pire. Le choix est
**"comment rendre le retry sans danger"**. C'est exactement ce que l'idempotence résout.

### Idempotence : la propriété qui rend un retry sûr

Une opération est idempotente si l'exécuter plusieurs fois produit le même résultat que
l'exécuter une seule fois. Certaines opérations le sont par nature, d'autres ne le sont
jamais sans aide explicite.

```text
GET     → idempotent par nature (lire deux fois ne change rien)
PUT     → idempotent par nature SI le corps décrit l'état final complet
          (remplacer "adresse = rue X" deux fois de suite donne le même état)
DELETE  → idempotent par nature (supprimer un objet déjà supprimé = déjà à l'état voulu)
POST    → PAS idempotent par nature : chaque appel dit "crée une nouvelle chose"
          POST /deliveries/482/confirm appelé deux fois = deux confirmations, sauf garde-fou
```

Le cas qui casse tout est presque toujours `POST` : créer une ressource, déclencher un
paiement, confirmer une livraison. C'est précisément le cas de la scène.

### La clé d'idempotence : faire porter la déduplication par le client

La solution standard consiste à faire porter au client la responsabilité de nommer sa propre
tentative, et au serveur celle de se souvenir des tentatives déjà vues :

```typescript
// Le client génère un identifiant unique PAR INTENTION D'ACTION, pas par requête HTTP.
// Le même identifiant est réutilisé sur chaque tentative de retry de LA MÊME intention.
const idempotencyKey = crypto.randomUUID(); // généré une fois, au moment du clic

async function confirmDelivery(deliveryId: string, key: string) {
  return fetch(`/deliveries/${deliveryId}/confirm`, {
    method: "POST",
    headers: { "Idempotency-Key": key },
  });
}

// Côté serveur : avant de traiter, vérifier si cette clé a déjà été vue.
async function handleConfirm(req: Request) {
  const key = req.headers.get("Idempotency-Key");
  if (!key) return respond(400, { code: "MISSING_IDEMPOTENCY_KEY" });

  const existing = await idempotencyStore.get(key);
  if (existing) {
    // Requête déjà traitée : renvoyer EXACTEMENT la même réponse, sans rejouer l'effet.
    return respond(existing.statusCode, existing.body);
  }

  // Verrou court pour éviter que deux requêtes avec la même clé s'exécutent en parallèle
  // (deux onglets, un double-tap rapide avant que la première réponse ne revienne).
  const lock = await idempotencyStore.tryLock(key);
  if (!lock) return respond(409, { code: "REQUEST_IN_PROGRESS" });

  const result = await confirmDeliveryOnce(req.params.deliveryId);
  await idempotencyStore.save(key, result); // mémorisé pour les tentatives suivantes
  return respond(200, result);
}
```

Point-clé souvent raté : la clé d'idempotence doit être générée **au moment de l'intention
utilisateur** (le clic), pas à chaque tentative réseau. Si l'appli génère une nouvelle clé à
chaque retry automatique, la déduplication ne sert à rien : c'est exactement l'erreur qui a
créé le doublon dans la scène du chauffeur.

### Codes d'erreur : lisibles par une machine avant d'être lisibles par un humain

Un message d'erreur en français ("Le créneau n'est plus disponible") est utile à un humain
qui debug dans les logs, inutile à un client qui doit décider automatiquement quoi faire.
Un contrat d'erreur sérieux sépare ces deux besoins :

```json
{
  "code": "SLOT_ALREADY_BOOKED",
  "message": "Le créneau du 14h30 a été réservé entre-temps par un autre client.",
  "retryable": false,
  "details": { "slotId": "slot_492", "conflictingBookingId": "bk_881" }
}
```

`code` est un identifiant stable, testé par le code client (`if (error.code ===
"SLOT_ALREADY_BOOKED")`), qui ne change jamais même si `message` est traduit ou reformulé.
`retryable` dit explicitement au client s'il vaut la peine de réessayer : une information que
le seul code HTTP ne porte pas toujours sans ambiguïté (voir plus bas). `details` porte des
données structurées exploitables (proposer le créneau suivant, afficher qui a réservé) sans
avoir à parser un message en langage naturel, qui casse à la moindre reformulation.

### Codes HTTP : ce qu'ils disent vraiment sur la marche à suivre

```text
Code   Sens réel pour un client automatique
────   ───────────────────────────────────────────────────────────────────────
400    Ma requête est mal formée. Retenter à l'identique ne sert à rien.
401    Je ne suis identifié par aucun moyen valable. Retenter sans changer le
       token ne sert à rien.
403    Je suis identifié, mais je n'ai pas le droit. Retenter ne sert à rien
       tant que les permissions ne changent pas.
404    La ressource n'existe pas (ou n'existe plus). Retenter ne sert à rien.
409    Conflit d'état (créneau déjà pris, version obsolète). Retenter SANS
       changer la requête ne sert à rien ; retenter APRÈS relecture de l'état
       peut réussir.
422    La requête est bien formée mais viole une règle métier (validation).
       Retenter à l'identique ne sert à rien.
429    Trop de requêtes. Retenter APRÈS le délai indiqué (header
       Retry-After) a de bonnes chances de réussir.
500    Erreur inattendue côté serveur. Retenter peut réussir si transitoire,
       mais sans certitude : c'est là que l'idempotence protège vraiment.
503    Service temporairement indisponible. Retenter après un backoff a de
       bonnes chances de réussir.
```

Le piège le plus commun : traiter tout code `>= 500` comme "à retenter aveuglément" et tout
code `4xx` comme "erreur définitive". C'est globalement vrai sauf pour `409` et `429`, qui
sont des `4xx` explicitement faits pour être retentés : mais pas n'importe comment.

### Retries avec backoff exponentiel et gigue

Retenter immédiatement et en boucle serrée après un échec est une des façons les plus
fiables de transformer une panne partielle en panne totale : des milliers de clients qui
retentent tous à l'instant T+1 seconde créent une deuxième vague de charge qui achève un
serveur déjà fragile.

```text
Tentative 1 : échoue à T=0
Tentative 2 : attendre ~1s  (+ gigue aléatoire de quelques centaines de ms)
Tentative 3 : attendre ~2s  (+ gigue)
Tentative 4 : attendre ~4s  (+ gigue)
Tentative 5 : attendre ~8s  (+ gigue), puis abandonner et remonter l'échec à l'humain

La gigue (jitter) évite que tous les clients retentent EXACTEMENT au même instant après
une panne partagée : sans elle, les tentatives se resynchronisent en vagues, créant des
pics de charge périodiques au lieu d'un trafic lissé.
```

### Timeouts : décider en combien de temps "je ne sais pas" devient "j'abandonne"

Un appel sans timeout n'échoue jamais explicitement : il reste juste suspendu, consommant une
connexion, un thread, un slot de file d'attente, jusqu'à ce que la ressource sous-jacente
lâche pour une raison sans rapport apparent. Chaque appel réseau doit avoir un timeout
explicite, choisi en fonction de ce que l'opération fait, pas d'une valeur par défaut copiée
partout :

```text
Lecture rapide (GET consommation d'un site)        → timeout court (quelques secondes)
Écriture avec effet métier (confirmer livraison)   → timeout un peu plus long, mais fini
Appel à un service tiers lent connu (facturation    → timeout généreux, MAIS avec un
externe, relevé de compteur physique)                 idempotency key posé avant l'appel
```

Un timeout côté client ne dit pas "l'opération a échoué côté serveur" : il dit seulement
"je ne sais plus". C'est le même problème que la coupure réseau de la scène, et la même
solution s'applique : ne jamais retenter une opération à effet sans clé d'idempotence.

Analogie : gérer les erreurs et l'idempotence, c'est le tri des urgences hospitalières qui
reconnaît un patient déjà pris en charge pour ne pas le réenregistrer, et un appel de détresse
en navigation maritime qu'il faut pouvoir répéter sans déclencher deux sauvetages.
Où l'analogie casse : un soignant ou un opérateur radio reconnaissent un visage ou une voix.
Un serveur ne reconnaît une requête répétée que si le client a pris soin d'envoyer une clé
d'idempotence, sinon deux appels identiques sont deux événements distincts pour lui.

## Compromis

| Option                                            | Coût                                                                                                           | Bénéfice                                                                    | Quand choisir                                                                                             |
| ------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| Idempotency key obligatoire sur tout POST à effet | Le client doit générer et transmettre une clé, le serveur doit stocker un historique de clés (avec expiration) | Retries sûrs, zéro duplication même sur réseau instable                     | Toute opération à effet (paiement, création, confirmation) appelée depuis un client sur réseau non fiable |
| Pas d'idempotency key, `POST` nu                  | Rien à implémenter côté client ni serveur                                                                      | Simplicité                                                                  | Opérations à très faible enjeu, ou appelées uniquement depuis un environnement réseau fiable et contrôlé  |
| Codes d'erreur structurés (`code` + `retryable`)  | Discipline de nommage stable, tests de non-régression sur les codes                                            | Clients automatiques fiables, moins de code fragile basé sur du texte parsé | Dès qu'un client automatique (pas seulement un humain) consomme les erreurs                               |
| Backoff exponentiel avec jitter                   | Latence perçue plus longue en cas de panne réelle avant abandon                                                | Évite l'effondrement en cascade d'un service déjà fragile                   | Tout appel réseau retenté automatiquement, en particulier vers un service partagé                         |

## Pièges classiques

- **La clé d'idempotence régénérée à chaque tentative.** Symptôme : les doublons persistent
  malgré la présence d'un mécanisme d'idempotence, parce que le client génère une nouvelle
  clé à chaque retry au lieu de réutiliser celle de l'intention initiale.
- **Le message d'erreur comme seul contrat.** Symptôme : un client qui fait
  `if (error.message.includes("créneau"))`, qui casse dès que le message est reformulé ou
  traduit dans une autre langue.
- **Le retry sans backoff.** Symptôme : un pic de charge synchronisé qui réapparaît toutes les
  N secondes pile après une panne, aggravant une dégradation au lieu de la résorber.
- **L'absence de timeout.** Symptôme : des connexions qui s'accumulent silencieusement sur le
  serveur, un pool de connexions épuisé, un incident qui ressemble à une fuite mémoire alors
  que la cause est un appel externe qui ne répond jamais et ne timeout jamais.
- **Le `409` traité comme un `500`.** Symptôme : un client qui abandonne ou alerte un humain
  sur un conflit d'état parfaitement normal (deux utilisateurs ont réservé le même créneau en
  même temps), alors qu'il aurait dû relire l'état et proposer une alternative automatiquement.

## Ce que tu dois savoir défendre

- Explique pourquoi `POST` n'est pas idempotent par nature, avec un exemple où ça cause un
  vrai dégât, et comment une clé d'idempotence corrige précisément ce problème.
- Pourquoi un code d'erreur stable (`SLOT_ALREADY_BOOKED`) vaut mieux qu'un message texte
  pour un client qui doit réagir automatiquement à l'erreur.
- Donne un exemple où retenter une requête sans backoff peut aggraver une panne au lieu de
  la résorber.
