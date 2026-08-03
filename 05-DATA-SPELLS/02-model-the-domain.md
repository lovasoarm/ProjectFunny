# Modéliser le réel : entités, relations, invariants

## La scène

Le club d'escalade prête du matériel à ses adhérents : baudriers, chaussons, casques. Le
bénévole qui a codé la première version du fichier de prêt a créé une table `Pret` avec les
colonnes `nom_adherent`, `materiel`, `date_emprunt`. Ça a marché six mois. Puis deux adhérents
se sont appelés "Julien Petit" à un mois d'écart et le bénévole a rendu le mauvais baudrier au
mauvais Julien. Puis un adhérent a emprunté deux baudriers le même jour et la ligne unique n'a
pu en représenter qu'un : le second a été noté "à la main" sur un carnet, introuvable trois
semaines plus tard quand le matériel a été signalé manquant à l'inventaire annuel.

Le bug n'est pas dans le code de prêt. Il est dans la modélisation initiale : "adhérent" a été
représenté par une chaîne de caractères libre au lieu d'une entité avec une identité stable, et
"un emprunt" a été pensé comme une ligne au lieu d'une relation qui peut se répéter. Personne
n'a écrit noir sur blanc, avant la première ligne de SQL, ce qu'est réellement un adhérent, ce
qu'est réellement un emprunt, et ce qui doit rester vrai en toutes circonstances.

## Ce qui se passe vraiment

### Une entité, ce n'est pas une table : c'est une identité qui dure

Une entité du domaine existe indépendamment de la façon dont tu la stockes. Le test pour
savoir si quelque chose mérite d'être une entité (avec sa propre identité, sa propre ligne, sa
propre clé) plutôt qu'un simple attribut : **est-ce que deux occurrences qui se ressemblent en
apparence doivent rester distinctes, et est-ce que cette chose peut changer d'attribut sans
perdre son identité ?**

Un adhérent reste le même adhérent s'il change de nom (mariage), d'adresse, de numéro de
téléphone. Un baudrier reste le même baudrier physique tout au long de sa vie, même après
plusieurs prêts et plusieurs contrôles de sécurité. Ce sont des entités : elles ont besoin d'un
identifiant stable, indépendant de tout attribut descriptif.

