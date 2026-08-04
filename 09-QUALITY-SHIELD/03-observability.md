# Logs structurés, métriques, traces, alertes utiles

## Le piège

Le système de tournées de livraison frigorifique tombe en panne un mardi à 14h : des
livreurs signalent que l'application de tournée n'affiche plus les commandes du jour.
L'équipe se connecte en SSH sur le serveur, ouvre les logs bruts avec `tail -f`, et voit
défiler des milliers de lignes de texte libre écrites par douze développeurs différents sur
trois ans, chacune dans un format différent : `"Erreur commande"`, `[ERROR] cmd failed
id=482`, `Exception in thread pool-3`. Personne ne peut répondre en moins de vingt minutes à
la question la plus simple : combien de tournées sont affectées, et depuis quand. Le vrai
problème n'est pas l'absence de logs : il y en a des millions de lignes. Le problème est
qu'aucune de ces lignes n'a été pensée pour répondre à une question qu'on se pose _pendant_
un incident, seulement pour tracer ce que le développeur trouvait intéressant au moment où
il écrivait la ligne.

## Ce qui se passe vraiment

L'observabilité n'est pas "avoir des logs". C'est la capacité d'un système à répondre à des
questions qu'on n'a pas anticipées à l'avance, à partir de données qu'il a produites de
lui-même. La nuance compte : un dashboard qui affiche un graphique prévu à l'avance
("nombre de commandes par heure") est du monitoring : utile, mais limité aux questions
posées avant l'incident. L'observabilité va plus loin : elle permet de creuser une question
surgie en plein incident ("pourquoi ces 40 tournées précises sont bloquées, et seulement
elles").

```text
Monitoring                          Observabilité
"Le taux d'erreur est à 12 %"       "Le taux d'erreur est à 12 %, et en filtrant
 (dashboard prévu à l'avance)        par entrepôt, il monte à 90 % sur celui de
                                     Lyon-Est depuis 14h03, corrélé au déploiement
                                     du service de température à 14h01"
```

Trois piliers, chacun répond à une question différente, et aucun ne remplace les autres.

### Logs structurés : "que s'est-il passé, précisément, ici"

Un log structuré est un événement avec des champs nommés et typés, pas une phrase en
français. La différence est immense au moment où on doit filtrer, agréger, ou chercher.

```typescript
// Mauvais : texte libre, impossible à interroger de façon fiable
console.log(
  `Livraison ${deliveryId} en retard de ${delayMinutes} minutes sur tournée ${tourId}`,
);

// Bon : structuré, chaque champ est interrogeable indépendamment
logger.warn("delivery.delayed", {
  deliveryId,
  tourId,
  delayMinutes,
  warehouseId,
  driverId,
  coldChainStatus: currentColdChainStatus,
});
```

Avec le log structuré, la question "quelles livraisons en retard de plus de 30 minutes
viennent de l'entrepôt de Lyon-Est aujourd'hui" devient une requête directe sur les champs
`delayMinutes`, `warehouseId` et un filtre de date : pas un `grep` fragile sur une phrase.

### Métriques : "combien, et est-ce que ça dérive"

Une métrique est un nombre agrégé dans le temps : compteur, jauge, histogramme. Elle ne dit
pas _quelle_ livraison a un problème, elle dit _combien_ et _depuis quand la tendance a
changé_ : c'est ce qui permet de détecter un problème avant qu'un client ne le signale.

```typescript
// Compteur : nombre d'événements cumulés
metrics.increment("delivery.cold_chain_breach", { warehouseId });

// Histogramme : distribution d'une durée, permet de voir la médiane ET la queue
metrics.histogram("delivery.duration_seconds", durationSeconds, { tourId });

// Jauge : valeur actuelle d'un état, monte et descend
metrics.gauge("tours.currently_active", activeToursCount);
```

Le choix du type de métrique n'est pas cosmétique : une moyenne sur un histogramme de durée
masque les cas extrêmes (une tournée qui prend 5 heures au lieu de 2 ne fait presque pas
bouger la moyenne sur 500 tournées, mais elle a peut-être un camion en panne).

### Traces : "où est passé le temps, et où ça a cassé, sur ce cas précis"

Une trace suit une requête ou une opération à travers tous les services qu'elle traverse.
Utile quand une opération lente ou en échec touche plusieurs systèmes (l'API de tournées, le
service de température, le service de facturation) et qu'il faut savoir lequel est en cause.

```text
Requête "confirmer livraison #4821" : durée totale : 3.2s

├─ API tournées         [====]                     220ms
├─ Service température  [==============]           1800ms  ← le goulot
├─ Service facturation   [===]                      180ms
└─ Notification SMS      [==]                       120ms

Sans trace : on sait que la requête a pris 3.2s, on ne sait pas où.
Avec trace : on voit immédiatement que le service température est le goulot.
```

### Alertes : la partie qui coûte cher si elle est mal réglée

Une alerte n'a de valeur que si elle est actionnable : quelqu'un qui la reçoit doit savoir
quoi faire, et l'ignorer doit avoir un coût réel (sinon l'équipe apprend à l'ignorer).

```text
Mauvaise alerte : "CPU à 85 %"
  → et alors ? Le service répond-il correctement ? L'utilisateur est-il affecté ?
  → réaction probable : personne ne bouge, ou tout le monde panique pour rien.

Bonne alerte : "Taux d'échec de confirmation de livraison > 5 % pendant 3 minutes"
  → lien direct avec l'impact utilisateur, seuil clair, fenêtre de temps qui filtre le bruit.
```

Règle de fond : alerter sur des symptômes visibles par l'utilisateur (taux d'erreur, latence
perçue, échec de paiement), pas sur des causes internes possibles (CPU, mémoire) sauf si
elles ont un lien démontré et immédiat avec un impact utilisateur.

