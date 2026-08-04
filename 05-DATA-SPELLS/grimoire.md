# Grimoire : Niveau 05, Data Spells

Mémo à ouvrir avant de modéliser un schéma ou de migrer une table de production vivante.
Sert à vérifier le mécanisme, pas à réciter du vocabulaire SQL. Postgres 16 (verifie le
2026-08-03).

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Entité | Chose du domaine avec une identité stable, indépendante de ses attributs. | `SELECT id FROM materiel WHERE id = '3f2a...';` | numéro de table en salle, stable même si le plat commandé change / numéro de cordage, stable même si sa longueur est recoupée |
| Clé naturelle / clé technique | Donnée du monde réel censée être unique, contre un identifiant inventé par le système. | `id uuid PRIMARY KEY DEFAULT gen_random_uuid(), email text UNIQUE` | nom du client en salle vs numéro de couvert attribué / nom du marin vs matricule de bord |
| Invariant | Règle qui doit rester vraie en toutes circonstances, défendue par une contrainte de base. | `ALTER TABLE creneau ADD CONSTRAINT prix_positif CHECK (prix >= 0);` | règle de sécurité aux urgences valable pour tout patient, sans exception / point d'ancrage qui ne doit jamais lâcher, quelle que soit la cordée |
| Normalisation | Éliminer la duplication d'un même fait pour qu'il ne puisse jamais se contredire lui-même. | `CREATE TABLE client (id uuid PRIMARY KEY, nom text);` | un seul carnet de commandes fait foi en cuisine, pas une copie par serveur / un seul livre de bord fait foi, pas un carnet par matelot |
| Dénormalisation en snapshot | Recopier volontairement une donnée pour figer un fait historique, nommée explicitement. | `ALTER TABLE facture ADD COLUMN tarif_au_moment_emission numeric NOT NULL;` | ticket de caisse qui garde le prix du jour, même si la carte change ensuite / relevé de position figé au moment du passage, même si la route change après |
| Index partiel | Index restreint aux lignes qui vérifient une condition, pour imposer "au plus une ligne" avec NULL impliqué. | `CREATE UNIQUE INDEX ON creneau (salle_id) WHERE annule_le IS NULL;` | un seul plat du jour actif par service, les anciens ne comptent plus / une seule route active tracée sur la carte, les anciennes sont archivées |
| Contrainte d'exclusion | Garantit qu'aucune paire de lignes ne viole une relation donnée, par exemple deux périodes qui se chevauchent. | `EXCLUDE USING gist (salle_id WITH =, periode WITH &&);` | deux réservations de la même table qui ne peuvent jamais se chevaucher / deux créneaux de mouillage au même quai qui ne peuvent jamais se chevaucher |
| Expand/contract | Découper un changement de schéma en étapes sûres isolément, sans downtime. | `ALTER TABLE materiel ADD COLUMN taille text; -- puis backfill, bascule, drop` | changement de menu en cuisine annoncé et testé avant de retirer l'ancien plat de la carte / changement de gréement testé en double avant de retirer l'ancien matériel |

## Défense orale

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Entité | Tu confonds identité et attributs, deux lignes différentes se retrouvent fusionnées par erreur | Peut-elle changer d'attribut sans changer d'identité ? |
| Clé naturelle / clé technique | Une clé naturelle qui change casse toutes les références qui s'appuient dessus | Pourquoi cette table utilise une clé technique et pas la clé naturelle en identifiant primaire ? |
| Invariant | Un chemin de code oublié viole la règle silencieusement, incident découvert bien plus tard | Cet invariant est-il défendu par une contrainte de base, ou seulement par du code applicatif ? |
| Normalisation | Deux copies du même fait divergent, plus personne ne sait laquelle est vraie | Ce fait est-il dupliqué quelque part, et si oui pourquoi assumé ? |
| Dénormalisation en snapshot | Un historique se met à jour rétroactivement, une facture déjà émise change de montant | Pourquoi cette copie est un snapshot volontaire et pas un oubli de normalisation ? |
| Index partiel | Une contrainte UNIQUE classique laisse passer plusieurs lignes NULL, l'invariant n'est jamais posé | Pourquoi une contrainte UNIQUE simple ne suffit pas ici ? |
| Contrainte d'exclusion | Deux périodes se chevauchent silencieusement, la base ne le refuse jamais | Comment la base empêche-t-elle, elle-même, deux périodes qui se chevauchent ? |
| Expand/contract | Un déploiement interrompu au milieu laisse le schéma dans un état incohérent, coupure de service | Si le déploiement s'arrête à cette étape précise, le système reste-t-il cohérent ? |

Grille détaillée : voir [boss-fight.md](./boss-fight.md).

## Arbre de décision : normaliser ou dénormaliser une colonne

```text
Cette donnée change-t-elle indépendamment de la ligne qui la porte ?
  OUI -> normaliser (table séparée, référencée par clé étrangère).
  NON v

Cette donnée doit-elle rester figée pour une raison métier ou légale (facture, contrat) ?
  OUI -> dénormaliser en snapshot, NOMMÉ explicitement (_snapshot, _au_moment_de).
  NON -> laisser normalisé, mesurer avant d'optimiser.
```

## Arbre de décision : quel verrou de concurrence

