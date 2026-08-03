# Migrations, historique, suppression douce : faire évoluer un schéma vivant

## La scène

La bibliothèque du club d'escalade doit ajouter une colonne `taille_baudrier` sur `materiel`,
obligatoire pour respecter une nouvelle règle de sécurité fédérale. Le développeur écrit :

```sql
ALTER TABLE materiel ADD COLUMN taille_baudrier text NOT NULL;
```

En local, sur trois lignes de test, ça passe. En production, sur douze mille lignes déjà là,
la migration échoue immédiatement : `NOT NULL` sans valeur par défaut sur une colonne ajoutée
à une table pleine exige que Postgres sache quoi mettre dans les douze mille lignes existantes,
et il ne le sait pas. Deuxième tentative, cette fois avec une valeur par défaut : la migration
passe, mais sur une grosse table elle verrouille l'écriture sur `materiel` pendant plusieurs
secondes : le temps qu'il faut à la base pour réécrire les métadonnées de chaque ligne existante.
Pendant ces quelques secondes, l'application de prêt à l'accueil, qui écrit sur cette table à
chaque emprunt, se bloque. Un bénévole tape "l'appli est en panne" dans le groupe WhatsApp du
club, un samedi matin de forte affluence.

Le vrai problème n'est ni le SQL ni la colonne. C'est d'avoir pensé la migration comme un
événement instantané ("j'ajoute la colonne") alors que c'est un processus qui a une durée, un
état intermédiaire, et un risque de verrou : sur une table qui continue de vivre pendant qu'on
la modifie.

## Ce qui se passe vraiment

### Une migration n'est pas un `ALTER TABLE`, c'est un changement de contrat en plusieurs étapes

