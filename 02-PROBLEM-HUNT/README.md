# Niveau 02 — Problem Hunt

## Ce que c'est

Ce niveau apprend à chasser le vrai problème avant de chasser la solution. La compétence
n'est pas "coder vite", c'est "coder la bonne chose". Un développeur qui livre une feature
parfaitement construite mais qui répond à une mauvaise demande a produit de la dette, pas
de la valeur — et il l'a fait avec zéro bug, ce qui rend l'erreur encore plus difficile à
détecter en revue de code.

Ici tu apprends à démonter une demande, à repérer les signaux faibles qui trahissent un besoin
mal formulé, à distinguer les utilisateurs qui comptent de ceux qu'on invente pour se rassurer,
à écrire des non-objectifs qui protègent l'architecture, et à définir un seuil d'échec avant
de coder — pas après avoir livré, quand tout le monde est déjà en mode justification.

Durée estimée : 6 à 9 heures, réparties sur plusieurs jours (les interviews et l'analyse de
signaux faibles demandent du recul, pas de la vitesse).

Prérequis : Niveau 00 (Prologue) et Niveau 01 (Mindset) terminés. Tu dois déjà savoir qu'écrire
du code n'est pas une fin en soi.

## Ce que tu sais faire à la sortie

- Tu sais transformer une phrase du type "il nous faudrait un tableau de bord" en une liste
  de besoins réels, avec les contraintes qui les entourent.
- Tu sais mener une interview qui fait émerger des faits et des comportements passés,
  pas des opinions et des promesses futures.
- Tu sais repérer un signal faible (contournement, tableur fantôme, ticket qui revient) avant
  qu'il devienne un incendie.
- Tu sais écrire des "jobs to be done" et distinguer un persona utile d'un persona décoratif
  qui ne sert qu'à décorer un slide.
- Tu sais rédiger une section "non-objectifs" qui a un effet réel sur les choix d'architecture,
  pas juste une liste de vœux pieux.
- Tu sais définir une métrique de succès produit (pas une métrique de vanité) et un seuil
  d'échec explicite, écrits avant la première ligne de code.

## Structure du niveau

```text
02-PROBLEM-HUNT/
├── 01-why-this-level.md       → ce qui casse quand on saute la chasse au problème
├── 02-find-the-real-need.md   → demande vs besoin vs contrainte, interviews, signaux faibles
├── 03-users-and-jobs.md       → jobs to be done, personas utiles vs décoratifs
├── 04-scope-and-non-goals.md  → écrire les non-objectifs, effet sur l'architecture
├── 05-success-metrics.md      → métrique produit vs vanité, seuil d'échec défini d'avance
├── challenge.md                → exercice appliqué, livrable, critères mesurables
├── boss-fight.md                → situation adverse + grille d'évaluation
└── grimoire.md                  → mémo dense
```

## Comment lire ce niveau

Dans l'ordre, sans sauter `01-why-this-level.md`. Les leçons 02 à 05 forment une chaîne :
tu ne peux pas écrire de bons non-objectifs (04) si tu n'as pas identifié le vrai besoin (02)
ni les bons utilisateurs (03). Le `challenge.md` te demande d'appliquer les quatre leçons sur
un cas réel, pas un cas jouet. Le `boss-fight.md` te met dans une situation où quelqu'un te
pousse à sauter cette étape — c'est le vrai test.

## Ce qui ne se passe pas ici

Pas d'architecture, pas de découpage technique. Ce niveau se termine quand tu as un document
de cadrage solide, pas un schéma de base de données. Le découpage en tranches livrables,
c'est le Niveau 03 (MVP Split), qui suppose que ce niveau-ci est acquis.
