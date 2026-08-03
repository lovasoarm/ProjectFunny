# Changement de spec : la tarification différenciée par salle

## La scène

Ton jalon Architecture vient de passer le seuil de 12/20. Ton modèle de données est posé :
une table de créneaux, une table de réservations, un compteur de capacité par créneau. Le
client fictif (ou ton formateur) t'envoie ce message, une semaine après avoir validé ton
schéma de tarification unique :

> "En fait on a oublié de vous dire un truc important : les trois salles n'ont pas le même
> tarif. La salle du centre-ville est plus chère en soirée, celle de banlieue a un tarif
> réduit le matin, et on veut pouvoir créer des créneaux récurrents (tous les mardis à 18h,
> pendant toute la saison) plutôt que de recréer chaque créneau à la main chaque semaine.
> Ça change beaucoup de choses pour vous ?"

Ce n'est pas un caprice de client capricieux. C'est le scénario normal d'un projet réel :
une contrainte tombe après que tu as figé une structure, parce que le client ne savait pas
lui-même que cette contrainte existait avant de voir ton architecture prendre forme. Ta
compétence ici n'est pas d'éviter ce genre de changement, c'est de savoir en mesurer le coût
et d'en tirer une leçon sur ce qui aurait rendu ce coût plus faible.

## Ce qui se passe vraiment

Un changement de spec après l'architecture a toujours deux coûts distincts, et les confondre
te fait mal évaluer la situation :

1. **Le coût d'implémentation** : le temps réel pour ajouter la fonctionnalité demandée.
2. **Le coût de correction structurelle** : le temps perdu parce que ton architecture
   d'origine rendait ce type de changement plus coûteux qu'il n'aurait dû l'être.

Le premier coût est souvent incompressible : une tarification différenciée par salle et par
créneau demande du travail, peu importe l'architecture. Le second coût, lui, est un signal
direct sur la qualité de ta conception initiale : c'est celui qui doit t'alerter.

```text
Changement de spec arrive
   |
   v
Coût d'implémentation pur (fonctionnalité neuve, inévitable)
   |
   +--> Coût de correction structurelle (rendu nécessaire par un choix de conception
   |     antérieur qui n'anticipait pas ce type d'évolution)
   |
   v
Total mesuré en fichiers touchés + heures, décomposé en deux lignes, pas une seule
```

## Mesurer le coût du changement : la méthode

Ne te contente jamais d'une impression ("ça a pris du temps", "ça a été assez simple"). Une
mesure vérifiable a deux composantes :

### 1. Fichiers touchés

Avant de commencer le changement, note l'état du dépôt (`git status` propre, dernier commit
du jalon Architecture identifié). Une fois le changement terminé, compte :

```bash
git diff --stat <commit-avant-changement> HEAD
```

Classe chaque fichier touché dans l'une de ces trois catégories :

```text
Catégorie A : fichier de configuration ou de données (ex. table de tarifs ajoutée)
Catégorie B : fichier métier modifié pour accueillir la nouvelle règle
Catégorie C : fichier métier modifié qui n'avait aucune raison de connaître la tarification
              (ex. le module de réservation qui devait recalculer un prix alors qu'il ne
              gérait que la capacité)
```

La catégorie C est le signal le plus important : chaque fichier qui s'y trouve est une preuve
que ton architecture couplait des responsabilités qui auraient dû rester séparées. Un
changement de tarification qui oblige à modifier le module de comptage de capacité prouve que
la capacité et le prix n'étaient pas assez découplés dans ton modèle initial.

### 2. Heures

Compte séparément :

- Heures passées à comprendre la nouvelle contrainte (lecture, clarification, reformulation).
- Heures passées à modifier le modèle de données (nouvelle table, nouvelles relations).
- Heures passées à modifier la logique métier (calcul du tarif, application de la règle de
  récurrence).
- Heures passées à corriger des effets de bord dans du code qui n'avait pas de rapport
  fonctionnel direct avec la tarification, mais qui a dû changer quand même (c'est l'équivalent
  temporel de la catégorie C ci-dessus).

Un total honnête ressemble à ceci, pas à un chiffre unique invérifiable :

```text
Compréhension de la contrainte           : 1.5 h
Modèle de données (tarifs, récurrence)   : 3 h
Logique métier (calcul, application)     : 4 h
Effets de bord hors périmètre attendu    : 2.5 h   <- ligne à surveiller
--------------------------------------------------
Total                                     : 11 h
```

Si la ligne "effets de bord hors périmètre attendu" dépasse 20 % du total, c'est un signal
fort que l'architecture initiale n'isolait pas assez la responsabilité touchée par le
changement.

## Prouver qu'une architecture facilitait le changement

Beaucoup d'apprenants affirment "mon architecture était modulaire" sans preuve. Voici ce qui
constitue une preuve recevable, pas une affirmation :

- **Le diff de la catégorie C est vide ou quasi vide.** Si aucun fichier hors du domaine de
  la tarification n'a dû changer, c'est la preuve la plus directe que la capacité et le prix
  étaient effectivement découplés.
- **Le nouveau concept (tarif différencié, créneau récurrent) s'ajoute par extension, pas par
  modification.** Si tu as pu ajouter une nouvelle table et un nouveau module de calcul sans
  toucher aux fichiers existants du comptage de capacité, l'architecture supportait le
  changement. Si tu as dû réécrire des fonctions existantes pour qu'elles acceptent un
  paramètre de prix qu'elles n'avaient pas prévu, l'architecture ne le supportait pas.
