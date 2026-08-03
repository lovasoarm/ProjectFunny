# Schéma, contraintes, index : normaliser puis dénormaliser sciemment

## La scène

Le club d'escalade veut enfin un vrai schéma. Un développeur arrive, a lu un article sur la
"3ème forme normale", et éclate consciencieusement toutes les données en tables minuscules :
`Pays`, `Ville`, `CodePostal`, `Rue` séparés pour l'adresse d'un adhérent, une table
`TypeMateriel` séparée de `Materiel`, une table `StatutEmprunt` séparée de `Emprunt`. Le
résultat : afficher la liste des emprunts en cours nécessite sept jointures. La page qui
listait les emprunts du jour, censée être la plus consultée de l'application par les bénévoles
à l'accueil, met deux secondes à charger sur un jeu de données de trois mille lignes. Personne
n'a mesuré le coût de la normalisation avant de l'appliquer partout par principe.

Le problème inverse existe aussi et il est plus dangereux : une base entièrement dénormalisée,
où le nom de l'adhérent est recopié dans chaque ligne d'emprunt "pour aller plus vite", où
un changement de nom (mariage) oblige à mettre à jour cinquante lignes historiques ou, pire,
n'est mis à jour nulle part et laisse cohabiter deux noms pour la même personne selon la table
consultée.

## Ce qui se passe vraiment

### Normaliser : éliminer la donnée qui peut se contredire elle-même

La normalisation n'est pas une religion esthétique, c'est une réponse à un problème précis :
**une même information stockée à deux endroits peut diverger**, et le jour où elle diverge, la
base ne sait plus laquelle des deux copies dit la vérité. Chaque forme normale élimine une
catégorie précise de ce risque.

```text
Table non normalisée (Emprunt) :
  id | adherent_nom | adherent_email | materiel_nom | materiel_categorie | date_emprunt

Problème : "materiel_categorie" dépend de "materiel_nom", pas de la ligne d'emprunt elle-même.
Si on change la catégorie d'un article, il faut réécrire TOUTES ses lignes d'emprunt passées,
ou vivre avec des lignes contradictoires (un article "Baudrier" catégorisé "Sécurité" sur une
ligne et "Protection" sur une autre, selon quand la ligne a été écrite).

Normalisé (3ème forme normale, l'essentiel) :
  Adherent(id, nom, email)
  Materiel(id, nom, categorie)
  Emprunt(id, adherent_id FK, materiel_id FK, date_emprunt, date_retour)

Chaque fait n'est écrit qu'à un seul endroit. Changer la catégorie d'un article = une seule
ligne à modifier, et tout l'historique la reflète immédiatement et correctement.
```

Le critère concret pour savoir si une colonne est mal placée : **si je change cette valeur,
combien de lignes dois-je toucher, et est-ce que ça correspond à la réalité du monde ?** La
catégorie d'un article change une fois, pour l'article : elle doit être écrite une fois, sur la
table `Materiel`. Le nom d'un adhérent à l'instant T d'un emprunt passé, en revanche, ne
"change" pas rétroactivement au sens propre : c'est une information historique. Recopier le nom
de l'adhérent au moment de l'emprunt n'est pas une violation de normalisation si c'est fait
consciemment pour figer un fait historique (voir dénormalisation ci-dessous) : mais il faut le
nommer explicitement (`nom_adherent_au_moment_emprunt`), pas le confondre avec "le nom actuel de
l'adhérent" lu depuis une jointure.

### Contraintes : faire porter les invariants par la base, pas par la confiance

Les invariants de la leçon précédente doivent, dès que c'est possible, devenir des contraintes
que la base refuse de violer : indépendamment de ce que fait le code applicatif, qui sera un
jour bugué, contourné, ou appelé par un script de migration écrit à la va-vite un dimanche soir.

```sql
-- INV-2 du club d'escalade : la date de retour est postérieure à la date d'emprunt.
CREATE TABLE emprunt (
  id            uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  adherent_id   uuid NOT NULL REFERENCES adherent(id),
  materiel_id   uuid NOT NULL REFERENCES materiel(id),
  date_emprunt  timestamptz NOT NULL DEFAULT now(),
  date_retour   timestamptz,
  CONSTRAINT retour_apres_emprunt CHECK (date_retour IS NULL OR date_retour >= date_emprunt)
);

-- INV-1 : un article ne peut avoir qu'un emprunt actif (date_retour NULL) à la fois.
-- Une contrainte UNIQUE classique ne suffit pas (NULL n'est jamais égal à NULL en SQL,
-- donc plusieurs lignes NULL cohabiteraient sans violation). Il faut un index unique partiel :
CREATE UNIQUE INDEX un_seul_emprunt_actif_par_materiel
  ON emprunt (materiel_id)
  WHERE date_retour IS NULL;
```

Cet index partiel est la traduction exacte de l'invariant métier : "au plus une ligne sans date
de retour, par article". Toute tentative d'insertion qui le violerait échoue au niveau de la
base, quel que soit le chemin de code emprunté : API REST, script d'import, console SQL
d'urgence à 23h. C'est la différence entre un invariant "documenté" et un invariant "garanti".

### Index : accélérer une lecture a toujours un prix à l'écriture

Un index n'est pas gratuit. Il accélère les lectures qui filtrent ou trient sur la colonne
indexée, au prix d'un espace disque supplémentaire et d'un ralentissement de chaque écriture
(chaque `INSERT`/`UPDATE`/`DELETE` doit aussi mettre à jour tous les index concernés).

```text
Sans index sur emprunt.materiel_id :
  SELECT * FROM emprunt WHERE materiel_id = '...' ;
  → scan complet de la table (lit CHAQUE ligne pour vérifier la condition)
  → coût proportionnel à la taille TOTALE de la table, pas au résultat recherché

Avec index sur emprunt.materiel_id :
  → recherche dans une structure arborescente (B-tree), coût quasi-logarithmique
  → mais chaque emprunt inséré doit désormais aussi mettre à jour cet index
```