Le réflexe naturel : "je veux ce schéma final, j'écris le SQL qui y mène directement" : casse
dès que la table a un volume réel et que l'application ne s'arrête pas pendant la migration
(ce qui est le cas de toute application qui n'accepte pas une coupure de service programmée).
La bonne approche découpe le changement en étapes qui restent chacune sûres à tout moment,
même si le déploiement s'arrête au milieu.

```text
Motif "expand / contract" (étendre puis retirer), pour renommer une colonne sans downtime :

Étape 1 : EXPAND : ajouter le nouveau sans toucher à l'ancien
  ALTER TABLE materiel ADD COLUMN taille text NULL;
  → le code ancien continue de lire/écrire "taille_baudrier", inchangé.

Étape 2 : BACKFILL : remplir la nouvelle colonne pour les lignes existantes,
  par lots, pas en une seule transaction géante
  UPDATE materiel SET taille = taille_baudrier
  WHERE id IN (SELECT id FROM materiel WHERE taille IS NULL LIMIT 1000);
  -- répété jusqu'à ce qu'il n'y ait plus rien à traiter

Étape 3 : DOUBLE ÉCRITURE : déployer un code applicatif qui écrit dans LES DEUX colonnes
  à chaque écriture, pendant une période de transition (jours, pas minutes).

Étape 4 : BASCULE DE LECTURE : déployer un code qui LIT désormais la nouvelle colonne.
  Vérifier en production que tout fonctionne, sur un temps suffisant pour être sûr.

Étape 5 : CONTRACT : retirer l'ancienne colonne et la double écriture, une fois certain
  que plus aucun code en production ne dépend de l'ancienne.
  ALTER TABLE materiel DROP COLUMN taille_baudrier;
```

Chaque étape, prise seule, est réversible et sans risque de verrou long. Le prix : ce qui
semblait être "une ligne de SQL" devient plusieurs déploiements coordonnés dans le temps. C'est
le vrai coût, souvent ignoré, d'un renommage de colonne sur une table de production vivante —
et c'est pour ça qu'un renommage "juste pour la lisibilité" sur une table critique n'est presque
jamais gratuit.

### Ajouter une colonne obligatoire sans verrouiller la table

Le piège d'ouverture de cette leçon a une solution directe, spécifique à ce cas précis :

```sql
-- MAUVAIS sur une grosse table : NOT NULL + valeur par défaut en une seule instruction
-- verrouille et réécrit chaque ligne existante (comportement historique de Postgres < 11,
-- et toujours vrai sur d'autres moteurs ou avec certains types de colonnes).
ALTER TABLE materiel ADD COLUMN taille_baudrier text NOT NULL DEFAULT 'M';

-- BON : ajouter la colonne nullable, sans verrou long (metadata-only sur Postgres récent)
ALTER TABLE materiel ADD COLUMN taille_baudrier text;

-- Remplir par lots, en arrière-plan, sans bloquer les écritures concurrentes
UPDATE materiel SET taille_baudrier = 'M' WHERE taille_baudrier IS NULL;

-- Ajouter la contrainte NOT NULL séparément, en deux temps sur Postgres :
-- une contrainte CHECK NOT VALID ne vérifie pas les lignes existantes tout de suite,
-- puis VALIDATE CONSTRAINT le fait sans verrou exclusif prolongé.
ALTER TABLE materiel ADD CONSTRAINT materiel_taille_not_null
  CHECK (taille_baudrier IS NOT NULL) NOT VALID;
ALTER TABLE materiel VALIDATE CONSTRAINT materiel_taille_not_null;
```

Le motif général : **séparer la définition d'une contrainte de sa validation sur les données
existantes**. Une contrainte posée `NOT VALID` protège immédiatement toute nouvelle écriture,
sans exiger un scan bloquant immédiat de tout l'historique.

### Migration réversible : toujours pouvoir revenir en arrière sans perte

Une migration qui ne peut pas être annulée transforme chaque déploiement en pari à sens unique.
Le critère de réversibilité n'est pas "techniquement, on pourrait écrire le SQL inverse" : c'est
"si on l'exécute, personne n'a perdu de donnée qui n'existe plus ailleurs".

```text
Migration réversible :
  ADD COLUMN, CREATE INDEX, CREATE TABLE
  → l'inverse (DROP) ne perd que ce que la migration elle-même a créé.

Migration destructive, à traiter différemment :
  DROP COLUMN, DROP TABLE, ALTER COLUMN TYPE (changement de type avec perte de précision)
  → l'inverse ne peut pas restaurer une donnée déjà supprimée.
  Règle : ne JAMAIS enchaîner un DROP dans le même déploiement qui rend cette colonne inutile.
  Toujours un délai d'observation entre "plus personne ne lit cette colonne" et "on la supprime".
```

### Historique : la ligne ne doit jamais mentir sur ce qu'elle était

Le modèle mental de la leçon 2 revient ici avec sa traduction concrète : une donnée dont l'état
"actuel" ne suffit pas doit être versionnée dans le temps, pas écrasée à chaque changement.

```sql
-- MAUVAIS : le tarif "vivant", écrasé à chaque changement : impossible de recalculer
-- une facture passée avec le tarif qui était réellement applicable à l'époque.
CREATE TABLE tarif_energie (id uuid PRIMARY KEY, prix_kwh numeric NOT NULL);

-- BON : chaque changement de tarif est une NOUVELLE ligne, avec une période de validité.
CREATE TABLE tarif_energie (
  id           uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  prix_kwh     numeric NOT NULL,
  valide_du    timestamptz NOT NULL,
  valide_au    timestamptz,  -- NULL = toujours en vigueur à ce jour
  CONSTRAINT periode_coherente CHECK (valide_au IS NULL OR valide_au > valide_du)
);

-- L'invariant "à toute date, exactement un tarif applicable" (INV-3 de la leçon 2)
-- se traduit en une contrainte d'exclusion, qui interdit deux périodes qui se chevauchent :
CREATE EXTENSION IF NOT EXISTS btree_gist;
ALTER TABLE tarif_energie ADD CONSTRAINT pas_de_chevauchement
  EXCLUDE USING gist (tstzrange(valide_du, valide_au) WITH &&);
```

`tstzrange` et l'opérateur `&&` (chevauchement) permettent à la base de refuser, au moment de
l'insertion, deux tarifs dont les périodes se recoupent : l'invariant temporel devient une
garantie de la base, pas une espérance du code applicatif.

### Suppression douce (soft delete) : quand "supprimer" ne doit pas effacer

Supprimer une ligne avec `DELETE` la fait disparaître définitivement : y compris de tout
historique, tout audit, toute facture qui la référence. Pour beaucoup de domaines, ce n'est pas
ce que "supprimer" veut vraiment dire.

```sql
-- Soft delete : marquer, ne pas effacer.
ALTER TABLE materiel ADD COLUMN supprime_le timestamptz;

-- Chaque requête "normale" doit exclure explicitement le supprimé : sinon un article
-- retiré de l'inventaire réapparaît dans les listes actives.
SELECT * FROM materiel WHERE supprime_le IS NULL;

-- Traduire l'INV-4 de la leçon 2 ("un article retiré ne peut plus être prêté, mais son
-- historique reste consultable") : la contrainte d'index partiel de la leçon 3 doit,
-- elle aussi, ignorer le matériel supprimé pour ne pas fausser la détection de doublon.
CREATE UNIQUE INDEX un_seul_emprunt_actif_par_materiel
  ON emprunt (materiel_id)
  WHERE date_retour IS NULL;
-- (l'invariant porte sur emprunt, pas sur materiel : materiel_id supprimé + emprunt
-- clos ne pose pas de problème ; c'est le service applicatif qui doit interdire un
-- NOUVEL emprunt sur un article supprimé : INV-4 n'est pas exprimable en contrainte pure)
```

Le vrai piège du soft delete n'est pas la colonne elle-même, c'est d'oublier une des requêtes
qui doit la filtrer : une vue de rapport, un export, un endpoint API écrit six mois plus tard
par quelqu'un qui ne connaît pas la convention. La solution durable : ne jamais interroger la
table brute directement en dehors d'un point d'accès central (une vue SQL, ou un repository
applicatif unique) qui applique le filtre une fois pour toutes.

```sql
-- Vue qui devient LE point d'accès "matériel actif" pour tout le reste du code.
CREATE VIEW materiel_actif AS
  SELECT * FROM materiel WHERE supprime_le IS NULL;
```

### Audit trail : savoir qui a fait quoi, et reconstruire l'état passé

Le soft delete répond à "est-ce encore là ?". L'audit répond à une question différente : "qui a
changé quoi, quand, et à partir de quel état ?" : nécessaire dès qu'une décision (une facture
contestée, un article manquant à l'inventaire) doit pouvoir être reconstituée après coup.

```sql
CREATE TABLE audit_log (
  id           uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  table_cible  text NOT NULL,
  ligne_id     uuid NOT NULL,
  action       text NOT NULL CHECK (action IN ('INSERT', 'UPDATE', 'DELETE')),
  avant        jsonb,             -- NULL si INSERT
  apres        jsonb,             -- NULL si DELETE
  acteur_id    uuid NOT NULL,     -- qui a fait l'action, jamais NULL même pour un script
  survenu_le   timestamptz NOT NULL DEFAULT now()
);
```

```text
Deux façons de peupler cet audit, avec un compromis net :

Trigger SQL (au niveau de la base)
  + Capture TOUT changement, quel que soit le chemin (API, script, console d'urgence)
  - Ne connaît pas "l'acteur" applicatif (l'utilisateur HTTP) sans effort supplémentaire
    (variable de session à faire passer explicitement)

Code applicatif (dans la couche service, avant chaque écriture)
  + Connaît naturellement l'acteur (déjà authentifié dans la requête en cours)
  - Un chemin d'écriture qui contourne ce code (migration manuelle, accès direct) n'est pas
    audité : le trou de sécurité classique
```

Le choix par défaut sain : trigger pour la garantie de complétude, avec l'acteur transmis via
une variable de session (`SET LOCAL app.current_user_id = '...'`) posée par le code applicatif
au début de chaque transaction : combinant la garantie du trigger et la connaissance de l'acteur.

## Compromis

| Option                                    | Coût                                                               | Bénéfice                                                          | Quand choisir                                                                               |
| ----------------------------------------- | ------------------------------------------------------------------ | ----------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| Expand/contract en plusieurs déploiements | Plus lent, plus de coordination, code temporaire (double écriture) | Zéro downtime, chaque étape réversible isolément                  | Table de production avec trafic réel, changement structurel (renommage, changement de type) |
| `ALTER TABLE` direct en une étape         | Rapide à écrire                                                    | Simplicité si la table est petite ou l'application peut s'arrêter | Table de test, environnement de développement, fenêtre de maintenance acceptée              |
| Contrainte `NOT VALID` puis `VALIDATE`    | Deux instructions au lieu d'une                                    | Pas de verrou exclusif prolongé pendant la validation             | Grosse table en production, contrainte nouvelle sur données existantes                      |
| Soft delete                               | Chaque requête doit filtrer, risque d'oubli                        | Réversible, garde l'historique et les références intactes         | Donnée référencée ailleurs (factures, emprunts passés) ou récupérable métier                |
| Suppression dure (`DELETE`)               | Irréversible                                                       | Plus simple, pas de filtre à maintenir partout                    | Donnée réellement jetable, jamais référencée, sans exigence légale de conservation          |
| Audit via trigger SQL                     | Coût de maintenance du trigger, légère charge à l'écriture         | Garantie de complétude quel que soit le chemin d'écriture         | Domaine réglementé ou litigieux (facturation, santé, sécurité)                              |

## Pièges classiques

- **`NOT NULL` + valeur par défaut sur une grosse table en une seule instruction.** Symptôme :
  la migration bloque les écritures pendant plusieurs secondes ou minutes en production,
  alors qu'elle était instantanée en local sur un jeu de données minuscule.
- **Un `DROP COLUMN` dans le même déploiement que le code qui arrête de la lire.** Symptôme :
  un déploiement partiel (rollback applicatif après incident) laisse tourner l'ancien code face
  à une colonne qui n'existe plus, erreur 500 en cascade.
- **Le soft delete oublié dans une requête.** Symptôme : un article supprimé de l'inventaire
  réapparaît dans un export ou un rapport écrit par quelqu'un qui ignorait la convention
  `supprime_le IS NULL`.
- **Le tarif ou l'état "vivant" écrasé au lieu d'être versionné.** Symptôme : une facture
  recalculée après coup donne un montant différent de la facture originale, parce que le tarif
  utilisé pour le calcul a changé entre-temps dans le référentiel.
- **L'audit qui ne connaît que le code, pas la console d'urgence.** Symptôme : un `UPDATE` manuel
  fait un dimanche soir pour corriger un incident n'apparaît nulle part dans l'audit trail, et
  personne ne sait expliquer pourquoi une donnée a changé six mois plus tard.

## Ce que tu dois savoir défendre

- Explique pourquoi ajouter une colonne `NOT NULL` avec une valeur par défaut peut verrouiller
  une table de production, et quelle séquence d'étapes l'évite.
- Décris le motif expand/contract sur un exemple de renommage de colonne, et explique pourquoi
  chaque étape prise isolément doit rester sûre même si le déploiement s'arrête au milieu.
- Pourquoi un trigger d'audit SQL offre-t-il une garantie que le code applicatif seul ne peut
  pas offrir, et quel est le prix de cette garantie ?