Analogie : l'observabilité, c'est les retours son et lumière qu'un régisseur surveille en
régie de spectacle pour savoir ce qui se passe sans monter sur scène, et les instruments de
navigation qui indiquent cap, vitesse et profondeur sans que le capitaine ait besoin de
regarder par-dessus bord.
Où l'analogie casse : les retours de régie et les instruments de navigation captent une
réalité physique continue. Un système logiciel ne produit que les logs, métriques et traces
qu'on a explicitement décidé d'émettre, tout le reste demeure invisible même en incident grave.

## Le coût caché de la sur-instrumentation

Logger chaque ligne de code, tracer chaque appel de fonction interne, alerter sur chaque
métrique système a un coût réel : volume de données à stocker et payer, bruit qui noie le
signal utile, et surtout un risque de sécurité si des données sensibles (dossier médical,
numéro de carte bancaire) finissent dans des logs jamais audités.

```typescript
// Danger : un log qui contient une donnée sensible finit dans des outils tiers,
// des rétentions longues, parfois accessibles à plus de monde que prévu.
logger.info("payment.processed", {
  cardNumber: card.number, // jamais : donnée sensible en clair
  customerEmail: customer.email, // à questionner selon la politique de rétention
  amount: payment.amount, // ok, utile pour le diagnostic
});
```

## Compromis

| Option                                         | Coût                                                              | Bénéfice                                            | Quand choisir                                                           |
| ---------------------------------------------- | ----------------------------------------------------------------- | --------------------------------------------------- | ----------------------------------------------------------------------- |
| Logs texte libre                               | Rapide à écrire sur le moment                                     | Presque nul en incident réel                        | Jamais en production, tout juste acceptable en script local jetable     |
| Logs structurés                                | Discipline de format, un peu plus de code au moment d'écrire      | Interrogeables, filtrables, corrélables en incident | Systématique en production                                              |
| Métriques sur symptômes utilisateur uniquement | Demande d'identifier les bons indicateurs en amont                | Alertes fiables, peu de bruit                       | Toujours en priorité sur les métriques internes                         |
| Traces distribuées                             | Instrumentation à poser sur chaque service, coût d'infrastructure | Diagnostic rapide sur les systèmes multi-services   | Dès que plus de deux services participent à une même opération critique |
| Tout instrumenter, tout alerter                | Coût de stockage, bruit, risque de fuite de données sensibles     | Illusion de tout voir                               | Jamais comme stratégie par défaut                                       |

## Pièges classiques

- Des logs en texte libre non structuré, découverts inutilisables le jour où il faut vraiment
  chercher quelque chose vite, en plein incident.
- Une alerte qui se déclenche sur une cause interne (CPU, mémoire) sans lien vérifié avec un
  impact utilisateur : le symptôme est que l'équipe finit par la couper ou l'ignorer.
- Une donnée sensible (mot de passe, numéro de carte, dossier médical) qui finit dans un log
  ou une trace, découvert lors d'un audit de sécurité plutôt qu'avant.
- Une métrique en moyenne qui masque des cas extrêmes graves noyés dans un grand nombre de
  cas normaux : le symptôme est un dashboard vert alors qu'une minorité de clients souffre.
- Confondre monitoring (dashboards prévus à l'avance) et observabilité (capacité à répondre
  à une question surgie en plein incident) : le symptôme est de se retrouver démuni dès
  qu'un problème sort du cadre des graphiques déjà construits.

## Ce que tu dois savoir défendre

1. Explique la différence entre monitoring et observabilité avec un exemple concret où le
   monitoring seul ne suffit pas à diagnostiquer un incident.
2. Pourquoi une alerte doit-elle être branchée sur un symptôme visible par l'utilisateur
   plutôt que sur une métrique système interne comme le CPU ?
3. Donne un exemple de donnée qui ne devrait jamais apparaître dans un log, et explique le
   risque concret si elle y apparaît quand même.
