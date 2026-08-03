[Sommaire](../CURRICULUM.md) | [Niveau precedent](../10-.../README.md) | [Niveau suivant](../12-.../README.md)

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
entrer dans un système sans tout lire, à repérer les points d'entrée qui comptent, à
reconstruire les contraintes qui ont façonné des choix qui te semblent d'abord absurdes, et
enfin à y toucher sans rien casser.

Ce niveau réutilise : niveau [06 (Archi Lab)](../06-.../README.md) pour reconnaître une
architecture en couches et une frontière de service, et niveau [07 (API Dojo)](../07-.../README.md)
pour reconnaître un pattern de repository et un contrat d'appel entre services.

Auto-test d'entrée :

1. Sais-tu expliquer la différence entre le point d'entrée d'exécution d'une requête et son
   point d'entrée de compréhension pour un lecteur qui découvre le système ?
2. Sais-tu citer deux façons de repérer un fichier à fort risque dans un dépôt inconnu, sans
   avoir lu sa logique métier ?
3. Sais-tu ce qu'est un test de caractérisation et pourquoi il diffère d'un test qui vérifie
   qu'un comportement est correct ?

Si tu rates une de ces trois questions, remonte au niveau précédent avant de continuer.

Temps de lecture : environ 70 minutes pour les six leçons et les études de cas.

Durée estimée : 9 à 13 heures, dont au moins 5 heures passées dans un vrai dépôt open source
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
- Tu sais établir le rayon d'impact d'une modification (appelants, tests, données) avant de
  toucher au code, et livrer un patch minimal accompagné d'une preuve de non-régression.
- Tu as pratiqué la méthode sur trois cas différents (réservation, back-office logistique,
  suivi de capteurs) et tu sais l'adapter à un domaine que tu ne connais pas encore.

## Structure du niveau

```text
11-BIG-APP-SNOOP/
+-- 01-why-this-level.md            -> pourquoi lire du code est une competence a part
+-- 02-read-a-codebase.md           -> methode d'entree dans un gros code en 3 heures
+-- 03-reverse-engineer-decisions.md -> deviner les contraintes derriere un design
+-- 04-case-studies.md              -> 3 etudes de cas guidees
+-- 05-modifier-sans-casser.md      -> rayon d'impact, patch minimal, non-regression
+-- challenge.md                    -> carte puis patch sur un vrai depot open source
+-- boss-fight.md                   -> archeologie de code + grille d'evaluation chiffree
\-- grimoire.md                     -> memo dense au format 4 colonnes
```

Fichiers du niveau : [01-why-this-level.md](01-why-this-level.md),
[02-read-a-codebase.md](02-read-a-codebase.md),
[03-reverse-engineer-decisions.md](03-reverse-engineer-decisions.md),
[04-case-studies.md](04-case-studies.md),
[05-modifier-sans-casser.md](05-modifier-sans-casser.md), [challenge.md](challenge.md),
[boss-fight.md](boss-fight.md), [grimoire.md](grimoire.md).

## Comment lire ce niveau

`02-read-a-codebase.md` te donne la méthode générale, applicable à n'importe quel dépôt.
`03-reverse-engineer-decisions.md` t'apprend à interpréter ce que tu as trouvé, pas juste à
le cartographier. `04-case-studies.md` déroule la méthode sur trois domaines différents pour
que tu voies qu'elle tient face à des contextes métier très éloignés.
[05-modifier-sans-casser.md](05-modifier-sans-casser.md) referme la boucle : comprendre ne
suffit pas, il faut savoir modifier sans provoquer de régression cachée. Le
[challenge.md](challenge.md) te demande de refaire l'exercice complet, seul, sur un dépôt
que personne ne t'a préparé, jusqu'à un vrai patch livré.

## Ce qui ne se passe pas ici

Ce niveau ne t'apprend pas à écrire une architecture depuis zéro : c'est le rôle des niveaux
06 et 07. Ici, l'objectif est la lecture, l'enquête et la modification prudente d'un système
existant : pas la conception d'un système neuf. Si tu veux contribuer pour de vrai à un
projet open source au-delà d'un petit patch d'entraînement, c'est une suite naturelle une
fois ce niveau terminé, pas un objectif de ce niveau.
