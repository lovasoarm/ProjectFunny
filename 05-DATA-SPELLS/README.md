# Niveau 05 — Data Spells

## Ce niveau en une phrase
Modéliser des données qui restent vraies dans dix ans, sous concurrence, avec de l'historique, et
qui répondent vite même quand la table a dix millions de lignes.

## Pourquoi ce nom
Un schéma de base de données bien pensé a quelque chose d'un sort : invisible tant qu'il fonctionne,
et absolument impossible à défaire proprement s'il est mal lancé au départ. Une migration mal
pensée n'est pas un bug qu'on corrige, c'est une dette qu'on porte pendant des années.

## Ce que tu sais déjà en arrivant ici
- Tu sais écrire des requêtes SQL simples et créer des tables avec des clés étrangères.
- Tu as déjà utilisé un ORM pour lire et écrire des données.
- Tu n'as jamais eu à décider seul comment modéliser une relation many-to-many avec de l'historique,
  ni à écrire une migration réversible sur une table déjà pleine de données réelles.

## Ce que tu sauras faire à la sortie
- Modéliser un domaine en identifiant ses entités, ses invariants, et le bon choix entre clé
  naturelle et clé technique.
- Normaliser un schéma pour éviter les incohérences, puis dénormaliser sciemment quand la
  performance ou la lecture l'exige, en connaissant le prix exact du choix.
- Écrire des migrations réversibles, gérer les données historiques, choisir entre suppression dure
  et suppression douce, et savoir quand un audit trail est nécessaire.
- Écrire des requêtes qui restent rapides à l'échelle : indexation pertinente, éviter le N+1,
  paginer par curseur plutôt que par offset, choisir un niveau d'isolation de transaction en
  connaissance de cause.

## Terrain de jeu
Ce niveau utilise deux fils rouges complémentaires : la refacturation d'énergie d'un syndic
d'immeuble (compteurs, relevés, tarifs qui changent dans le temps, répartition entre logements) et
la bibliothèque d'un club d'escalade (emprunts de matériel, adhérents, historique de prêts). Deux
domaines avec de vrais invariants temporels et une vraie concurrence d'accès.

## Plan du niveau
1. `01-why-this-level.md` — ce qui casse quand le modèle de données est pensé après le code.
2. `02-model-the-domain.md` — entités, invariants, clés naturelles vs techniques.
3. `03-relations-and-normalization.md` — normaliser puis dénormaliser sciemment.
4. `04-migrations-and-time.md` — migrations réversibles, historique, soft delete, audit.
5. `05-queries-that-scale.md` — index, N+1, pagination par curseur, transactions et isolation.
6. `challenge.md` — exercice appliqué et critères de réussite mesurables.
7. `boss-fight.md` — situation adverse réaliste, avec grille d'évaluation.
8. `grimoire.md` — mémo dense à garder sous la main.

## Durée estimée
Une semaine et demie. La modélisation (leçon 2 et 3) demande du temps de réflexion sur papier avant
tout code — ne le saute pas, c'est la partie la plus rentable du niveau.

## Prérequis
Niveau 04, ou une expérience équivalente d'écriture d'API avec base de données relationnelle. Tu dois
savoir écrire une jointure SQL et une transaction basique avant d'attaquer la leçon 3.

## Comment progresser
Modélise un des deux domaines sur papier avant d'ouvrir un éditeur SQL. Code le schéma, remplis-le
avec un volume de données réaliste (des dizaines de milliers de lignes, pas dix), puis observe ce
qui devient lent et pourquoi. Le challenge et le boss fight vérifient que tes décisions tiennent sous
charge, pas seulement sur ton jeu de données de trois lignes.
