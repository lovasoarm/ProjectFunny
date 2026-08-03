# Niveau 11 : Big App Snoop

## Ce que c'est

Jusqu'ici tu as construit. Des projets à toi, nés d'une feuille blanche, où chaque décision
t'appartenait. Ce niveau t'envoie ailleurs : dans le code de quelqu'un d'autre, gros, ancien,
mal documenté, plein de décisions que personne ne t'explique. C'est ce qui t'attend dans 90%
des emplois réels : tu n'arrives presque jamais sur un projet vierge, tu arrives sur un
existant de 200 000 lignes avec trois générations de développeurs qui ont laissé des traces
contradictoires.

Savoir lire un code étranger vite et juste est une compétence séparée de savoir écrire du
code. Elle se muscle différemment : moins de syntaxe, plus d'enquête. Tu dois apprendre à
entrer dans un système sans tout lire, à repérer les points d'entrée qui comptent, et à
reconstruire les contraintes qui ont façonné des choix qui te semblent d'abord absurdes.

Durée estimée : 8 à 12 heures, dont au moins 5 heures passées dans un vrai dépôt open source
que tu n'as jamais ouvert avant ce niveau.

Prérequis : Niveaux 06 (Archi Lab) et 07 (API Dojo) terminés. Tu dois savoir reconnaître une
architecture en couches, un pattern de repository, une frontière de service avant de pouvoir
les repérer dans le code de quelqu'un d'autre. Sans ce bagage, tu lis des lignes sans voir
la forme derrière.

## Ce que tu sais faire à la sortie

- Tu sais entrer dans un dépôt inconnu de plusieurs dizaines de milliers de lignes et en
  produire une carte mentale utilisable en moins de trois heures.
- Tu sais distinguer le point d'entrée d'exécution du point d'entrée de compréhension, et tu
  sais pourquoi ce ne sont presque jamais les mêmes fichiers.
- Tu sais reconstruire, à partir du code seul, les contraintes (produit, historique, équipe,
  performance) qui expliquent un design qui semble d'abord une erreur.
- Tu sais repérer une dette technique délibérée d'une dette technique subie, et pourquoi la
  distinction change ta façon d'en parler à l'équipe en place.
- Tu as pratiqué la méthode sur trois cas différents (réservation, back-office logistique,
  suivi de capteurs) et tu sais l'adapter à un domaine que tu ne connais pas encore.

## Structure du niveau

```text
11-BIG-APP-SNOOP/
├── 01-why-this-level.md            → pourquoi lire du code est une compétence à part
├── 02-read-a-codebase.md            → méthode d'entrée dans un gros code en 3 heures
├── 03-reverse-engineer-decisions.md → deviner les contraintes derrière un design
├── 04-case-studies.md               → 3 études de cas guidées
├── challenge.md                      → exercice appliqué sur un vrai dépôt open source
├── boss-fight.md                     → situation adverse + grille d'évaluation
└── grimoire.md                       → mémo dense
```

## Comment lire ce niveau

`02-read-a-codebase.md` te donne la méthode générale, applicable à n'importe quel dépôt.
`03-reverse-engineer-decisions.md` t'apprend à interpréter ce que tu as trouvé, pas juste à
le cartographier. `04-case-studies.md` déroule la méthode sur trois domaines différents pour
que tu voies qu'elle tient face à des contextes métier très éloignés. Le `challenge.md` te
demande de refaire l'exercice seul, sur un dépôt que personne ne t'a préparé.

## Ce qui ne se passe pas ici

Tu n'écris presque pas de code neuf dans ce niveau. L'objectif est la lecture, l'enquête et
la restitution écrite de ce que tu as compris : pas la contribution. Si tu veux contribuer
pour de vrai à un projet open source, c'est une suite naturelle une fois ce niveau terminé,
pas un objectif de ce niveau.