- **Le temps de compréhension de la contrainte dépasse le temps de modification du code.**
  Contre-intuitif mais sain : si comprendre la règle de tarification a pris plus de temps que
  de l'implémenter, c'est que le code se laissait modifier facilement une fois la règle claire.
  L'inverse (implémentation plus longue que la compréhension) signale une résistance
  structurelle du code, pas seulement de la complexité métier.
- **Aucun test existant n'a dû être réécrit, seulement de nouveaux tests ajoutés.** Si les
  tests de comptage de capacité continuent de passer sans modification après l'ajout de la
  tarification, c'est la preuve que les deux responsabilités étaient bien séparées au niveau
  du comportement observable, pas seulement au niveau du code source.

## Ce que tu dois consigner

Dans ton `TDD_JOURNAL.md` (voir `03-deliverables.md`), ajoute une entrée datée avec :

1. Le tableau de fichiers touchés par catégorie (A, B, C) avec le nombre de fichiers dans
   chacune.
2. Le tableau d'heures décomposé comme ci-dessus.
3. Une phrase d'interprétation honnête : ton architecture a-t-elle facilité ce changement ou
   l'a-t-elle rendu plus coûteux qu'il n'aurait dû l'être, et pourquoi précisément (nomme le
   couplage exact, pas une impression générale de "code compliqué").

Cette entrée est réutilisée telle quelle par le critère "Coût du changement de spec" de
`04-evaluation-grid.md` : une entrée absente ou non chiffrée fait perdre ce critère
intégralement, une estimation vague sans le détail par catégorie le fait perdre à moitié.

## Compromis à assumer face au changement

| Option                                                        | Coût                                          | Bénéfice                                                       | Quand choisir                                                                 |
| --------------------------------------------------------------- | ------------------------------------------------ | ----------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Absorber le changement dans le modèle existant sans refonte    | Rapide à court terme                              | Livraison plus vite dans le délai annoncé au client               | Si le couplage révélé reste limité à un ou deux fichiers de catégorie C          |
| Refactorer le module de capacité pour bien séparer prix/capacité | Coût immédiat plus élevé, retarde la V1 possible | Les futurs changements de tarification ne recoûteront plus cela   | Si le diagnostic montre un couplage large (plusieurs fichiers de catégorie C)     |
| Documenter le couplage comme dette assumée, sans corriger maintenant | Coût nul à court terme                        | Livraison rapide, mais le prochain changement coûtera pareil ou plus | Si le délai client ne permet aucune des deux options précédentes cette itération |

## Pièges classiques

- **Estimer le coût "au feeling" sans compter les fichiers ni les heures.** Symptôme : tu ne
  peux pas répondre si on te demande combien de fichiers ont été touchés hors du périmètre
  attendu.
- **Confondre le coût d'implémentation avec le coût de correction structurelle.** Symptôme :
  tu dis "ça a pris du temps" sans distinguer ce qui était inévitable de ce qui révélait un
  défaut de conception.
- **Refactorer en silence sans le documenter comme un coût du changement.** Symptôme : ton
  TDD_JOURNAL.md ne mentionne jamais ce changement de spec alors qu'il a occupé plusieurs
  heures du jalon 3.
- **Traiter chaque changement de spec comme la preuve que l'architecture était mauvaise.**
  Symptôme : tu conclus systématiquement à un échec architectural, alors qu'un peu de coût
  d'adaptation est normal et attendu, même dans une bonne architecture.

## Ce que tu dois savoir défendre

- Pourquoi mesurer un changement de spec en une seule estimation globale ("ça a pris deux
  jours") ne prouve rien sur la qualité de ton architecture.
- Ce que signifie précisément un fichier de catégorie C, et pourquoi son absence est une
  preuve plus forte qu'une déclaration d'intention ("j'ai voulu faire du découplage").
- Comment tu aurais pu anticiper ce changement de spec dès le jalon Cadrage, sans pour autant
  sur-concevoir une architecture pour des besoins hypothétiques qui ne seraient jamais
  arrivés.

## Lien avec la grille d'évaluation

Ce document alimente directement deux endroits de `04-evaluation-grid.md` :

```text
Critère "Coût du changement de spec"   --> exige le tableau fichiers + heures ci-dessus
Seuil 90-100 (excellence)              --> exige une interprétation honnête du couplage,
                                            pas seulement le tableau chiffré
```

Un apprenant qui produit le tableau chiffré mais ne conclut jamais sur ce que ça révèle de
son architecture reste bloqué sous le seuil d'excellence, même avec un score correct ailleurs
sur la grille : parce que la mesure seule ne vaut rien sans l'interprétation qui en découle.

## Ce que ce changement dit du client, pas seulement de ton code

Un client qui découvre une contrainte de tarification après avoir vu ton schéma n'a pas menti
au jalon Cadrage : il ne savait pas que cette contrainte était structurante avant de voir une
architecture concrète face à laquelle réagir. C'est exactement le mécanisme décrit dans
`01-why-this-level.md` : un brief ambigu se réduit par itération, pas par un questionnaire
initial parfait. Le changement de spec n'est donc pas un échec de ton cadrage, c'est la
preuve que ton cadrage a fait son travail : produire quelque chose d'assez concret pour faire
émerger une contrainte que personne n'avait formulée avant.

Défends cette idée à l'oral si on te demande pourquoi tu n'as pas "tout prévu" dès le départ :
prévoir l'imprévisible n'est pas la compétence testée ici, réagir avec une mesure chiffrée et
une architecture qui limite les dégâts en est une.