À l'inverse, une couleur de baudrier, une taille de chausson, ne sont pas des entités : ce sont
des attributs ou, au pire, des valeurs d'un petit référentiel fermé (une "value list").
Le piège classique : promouvoir en entité à part entière quelque chose qui n'en est pas une,
ce qui ajoute une jointure et une table à maintenir pour rien (sur-ingénierie), ou l'inverse,
garder en simple attribut quelque chose qui aurait dû être une entité, ce qui empêche de
représenter la réalité (sous-modélisation, le bug du club d'escalade).

### Clé naturelle vs clé technique

Une clé naturelle est une donnée du monde réel censée identifier une ligne de façon unique : un
email, un numéro de licence FFME, un numéro de série de matériel gravé par le fabricant. Une clé
technique (ou "clé de substitution") est un identifiant inventé par le système, sans
signification métier : un UUID, un entier auto-incrémenté.

```text
Clé naturelle (numéro de licence FFME)     Clé technique (UUID généré par le système)
─────────────────────────────────────     ──────────────────────────────────────────
+ Lisible, vérifiable par un humain         + Ne change jamais, quoi qu'il arrive au métier
+ Pas de jointure "juste pour un id"        + Stable même si la règle métier change
- Peut changer (fusion de fédérations,      - Ne veut rien dire sans jointure
  erreur de saisie corrigée, ré-émission)   - Une ligne orpheline est illisible en debug
- Peut être NULL (adhérent pas encore
  licencié) → casse la contrainte PK
```

Règle pratique : **une clé technique par défaut, une clé naturelle en contrainte d'unicité en
plus si elle existe et qu'elle est stable.** Le numéro de licence FFME devient une colonne
`UNIQUE`, pas la clé primaire : le jour où la fédération change son format de numérotation
(c'est arrivé), tu changes une valeur dans une colonne, pas l'identifiant de toutes les lignes
liées dans toutes les tables étrangères de la base.

### Relations et cardinalités : dessiner avant de coder

Trois questions à te poser pour chaque paire d'entités liées :

1. Un `A` peut-il être lié à plusieurs `B`, et inversement ?
2. La relation porte-t-elle elle-même des attributs (une date, une quantité, un statut) ?
3. La relation peut-elle exister plusieurs fois entre les deux mêmes entités, à des moments
   différents ?

```text
Adhérent ──────< Emprunt >────── Matériel
   1                                 1
        (un emprunt relie exactement un adhérent et un article de matériel,
         à un instant donné : la relation ELLE-MÊME porte une date de début,
         une date de retour prévue, une date de retour réelle)

Cardinalité réelle : un adhérent a PLUSIEURS emprunts dans le temps (historique).
Un article de matériel a PLUSIEURS emprunts dans le temps (historique).
Mais à un instant T, un article de matériel ne peut être emprunté que par un seul adhérent
à la fois (INVARIANT : voir plus bas).
```

Le piège du bénévole du club d'escalade : il a modélisé `Pret` comme s'il ne pouvait exister
qu'un emprunt actif à la fois, tout court (une ligne = un état), sans distinguer "l'historique
de tous les emprunts" (qui doit s'accumuler, jamais s'écraser) de "l'emprunt actif en cours"
(qui est une question qu'on pose à l'historique, pas une donnée séparée à maintenir à la main).

### Invariants : ce qui doit rester vrai, point final

Un invariant est une règle qui doit être vraie à tout instant, quel que soit le chemin
emprunté par le code pour arriver là. La différence avec une simple validation de formulaire :
un invariant doit tenir même si deux processus concurrents écrivent en même temps, même si
quelqu'un modifie la base directement en SQL, même dans six mois quand un stagiaire écrira une
nouvelle route API sans relire l'ancien code.

Invariants du club d'escalade, formulés explicitement (ce qui n'avait jamais été fait) :

```text
INV-1 : un article de matériel ne peut avoir qu'un emprunt "actif" (sans date de retour) à
        la fois : impossible de le prêter deux fois en même temps.
INV-2 : la date de retour réelle, si elle existe, est postérieure ou égale à la date d'emprunt.
INV-3 : un adhérent dont l'adhésion a expiré ne peut pas démarrer un nouvel emprunt (mais garde
        le droit de rendre ce qu'il a déjà emprunté).
INV-4 : un article de matériel retiré de l'inventaire (perdu, détruit) ne peut plus être prêté,
        mais son historique de prêts passés reste consultable.
```

Un invariant qui n'est écrit nulle part n'existe pas pour le code qui vient après toi. Il doit
se traduire en contrainte de base de données quand c'est possible (INV-1 et INV-2 peuvent
devenir des contraintes SQL, voir la leçon suivante), et en règle de code applicatif explicite
et testée quand la base ne peut pas l'exprimer nativement (INV-3, INV-4, qui dépendent d'une
logique métier plus riche qu'une simple contrainte de colonne).

### Ce qu'on refuse sciemment de modéliser

Modéliser tout ce qui pourrait un jour être utile est un piège symétrique du sous-modélisation :
chaque entité et chaque relation ajoutée est un coût permanent (jointures, migrations,
invariants à maintenir), payé même si la fonctionnalité correspondante n'existe jamais.

```text
Refusé pour le club d'escalade, et pourquoi :

- "Catégorie de niveau d'escalade de l'adhérent" comme entité séparée avec sa propre table
  de progression → aucune fonctionnalité actuelle n'en a besoin, un simple enum suffirait
  SI le besoin apparaissait ; ne pas construire l'infrastructure d'une feature qui n'existe
  pas encore ("YAGNI" appliqué à la donnée, pas seulement au code).

- "Fournisseur du matériel" comme entité liée à chaque article → vrai besoin métier absent
  aujourd'hui (le club ne renégocie pas de contrat fournisseur par article), ajouté seulement
  le jour où quelqu'un doit vraiment comparer des fournisseurs.
```

Le critère de décision n'est pas "est-ce que ça pourrait servir" (presque tout pourrait servir
un jour) mais "est-ce qu'une décision ou une règle métier actuelle en dépend aujourd'hui". Une
entité ajoutée sans besoin réel actuel est une dette qu'on paie tout de suite (complexité) pour
un bénéfice hypothétique qu'on paiera peut-être jamais.

Analogie : Analogie : modéliser un domaine, c'est prendre les mesures et choisir les assemblages avant de couper le bois à l'atelier de menuiserie, et trier les patients selon leur état réel aux urgences.
Où l'analogie casse : le bois ne change pas de forme une fois scié, et un patient trié reste le
même individu. Un modèle de données doit encaisser des années de changements métier que
personne n'a anticipés au moment du plan.

