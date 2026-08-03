# Niveau 06 — Archi-Lab

## Ce que c'est

Tu sais découper un projet en tickets, écrire du code qui marche, livrer un MVP. Ce niveau
s'attaque à autre chose : la forme que prend ton code quand il grossit. Pas la syntaxe, pas le
framework — la façon dont les morceaux se parlent entre eux, et ce qui se passe quand cette
façon est mauvaise. Une architecture n'est pas un schéma qu'on dessine avant de coder. C'est
la conséquence de mille petites décisions de couplage prises sans y penser. Ce niveau te
donne le vocabulaire et les réflexes pour prendre ces décisions consciemment.

Durée estimée : 8 à 12 heures, réparties sur plusieurs jours. C'est un niveau qui se digère mal
d'un coup — laisse les idées infuser entre deux leçons.

Prérequis : avoir livré au moins un projet qui dépasse le stade du prototype (Niveau 03,
MVP-Split, complété). Tu dois avoir déjà senti la douleur d'un fichier de 800 lignes qui fait
tout, sinon ces leçons resteront abstraites.

## Ce que tu sais faire à la sortie

- Tu sais repérer un couplage fort à l'œil, dans du code que tu n'as jamais vu.
- Tu sais expliquer pourquoi "tout dans un seul fichier" et "un microservice par fonction"
  sont les deux faces de la même erreur de jugement.
- Tu sais dessiner les couches d'une application (UI, cas d'usage, domaine, infra) et dire
  quelle couche a le droit de connaître quelle autre.
- Tu sais nommer la source de vérité d'une donnée dans un système avec cache, et expliquer
  ce qui se passe si elle divergeait pendant 30 secondes.
- Tu sais argumenter un choix entre monolithe modulaire et services, avec des coûts réels,
  pas des slogans.

## Structure du niveau

```text
06-ARCHI-LAB/
├── 01-why-this-level.md         → ce qui casse sans architecture pensée
├── 02-boundaries-and-coupling.md → couplage, cohésion, modules, dépendances dirigées
├── 03-layers-and-flow.md         → UI / cas d'usage / domaine / infra, inversion de dépendance
├── 04-state-and-truth.md         → source de vérité, cache, duplication, cohérence éventuelle
├── 05-choosing-architecture.md   → monolithe modulaire vs services : critères, coûts réels
├── challenge.md                  → exercice appliqué et livrable noté
├── boss-fight.md                 → situation adverse réaliste + grille d'évaluation
└── grimoire.md                   → mémo dense
```

## Comment lire ce niveau

Dans l'ordre, sans sauter. `02` et `03` sont le cœur théorique : tout le reste s'appuie dessus.
`04` est le piège le plus sournois de ce niveau — la plupart des bugs de production que tu
verras dans ta carrière viennent de là. `05` referme la boucle en te donnant un cadre de
décision, pas une religion ("microservices for ever" ou "monolithe for ever" sont deux sectes
à fuir).

## Ce qui ne se passe pas ici

Ce niveau ne t'apprend pas un framework d'architecture précis (Clean Architecture, Hexagonal,
DDD tactique...) au sens catalogue. Il t'apprend les forces sous-jacentes que tous ces
frameworks essaient de dompter. Une fois ces forces comprises, n'importe quel nom de pattern
que tu croiseras plus tard se lira en cinq minutes au lieu de te sembler être une nouvelle
religion à apprendre par cœur.