```text
Les conflits d'écriture sur cette ligne sont-ils fréquents ?
  OUI, et la transaction est courte -> verrou pessimiste (SELECT ... FOR UPDATE).
  NON, conflits rares, forte concurrence de lecture -> verrou optimiste (colonne version).
  Garantie absolue requise, section critique rare et identifiée -> SERIALIZABLE, avec retry.
```

## Checklist avant toute migration sur une table de production vivante

- [ ] Le volume réel de la table a été pris en compte (pas seulement testé sur un jeu de trois
      lignes).
- [ ] Aucune contrainte `NOT NULL` + valeur par défaut n'est ajoutée en une seule instruction
      sur une table déjà pleine : passer par `NOT VALID` + `VALIDATE` ou un backfill par lots.
- [ ] Un `DROP COLUMN`/`DROP TABLE` n'est jamais dans le même déploiement que le code qui arrête
      de la lire : un délai d'observation sépare les deux.
- [ ] Chaque étape d'une migration expand/contract reste sûre isolément si le déploiement
      s'arrête au milieu.
- [ ] Le temps de verrouillage estimé de chaque instruction a été vérifié (`EXPLAIN`, doc du
      moteur), pas supposé.

## Checklist avant d'exposer une requête de liste à fort volume

- [ ] Aucune boucle applicative ne déclenche une requête par élément d'une liste précédente
      (N+1) : vérifié par un compteur de requêtes en test, pas seulement à l'oeil.
- [ ] La pagination est par curseur si le volume est non borné ou en forte croissance, avec un
      index couvrant exactement l'ordre du curseur.
- [ ] La clé de curseur garantit un ordre total (aucune égalité possible entre deux lignes
      distinctes).
- [ ] Chaque `WHERE`/`JOIN`/`ORDER BY` réellement exécuté souvent a un index correspondant,
      jamais "au cas où".

## Requêtes prêtes à copier

```sql
-- 1. Ajouter une contrainte NOT NULL sans verrou exclusif prolongé sur une table pleine.
-- Etape a : poser la contrainte NOT VALID, elle protege immediatement les nouvelles ecritures
-- sans scanner les lignes existantes.
ALTER TABLE materiel
  ADD CONSTRAINT materiel_taille_not_null CHECK (taille_baudrier IS NOT NULL) NOT VALID;
```

```sql
-- 2. Valider la contrainte sur les lignes existantes, dans un second temps.
-- Ce scan lit la table mais ne pose pas de verrou exclusif comme le ferait un ALTER classique.
ALTER TABLE materiel VALIDATE CONSTRAINT materiel_taille_not_null;
```

```sql
-- 3. Creer un index sans bloquer les ecritures concurrentes, restreint aux lignes utiles.
-- CONCURRENTLY evite le verrou exclusif ; WHERE reduit la taille de l'index aux lignes actives.
CREATE INDEX CONCURRENTLY idx_creneau_salle_actif
  ON creneau (salle_id, debut)
  WHERE annule_le IS NULL;
```

```sql
-- 4. Empecher deux reservations de la meme salle de se chevaucher dans le temps.
-- btree_gist est requis pour combiner egalite (=) et chevauchement (&&) dans une seule contrainte.
CREATE EXTENSION IF NOT EXISTS btree_gist;

ALTER TABLE creneau
  ADD CONSTRAINT creneau_pas_de_chevauchement
  EXCLUDE USING gist (salle_id WITH =, periode WITH &&);
```

```sql
-- 5. Migration expand-contract complete pour renommer une colonne sans downtime.
-- Etape EXPAND : ajouter la nouvelle colonne, nullable, sans toucher a l'ancienne.
ALTER TABLE materiel ADD COLUMN taille text;

-- Etape BACKFILL : remplir par lots pour ne pas verrouiller toute la table d'un coup.
UPDATE materiel SET taille = taille_baudrier
  WHERE id IN (SELECT id FROM materiel WHERE taille IS NULL LIMIT 1000);
-- repeter jusqu'a ce qu'il n'y ait plus rien a traiter

-- Etape CONTRACT (une fois le code applicatif bascule et observe un temps suffisant) :
ALTER TABLE materiel DROP COLUMN taille_baudrier;
```

## Heuristique de secours

Avant d'écrire une contrainte "dans le code applicatif seulement", demande-toi : "si quelqu'un
modifie cette table directement en SQL un dimanche soir d'urgence, cet invariant tient-il
encore ?" Si la réponse est non, l'invariant doit descendre dans la base : contrainte `CHECK`,
index unique partiel, ou contrainte d'exclusion : indépendamment de la confiance qu'on a dans
le code applicatif.

## Si tu rates le boss-fight

Relis d'abord le critère qui a plafonné ta note : refus argumenté de l'UPDATE en place,
schéma minimal livrable, ou cohérence de lecture du tarif. Reprends la scène en identifiant
la donnée qui doit devenir un snapshot et celle qui doit rester normalisée. Relis l'arbre de
décision ci-dessus. Attends 48 h avant de retenter le boss-fight pour juger la scène à froid.
Si l'échec se reproduit sur le même critère, redescends au niveau 04 relire "invariant d'une
feature" : un mauvais schéma cache souvent un invariant jamais nommé.
