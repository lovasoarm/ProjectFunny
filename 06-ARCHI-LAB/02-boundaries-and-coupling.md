# Couplage, cohésion, modules, dépendances dirigées

## Le piège

Tu gères le planning de tournées de livraison d'une petite entreprise de transport. Un module
`Tournee` calcule l'itinéraire optimal. Un module `Facturation` calcule le prix du client en
fonction de la distance parcourue. Pour aller vite, tu fais en sorte que `Facturation` importe
directement la classe interne `Itineraire` de `Tournee` et lise son champ `distanceKm` calculé
en cours de route, plutôt que d'attendre le résultat final de la tournée. Ça marche, ça te
fait gagner une demi-journée. Trois semaines plus tard, l'équipe optimisation change la façon
dont `Itineraire` calcule la distance (elle passe d'un calcul à vol d'oiseau à un calcul par
API de routage réel, en plusieurs étapes). Le champ `distanceKm` n'existe plus au même moment
du cycle de vie de l'objet. La facturation se met à générer des factures à zéro euro, en
silence, pendant deux jours, avant que quelqu'un ne remarque l'anomalie comptable.

## Ce qui se passe vraiment

### Couplage : ce que ça mesure vraiment

Le couplage n'est pas "est-ce que deux modules communiquent". Deux modules qui ne
communiquent jamais ne servent à rien ensemble. Le couplage mesure **combien un module doit
savoir sur l'intérieur d'un autre pour fonctionner**, et donc combien de changements internes
dans l'un vont se propager en cassures dans l'autre.

Il existe un gradient, du plus fort couplage (le pire) au plus faible (le mieux) :

```text
Couplage de contenu     → A lit/modifie directement les variables internes de B
                           (le pire : n'importe quel détail interne de B casse A)

Couplage commun         → A et B partagent un état global mutable
                           (une variable partagée mute sous les pieds de l'autre)

Couplage de contrôle    → A envoie à B un flag qui dicte SON comportement interne
                           ex: doWork(data, { skipValidation: true })
                           (A doit connaître la logique interne de B pour l'utiliser)

Couplage de donnée      → A envoie à B exactement les données dont B a besoin, en argument
                           explicite, sans connaître comment B les traite
                           (le mieux réaliste : A ignore tout de l'intérieur de B)

Couplage de message     → A et B communiquent par événements/messages, sans appel direct
                           (le plus faible : A ne sait même pas que B existe précisément)
```

Le couplage de contenu de l'exemple vétérinaire (`distanceKm` lu directement) est le pire
niveau du gradient. La correction n'est pas "il ne faut jamais coupler" — un couplage de
donnée entre `Tournee` et `Facturation` est nécessaire et sain, la facturation a besoin de
connaître la distance. La correction consiste à **choisir consciemment le niveau de couplage
minimal suffisant**, et à le rendre explicite via une interface stable :

```typescript
// Mauvais : couplage de contenu, Facturation connaît l'interne de Tournee
const prix = tournee.itineraire.segments[0].distanceCalculee * tarifKm;

// Bon : couplage de donnée, via un contrat explicite et stable
interface TourneeCompletee {
  distanceTotaleKm: number;   // valeur finale garantie, peu importe comment elle est calculée
  dureeMinutes: number;
}

function calculerFacture(tournee: TourneeCompletee, tarifKm: number): number {
  return tournee.distanceTotaleKm * tarifKm;
}
```

Le point-clé : `TourneeCompletee` est un contrat. Tant que ce contrat ne change pas,
`Tournee` peut changer son mode de calcul interne — passer du vol d'oiseau à une API de
routage — sans que `Facturation` ne le sache ni n'ait besoin d'être modifié.

### Cohésion : l'autre moitié de l'équation

La cohésion mesure si les éléments **à l'intérieur** d'un module ont une bonne raison d'être
ensemble. Un module à forte cohésion regroupe des choses qui changent pour la même raison, au
même moment. Un module à faible cohésion regroupe des choses qui n'ont rien à voir, réunies
par accident de code plutôt que par intention.

```text
routes/appointments.js (faible cohésion, 640 lignes)
  ├── logique de disponibilité des créneaux    → change quand les règles de planning changent
  ├── calcul de prix                            → change quand la grille tarifaire change
  ├── envoi d'email de confirmation              → change quand le fournisseur d'email change
  └── mise à jour du stock de vaccins            → change quand la logique de stock change

  4 raisons de changer différentes, mélangées dans le même fichier.
  Un changement de fournisseur d'email oblige à toucher un fichier qui gère aussi le stock.
```

La règle de Robert C. Martin (principe de responsabilité unique, souvent mal résumé en "une
classe = une fonction") se formule mieux ainsi : **un module ne devrait avoir qu'une seule
raison de changer**. Ce n'est pas une question de taille — un module de 300 lignes qui gère
uniquement la disponibilité des créneaux a une cohésion parfaite. Un module de 30 lignes qui
mélange authentification et calcul de TVA a une cohésion nulle.

Couplage et cohésion sont liés : quand la cohésion d'un module baisse (il fait des choses non
reliées), son couplage avec le reste du système grimpe presque toujours en proportion, parce
que chacune de ses responsabilités crée sa propre dépendance vers l'extérieur.

