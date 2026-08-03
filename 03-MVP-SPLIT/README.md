# Niveau 03 : MVP Split

## Ce que c'est

Ce niveau apprend à découper un problème déjà bien compris (Niveau 02 fait) en tranches
livrables qui ont de la valeur individuellement. La compétence n'est pas "faire un MVP",
tout le monde connaît le mot. La compétence est de savoir couper une feature sans la casser,
d'estimer sans mentir, et de savoir dire non à un ajout de périmètre sans passer pour quelqu'un
qui ne veut pas travailler.

Ici tu apprends la différence entre une tranche verticale (qui traverse toute la stack et
livre un bout de valeur complet) et une couche horizontale (qui prépare l'infrastructure mais
ne livre rien d'utilisable seule). Tu apprends où placer les lignes de coupe dans une feature
complexe, comment estimer avec de l'incertitude assumée plutôt que cachée, et comment arbitrer
entre dire oui, dire non, et dire "pas maintenant, et voici pourquoi".

Durée estimée : 6 à 9 heures, avec au moins un exercice de découpage sur un vrai cahier des
charges (le tien ou celui fourni dans `challenge.md`).

Prérequis : Niveau 02 (Problem Hunt) terminé. Tu dois arriver ici avec un besoin réel identifié,
des utilisateurs qui comptent, des non-objectifs écrits et une métrique de succès définie.
Sans ça, découper un problème mal posé ne fait que produire des tranches inutiles plus vite.

## Ce que tu sais faire à la sortie

- Tu sais distinguer une tranche verticale d'une couche horizontale et tu sais pourquoi
  livrer par couches retarde le retour terrain de plusieurs semaines.
- Tu sais identifier où couper une feature complexe sans casser son utilité, et tu sais
  utiliser un feature flag pour livrer du code incomplet en sécurité.
- Tu sais estimer une tâche en assumant l'incertitude au lieu de la maquiller derrière un
  chiffre unique, et tu sais repérer l'effet tunnel avant qu'il ne te morde.
- Tu sais dire non à une demande de périmètre supplémentaire avec un argument de coût
  d'opportunité, pas avec de la mauvaise volonté.
- Tu sais assumer une dette technique consciemment plutôt que la découvrir en production.

## Structure du niveau

```text
03-MVP-SPLIT/
├── 01-why-this-level.md         → ce qui casse quand on découpe mal ou pas du tout
├── 02-slicing-value.md           → tranches verticales vs couches horizontales
├── 03-cut-lines.md               → où couper une feature sans la casser, feature flags
├── 04-estimating-honestly.md     → incertitude, découpage, budget de temps, effet tunnel
├── 05-when-to-say-no.md          → arbitrage, coût d'opportunité, dette assumée
├── challenge.md                   → exercice appliqué, livrable, critères mesurables
├── boss-fight.md                   → situation adverse + grille d'évaluation
└── grimoire.md                     → mémo dense
```

## Comment lire ce niveau

Dans l'ordre. `02-slicing-value.md` pose le principe (verticale vs horizontale),
`03-cut-lines.md` l'applique à une feature concrète, `04-estimating-honestly.md` t'apprend
à chiffrer ce découpage sans te mentir, et `05-when-to-say-no.md` te donne les mots pour
défendre ce découpage face à quelqu'un qui veut tout, tout de suite. Le `boss-fight.md`
simule exactement cette pression.

## Ce qui ne se passe pas ici

Pas de code de production détaillé, pas de choix de stack technique. Ce niveau produit un plan
de livraison en tranches avec des estimations honnêtes, pas une implémentation. L'exécution
technique du découpage arrive dans les niveaux suivants.
