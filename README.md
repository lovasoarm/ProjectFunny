# ProjectFunny : Le curriculum qui fabrique des cerveaux d'ingénieurs

Ce n'est pas un cours. C'est un parcours d'entraînement.

Un cours te donne des notions. Ici, on te met en situation, on casse ton plan, on te demande de
défendre tes choix, et on te fait livrer. À la sortie, tu ne "connais" pas l'ingénierie logicielle :
tu sais conduire un projet réel du besoin flou jusqu'à la mise en production, et expliquer pourquoi
chaque décision a été prise.

## À qui ça s'adresse

- Tu sais déjà coder un peu (une boucle, une fonction, une requête HTTP ne te font pas peur).
- Tu bloques dès que le projet devient réel : trop d'options, trop de flou, tout casse.
- Tu veux comprendre ce que font les seniors dans leur tête, pas recopier leurs recettes.

Si tu cherches un tutoriel "framework X en 10 minutes", ce dépôt n'est pas pour toi.

## Le principe

```text
   BESOIN FLOU
       |
       v
  [ 02 PROBLEM-HUNT ]  ->  tu sais ce qu'il faut construire, et surtout ce qu'il ne faut pas
       |
       v
  [ 03 MVP-SPLIT ]     ->  tu sais découper en tranches livrables
       |
       v
  [ 04..07 CONSTRUCTION ] -> UI, données, architecture, API : tu sais où sont les frontières
       |
       v
  [ 08..10 CONDUITE ]  ->  roadmap, qualité, équipe : ça tient dans le temps
       |
       v
  [ 11..12 EPREUVE ]   ->  gros code inconnu, puis projet complet noté
       |
       v
    INGENIEUR
```

## Règles du jeu

1. **Tu livres.** Chaque niveau se termine par un artefact : un document, un schéma, du code.
2. **Tu défends.** Chaque leçon finit par trois questions. Si tu ne sais pas répondre à l'oral, tu
   n'as pas fini la leçon.
3. **Tu mesures.** Une décision sans critère de succès est une opinion.
4. **Tu jettes.** Le premier découpage est faux. Le refaire fait partie du travail.
5. **Tu écris la décision, pas seulement le code.** Le code dit le "comment". Ton texte dit le "pourquoi".

## Structure du dépôt

```text
ProjectFunny/
  README.md              ce fichier
  CURRICULUM.md          détail des 16 niveaux, objectifs et durées
  ROADMAP.md             trois rythmes de parcours (sprint, saison, marathon)
  EPILOGUE.md            ce qui vient après le dernier niveau
  ANNEXE-perennite.md    comment ce curriculum reste valable quand les outils changent
  ANNEXE-et-apres.md     spécialisations possibles et signaux de progression
  CONTRIBUTING.md        comment améliorer ce curriculum
  LICENSE
  00-PROLOGUE/ ... 15-BONUS-VAULT/
```

Chaque niveau contient : `README.md` (la carte), `01-why-this-level.md` (pourquoi il existe et ce
qui casse sans lui), des leçons numérotées, `challenge.md` (livrable), `boss-fight.md` (situation
adverse), `grimoire.md` (mémo dense).

## Comment démarrer

1. Lis `00-PROLOGUE/README.md`.
2. Choisis un projet fil rouge **réel** (pas une todo-list) : un besoin que tu as vu de tes yeux.
3. Fais chaque niveau sur ce projet. Le curriculum n'a de valeur que collé à un cas concret.

## Durée honnête

- Parcours dense : 8 semaines à 12 h/semaine.
- Parcours réaliste avec un travail à côté : 5 à 6 mois à 4 h/semaine.
- Le capstone (niveau 12) demande 25 à 40 h à lui seul.
