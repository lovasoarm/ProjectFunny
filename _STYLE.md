# STYLE.md : référentiel de forme du curriculum "ProjectFunny"

Ce fichier est le référentiel unique. Aucun autre document ne peut assouplir ces règles.
Toute contribution qui ne respecte pas ce fichier est refusée en revue, sans discussion de goût.

Version du référentiel : 2.0. Dernière révision : 2026-08-03. Prochaine révision due : 2027-02-03.

## Objectif

Former de vrais cerveaux d'ingénieurs : comprendre ce qu'on fait, pourquoi, ce qui casse,
et comment décider quand le projet devient réel. Pas de cours scolaire, pas de liste de notions.

## Ton

- Direct, vivant, un peu jeu vidéo (niveaux, quêtes, boss fight, grimoire), mais jamais puéril.
- Français. Phrases courtes. Zéro remplissage, zéro "il est important de noter que".
- On parle au lecteur : "tu".
- Refuser le contenu tiède : chaque page doit apprendre quelque chose de non évident.

## Règles typographiques (contrôlables automatiquement)

1. **Em-dash interdit.** Le caractère U+2014 ne doit apparaître nulle part. Utilise deux points,
   une virgule, une parenthèse ou un point. En-dash U+2013 interdit également.
2. **Flèches ASCII uniquement.** Écris `-->` et `<--`. Les caractères Unicode de flèche
   (U+2192, U+2190, U+21D2, U+2194, et tous leurs voisins) sont interdits, y compris dans les
   blocs ```text et dans les tableaux.
3. **Pas de caractère de dessin de boîte** (`├`, `│`, `└`, `─`) dans les arborescences :
   utilise `|`, `+--`, `\--` en ASCII pur.
4. **Emojis interdits.**
5. Blocs de code toujours typés : ```text, ```sql, ```ts, ```bash.

Contrôle avant toute contribution :

```bash
grep -rn $'\u2014\|\u2013\|\u2192\|\u2190\|\u21D2\|\u2194' --include='*.md' . && echo "REFUSE" || echo "OK"
```

## Analogies : univers autorisés et règle de rupture

Une analogie est un outil de compréhension, pas une décoration. Trois règles :

1. **Univers autorisés, et rien d'autre** :
   - la cuisine d'un restaurant en service,
   - l'atelier de menuiserie,
   - le service des urgences d'un hôpital,
   - la régie technique d'un spectacle,
   - la navigation maritime,
   - la course en montagne (alpinisme, refuge, cordée).
2. **Exactement 2 analogies par ligne d'analogie** : une ligne d'analogie compare la notion à
   deux univers différents de la liste, jamais un seul (trop facile à prendre au pied de la
   lettre), jamais trois (bavardage).
3. **Ligne "Où l'analogie casse" obligatoire**, immédiatement sous l'analogie, qui dit
   précisément ce que la comparaison fait croire de faux.

Gabarit imposé :

```text
Analogie : une migration de schéma, c'est le passage de commande en cuisine pendant le
service, et le changement de voile en pleine mer.
Où l'analogie casse : en cuisine et en mer, on peut interrompre une seconde. Une migration
tourne sur des données vivantes que personne n'a mises en pause, et un rollback n'annule
pas ce que les clients ont déjà lu.
```

**Obligation de couverture** : chaque leçon des niveaux 05 à 09 (le noyau technique) contient
au moins une analogie conforme, avec sa ligne de rupture.

## Structure d'une leçon (fichier `NN-slug.md`)

1. `# Titre` court et parlant.
2. **Le piège** ou **La scène** : 3-6 lignes de situation concrète et crédible (jamais un exemple
   cliché type "todo app" ou "blog" ; préférer : gestion de créneaux d'un cabinet vétérinaire,
   suivi de tournées de livraison, bibliothèque d'un club d'escalade, refacturation d'énergie).
3. **Ce qui se passe vraiment** : le modèle mental, expliqué en profondeur.
4. Au moins un bloc ASCII (diagramme, flux, arbre de décision) dans un bloc ```text.
5. Du code réel et minimal quand ça éclaire (TypeScript / SQL / pseudo-code), commenté.
6. **Compromis** : tableau ou liste "Option A / Option B --> coût, bénéfice, quand choisir".
7. **Pièges classiques** : 3-5 puces, chacune avec le symptôme observable.
8. **Ce que tu dois savoir défendre** : 3 questions auxquelles le lecteur doit pouvoir répondre à l'oral.

## Structure d'un niveau (dossier)

- `README.md` : carte du niveau, durée estimée, prérequis, ce que tu sais faire à la sortie,
  plus les trois lignes standard obligatoires :
  - `Ce niveau réutilise :` (niveaux amont et notion précise réutilisée, avec liens relatifs)
  - `Auto-test d'entrée :` exactement 3 questions ; si tu en rates une, tu remontes d'un niveau
  - `Temps de lecture :` minutes de lecture seule, distinct de la durée totale du niveau
- `01-why-this-level.md` : pourquoi ce niveau existe, ce qui casse sans lui.
- leçons numérotées.
- `challenge.md` : exercice appliqué, livrable, critères de réussite mesurables.
- `boss-fight.md` : situation adverse réaliste + grille d'évaluation + **seuil de validation
  chiffré** (points par critère, total, seuil de passage explicite).
- `grimoire.md` : au format imposé ci-dessous.

**Écart au gabarit** : un niveau qui n'a pas tous ces fichiers doit contenir, dans son
`README.md`, une section `## Écart au gabarit` qui nomme le fichier absent et justifie
l'absence en trois lignes minimum. Un écart non justifié est un défaut bloquant.

## Format imposé du grimoire

Le grimoire n'est pas un glossaire. Il sert à réviser avant un boss-fight : il doit donc
récapituler ce que la grille évalue, pas seulement du vocabulaire.

Ordre imposé :

1. `# Grimoire : <Nom du niveau>`
2. Un tableau **à 4 colonnes exactement**, en-têtes littérales :

   | Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |

   Une ligne par notion, 8 lignes minimum. La colonne "défendre" contient une question orale.
3. `## Comportements évalués en boss-fight` : tableau à 3 colonnes
   (`Comportement | Preuve attendue dans ta copie | Signal d'échec`), couvrant au minimum la
   justification par un mécanisme, le compromis nommé et assumé, et l'honnêteté sur ce que tu
   ne sais pas. Ces lignes doivent correspondre mot pour mot aux critères de `boss-fight.md`.
4. Les checklists, formules, heuristiques et blocs ASCII existants, conservés **sous** le
   tableau.

## Liens et navigation

Chaque `README.md` de niveau ouvre par une ligne de navigation en liens relatifs :
`[Sommaire](CURRICULUM.md) | [Niveau precedent](../NN-.../README.md) | [Niveau suivant](../NN-.../README.md)`
et liste ses fichiers en liens relatifs cliquables. Toute mention d'un autre fichier du dépôt
dans une leçon est un lien relatif, jamais un nom nu.

## Durées

Une durée annoncée est une estimation dérivée d'un volume, pas une impression. Règle de
calcul unique, appliquée dans `CURRICULUM.md` :

```text
duree_niveau = lecture (150 lignes/h) + exercices (challenge + boss-fight)
arrondi au demi-heure superieur
```

Le total de `CURRICULUM.md` et celui de `README.md` doivent être identiques au quart d'heure.

## Dépendances d'outils

Toute mention d'outil indique une version minimale et une date de vérification, sous la forme
`Node 20 LTS (verifie le 2026-08-03)`. Une liste de ressources externes porte sa date de
dernière vérification en tête de fichier.

## Interdits

- Emojis.
- Listes de définitions sans conséquence pratique.
- "Bonnes pratiques" sans coût associé.
- Exemples génériques (todo, blog, e-commerce basique).
- Promesses vagues ("cela améliore la qualité") sans mécanisme expliqué.
- Analogie hors des univers autorisés, ou sans sa ligne de rupture.

## Longueur

Chaque leçon : 150 à 350 lignes de markdown dense. README de niveau : 60-120 lignes.