Règle pratique : indexer les colonnes utilisées dans un `WHERE`, un `JOIN`, ou un `ORDER BY`
d'une requête réellement exécutée souvent, jamais "au cas où". Une table avec quinze index sur
des colonnes jamais filtrées ralentit chaque écriture pour un bénéfice nul.

```sql
-- Index composite : l'ordre des colonnes compte. Cet index sert une requête qui filtre par
-- adherent_id ET trie par date_emprunt, mais ne sert PAS une requête qui filtre uniquement
-- sur date_emprunt (l'index composite ne peut être utilisé que "de gauche à droite").
CREATE INDEX idx_emprunt_adherent_date ON emprunt (adherent_id, date_emprunt DESC);
```

### Dénormaliser sciemment : le contraire n'est pas un péché, c'est un choix chiffré

Dénormaliser, c'est réintroduire de la redondance volontairement, pour une raison précise et
mesurée : jamais "pour aller plus vite" sans avoir mesuré que c'était nécessaire.

```text
Cas légitime de dénormalisation, refacturation d'énergie :

Facture(id, logement_id, periode, montant_total, tarif_applique_snapshot jsonb)

"tarif_applique_snapshot" recopie le détail du tarif au moment de l'émission de la facture,
alors que le tarif "vivant" existe déjà dans TarifEnergie. Ce n'est pas une erreur de
modélisation : une facture émise doit rester lisible et immuable même si le tarif change plus
tard dans le référentiel : c'est une exigence légale et comptable, pas un raccourci de
performance.

Cas illégitime (le vrai piège) :

Emprunt(id, adherent_id, adherent_nom, adherent_email, materiel_id, ...)

"adherent_nom" et "adherent_email" sont recopiés "pour éviter une jointure" sur une donnée qui,
elle, change au fil du temps sans raison métier de figer une version historique. Résultat :
l'email de l'adhérent change dans Adherent, mais reste faux dans deux cents lignes d'Emprunt
existantes, et personne ne sait laquelle des deux copies interroger pour lui envoyer un message.
```

Le critère : dénormaliser une donnée qui doit rester figée pour des raisons métier ou légales
(un snapshot explicite, nommé comme tel) est sain. Dénormaliser une donnée qui doit rester
synchronisée avec sa source pour économiser une jointure est une dette technique qui finira par
mentir.

## Compromis

| Option                                         | Coût                                                                       | Bénéfice                                                                  | Quand choisir                                                    |
| ---------------------------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| Normalisation stricte (3NF) partout            | Plus de jointures à chaque lecture, plus de tables à maintenir             | Aucune incohérence possible, une seule source de vérité par fait          | Par défaut, sauf preuve mesurée du contraire                     |
| Dénormalisation en snapshot explicite          | Duplication assumée, champ nommé clairement (`_snapshot`, `_au_moment_de`) | Immuabilité et lisibilité d'un document historique (facture, contrat)     | Donnée qui doit rester figée pour des raisons légales/comptables |
| Dénormalisation "de confort" (cache non nommé) | Risque de divergence silencieuse, source de vérité ambiguë                 | Une jointure en moins en apparence                                        | Quasiment jamais sans mesure de performance réelle préalable     |
| Index composite large                          | Coût d'écriture, coût de maintenance                                       | Sert plusieurs requêtes filtrant/triant sur les mêmes colonnes en préfixe | Requêtes fréquentes et identifiées, pas anticipées               |

## Pièges classiques

- **L'index qui ne sert à rien.** Symptôme : `EXPLAIN` montre un scan complet malgré un index
  présent : souvent parce que l'ordre des colonnes de l'index composite ne correspond pas à
  l'ordre du `WHERE`, ou parce qu'une fonction est appliquée sur la colonne (`WHERE
LOWER(email) = ...` ignore un index sur `email` brut).
- **La contrainte NULL oubliée sur une clé étrangère.** Symptôme : des lignes "orphelines"
  logiquement invalides insérées silencieusement, découvertes seulement lors d'un rapport qui
  s'appuie sur une jointure interne qui les exclut sans prévenir.
- **La dénormalisation non documentée.** Symptôme : deux développeurs différents corrigent la
  même incohérence à deux endroits différents, dans deux sens différents, parce que personne ne
  savait laquelle des deux colonnes était la source de vérité.
- **La contrainte `UNIQUE` classique censée remplacer un index partiel.** Symptôme : deux
  emprunts actifs simultanés sur le même article passent sans erreur, parce que `NULL` n'est
  jamais égal à `NULL` en SQL : la contrainte croyait interdire un doublon qu'elle n'a jamais
  détecté.
- **Sur-normaliser une donnée qui n'a jamais deux occurrences réelles.** Symptôme : une table
  `Civilite(id, libelle)` avec trois lignes ("M.", "Mme", "Non précisé") jointe partout pour
  rien : le gain de normalisation (éviter une incohérence) est nul sur un référentiel de trois
  valeurs qui ne changera jamais.

## Ce que tu dois savoir défendre

- Explique, avec l'exemple de la facture d'énergie, pourquoi dénormaliser peut être la décision
  correcte : et à quelle condition précise ça reste sain plutôt que dangereux.
- Pourquoi une contrainte `UNIQUE` classique ne suffit-elle pas à garantir "un seul emprunt
  actif par article", et comment un index partiel résout-il exactement ce problème ?
- Donne un exemple où ajouter un index rendrait une lecture plus rapide mais dégraderait une
  écriture au point de ne pas valoir le coup : chiffre l'arbitrage même approximativement.