### Modules et dépendances dirigées

Un module est une unité qui expose une frontière : une interface publique, et un intérieur
caché. La frontière n'a de valeur que si elle est respectée dans un seul sens. C'est le
concept de **dépendance dirigée** : dans un système sain, les dépendances forment un graphe
acyclique — tu peux dessiner une flèche de "qui dépend de qui" et jamais revenir à ton point
de départ en suivant les flèches.

```text
Système sain (acyclique) :

  UI ──depend on──> Domaine <──depend on── Infra

  UI connaît le Domaine. Infra connaît le Domaine.
  Le Domaine ne connaît ni l'UI ni l'Infra.
  → on peut changer l'UI ou l'Infra sans toucher au Domaine.


Système malade (cycle) :

  UI ──> Domaine ──> Infra ──> UI
                       (Infra importe une fonction d'affichage pour formater un log)

  → impossible de déployer/tester Infra sans charger tout UI.
  → un changement dans UI peut casser Infra sans lien logique apparent.
```

Un cycle de dépendance est presque toujours un signal d'alarme, pas un détail. Il signifie
qu'aucune des deux parties ne peut être comprise, testée, ou changée indépendamment de
l'autre — elles forment de facto un seul gros module, mais réparties dans deux dossiers qui
prétendent être séparés.

### Comment détecter un mauvais découpage sans outil

Trois questions à te poser sur un module que tu regardes pour la première fois :

1. Si je supprime ce module et le remplace par un autre qui respecte la même interface,
   combien d'autres fichiers dois-je toucher ? (Zéro = bon découpage. Beaucoup = mauvais.)
2. Est-ce que je peux décrire ce que fait ce module en une phrase sans "et" ? ("Il calcule la
   disponibilité des créneaux" est bon. "Il calcule la disponibilité et envoie des emails et
   met à jour le stock" est un signal de faible cohésion.)
3. Si deux personnes travaillent en même temps sur deux fonctionnalités différentes, doivent-
   elles modifier le même fichier ? Si oui systématiquement, c'est que la frontière du module
   ne correspond à aucune frontière métier réelle.

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Découpage fin (beaucoup de petits modules à faible couplage) | Plus de fichiers, plus d'indirection à suivre en lecture, courbe d'entrée plus raide | Changements localisés, testabilité, parallélisation entre développeurs | Projet destiné à vivre plusieurs mois/années, équipe > 1 personne |
| Découpage grossier (peu de gros modules) | Rayon d'explosion large à chaque changement, conflits de merge fréquents | Rapidité de mise en place initiale, moins de sauts de fichier en lecture | Prototype jetable, script d'un jour, exploration technique |
| Couplage de donnée explicite (interfaces/contrats) | Écrire et maintenir le contrat, parfois redondant en apparence | Modules remplaçables indépendamment, tests isolés faciles | Dès qu'un module a plus d'un consommateur ou plus d'une implémentation possible |
| Couplage de contenu (accès direct à l'interne) | Fragilité immédiate au moindre changement interne, bugs silencieux | Vitesse d'écriture initiale | Jamais en dehors d'un script jetable de moins d'une journée |

## Pièges classiques

- **Le module "utils" fourre-tout.** Symptôme observable : `utils.js` importé par la moitié
  du projet, avec des fonctions qui n'ont aucun rapport entre elles. C'est une faible cohésion
  déguisée en organisation.
- **L'interface qui fuit l'implémentation.** Symptôme : une interface `PaymentGateway` qui
  expose une méthode `getStripeCustomerId()`. Le nom de l'implémentation concrète (Stripe) a
  fui dans le contrat censé l'abstraire — changer de fournisseur de paiement casse tout de
  toute façon.
- **Le cycle de dépendance masqué par les imports dynamiques.** Symptôme : le projet compile
  et tourne, mais un outil d'analyse de dépendances (`madge`, `dependency-cruiser`) révèle des
  cycles que personne n'avait vus parce qu'ils passaient par des imports différés.
- **La fausse frontière technique.** Découper par type de fichier (`controllers/`,
  `services/`, `models/`) donne l'illusion d'une architecture alors que chaque "feature"
  reste éparpillée sur les trois dossiers et fortement couplée en travers.
- **Le partage d'état global "juste pour cette fois".** Symptôme : une variable de module ou
  un singleton mutable utilisé pour passer une info entre deux parties du code qui, en théorie,
  ne devraient pas se connaître. Elle finit toujours par être lue à un moment où elle n'a pas
  la valeur attendue.

## Ce que tu dois savoir défendre

- Explique la différence entre couplage et cohésion avec un exemple où l'un est bon et
  l'autre mauvais dans le même module.
- Pourquoi un cycle de dépendance est presque toujours un problème, même si le code compile
  et fonctionne en apparence.
- Donne un exemple de couplage de contenu que tu as écrit ou vu, et comment le réécrire en
  couplage de donnée.