## Modèle appliqué : refacturation d'énergie

Même exercice sur le second fil rouge du niveau, en condensé :

```text
Copropriete (1) ──< Logement (n)
Logement (1) ──< Occupation (n)          # historique : qui habite où, sur quelle période
Compteur (1) ──< Releve (n)              # historique de lecture, jamais écrasé
TarifEnergie (n) : période de validité   # jamais "le tarif", toujours "le tarif à telle date"

Invariants :
INV-1 : les périodes d'Occupation d'un même Logement ne se chevauchent jamais.
INV-2 : un Releve a toujours une valeur >= au Releve précédent du même Compteur (le compteur
        ne recule pas, sauf remplacement explicite tracé comme un événement séparé).
INV-3 : à toute date donnée, il existe exactement un TarifEnergie applicable, jamais zéro,
        jamais deux.
```

Remarque le motif qui revient : "un logement" n'est pas assez, il faut "qui occupe ce logement
et depuis quand" ; "le tarif" n'est pas assez, il faut "le tarif valable à telle date". C'est le
fil conducteur de tout ce niveau : modéliser sans le temps produit un modèle qui ment dès que la
réalité bouge.

## Compromis

| Option                                                                   | Coût                                                              | Bénéfice                                                                                                  | Quand choisir                                                                 |
| ------------------------------------------------------------------------ | ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| Clé technique partout + clé naturelle en `UNIQUE`                        | Une colonne et un index en plus                                   | Stabilité totale des relations même si la donnée métier change                                            | Par défaut, presque toujours                                                  |
| Clé naturelle comme clé primaire                                         | Simplicité apparente, pas de colonne supplémentaire               | Lisibilité immédiate en debug                                                                             | Référentiel vraiment figé à vie (code ISO pays, par exemple)                  |
| Modéliser une relation many-to-many avec table de jonction dès le départ | Une table de plus, une jointure de plus dans les requêtes simples | Peut porter des attributs propres à la relation (date, quantité, statut) et supporte le many-to-many réel | Dès que la relation peut se répéter dans le temps ou porter une donnée propre |
| Modéliser en 1-n en attendant d'avoir un vrai besoin de many-to-many     | Migration future nécessaire si le besoin apparaît                 | Simplicité tant que la réalité est vraiment 1-n                                                           | Cardinalité réelle du domaine vérifiée à 1-n, pas une supposition de confort  |

## Pièges classiques

- **La chaîne de caractères en guise d'identité.** Symptôme : deux enregistrements qui se
  ressemblent ("Julien Petit" et "Julien Petit") sont traités comme la même entité, ou
  inversement une faute de frappe casse silencieusement une jointure logique.
- **L'état courant sans historique.** Symptôme : une colonne `statut` ou `adresse` écrasée à
  chaque changement : impossible de répondre "qui habitait ce logement le 3 mars", la réponse
  n'existe plus.
- **L'invariant seulement dans la tête du développeur.** Symptôme : une règle métier appliquée
  dans un seul endroit du code (un formulaire) mais absente de la base ; une autre route API,
  écrite plus tard par quelqu'un d'autre, la viole sans le savoir.
- **La sur-modélisation anticipée.** Symptôme : des tables et des relations pour des besoins
  hypothétiques, jamais requêtées, qui compliquent chaque migration et chaque requête sans
  jamais servir à rien.
- **La cardinalité supposée au lieu de vérifiée.** Symptôme : un modèle "un adhérent, un
  emprunt actif" codé en dur, qui casse le jour où un adhérent emprunte deux articles à la fois
  : une situation pourtant normale et prévisible dès le premier jour si on avait posé la
  question.

## Ce que tu dois savoir défendre

- Donne un exemple où transformer un attribut en entité à part entière était la bonne décision,
  et un exemple où ça aurait été de la sur-ingénierie : explique le critère qui les distingue.
- Pourquoi préfère-t-on une clé technique à une clé naturelle dans la majorité des cas, alors
  que la clé naturelle semble plus simple à lire ?
- Prends un invariant du club d'escalade ou de la refacturation d'énergie et explique comment
  il pourrait être violé si on ne le traduit qu'en validation de formulaire, jamais en
  contrainte de base de données ni en règle de code testée.
