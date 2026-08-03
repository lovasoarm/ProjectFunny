# Grimoire — Data Spells

## Vocabulaire

- **Entité** : chose du domaine avec une identité stable, indépendante de ses attributs — teste
  avec "peut-elle changer d'attribut sans changer d'identité ?".
- **Clé naturelle / clé technique** : une donnée du monde réel censée être unique, contre un
  identifiant inventé par le système sans signification métier. Par défaut : clé technique,
  clé naturelle en `UNIQUE`.
- **Invariant** : règle qui doit rester vraie en toutes circonstances, quel que soit le chemin
  de code emprunté — se traduit en contrainte de base quand c'est possible.
- **Normalisation** : éliminer la duplication d'un même fait pour qu'il ne puisse jamais se
  contredire lui-même entre deux copies.
- **Dénormalisation en snapshot** : recopier volontairement une donnée pour figer un fait
  historique (facture), nommée explicitement — distincte d'une dénormalisation "de confort".
- **Index partiel** : index restreint aux lignes qui vérifient une condition (`WHERE ... IS
  NULL`), seul moyen fiable d'imposer "au plus une ligne de ce type" quand `NULL` est impliqué.
- **Contrainte d'exclusion** : garantit qu'aucune paire de lignes ne viole une relation donnée
  (ex : aucune période qui chevauche une autre) — au-delà de ce qu'une contrainte `UNIQUE` sait faire.
- **Expand/contract** : découper un changement de schéma en étapes (ajouter, remplir, basculer,
  retirer) qui restent chacune sûres isolément, sans downtime.
- **`NOT VALID` / `VALIDATE CONSTRAINT`** : poser une contrainte immédiatement pour les nouvelles
  écritures, valider les lignes existantes séparément, sans verrou exclusif prolongé.
- **Soft delete** : marquer une ligne comme supprimée sans l'effacer, pour préserver ce qui la
  référence — exige un filtre systématique côté lecture.
- **Audit trail** : trace de qui a changé quoi et quand, indépendante du soft delete, nécessaire
  dès qu'une décision doit être reconstituable après coup.
- **N+1** : une requête par élément d'une liste dans une boucle applicative, coût qui croît
  linéairement avec le volume — invisible sur un petit jeu de données.
- **Pagination par curseur** : navigation basée sur une clé stable et ordonnée en totalité,
  stable même sous insertions/suppressions concurrentes, contrairement à l'offset.
- **Lost update** : deux transactions concurrentes lisent la même valeur, écrivent chacune une
  version corrigée, l'une écrase silencieusement l'autre — invisible sous `READ COMMITTED`.
- **Verrou pessimiste (`FOR UPDATE`)** : empêche le conflit en bloquant les lecteurs concurrents
  d'une ligne destinée à être modifiée.
- **Verrou optimiste (colonne `version`)** : détecte le conflit au moment d'écrire, sans jamais
  bloquer, au prix d'une logique de retry côté code.

## Arbre de décision — normaliser ou dénormaliser une colonne

```text
Cette donnée change-t-elle indépendamment de la ligne qui la porte ?
  OUI → normaliser (table séparée, référencée par clé étrangère).
  NON ↓

Cette donnée doit-elle rester figée pour une raison métier ou légale (facture, contrat) ?
  OUI → dénormaliser en snapshot, NOMMÉ explicitement (`_snapshot`, `_au_moment_de`).
  NON → laisser normalisé, mesurer avant d'optimiser.
```

## Arbre de décision — quel verrou de concurrence

```text
Les conflits d'écriture sur cette ligne sont-ils fréquents ?
  OUI, et la transaction est courte → verrou pessimiste (SELECT ... FOR UPDATE).
  NON, conflits rares, forte concurrence de lecture → verrou optimiste (colonne version).
  Garantie absolue requise, section critique rare et identifiée → SERIALIZABLE, avec retry.
```

## Checklist avant toute migration sur une table de production vivante

- [ ] Le volume réel de la table a été pris en compte (pas seulement testé sur un jeu de trois
      lignes).
- [ ] Aucune contrainte `NOT NULL` + valeur par défaut n'est ajoutée en une seule instruction
      sur une table déjà pleine — passer par `NOT VALID` + `VALIDATE` ou un backfill par lots.
- [ ] Un `DROP COLUMN`/`DROP TABLE` n'est jamais dans le même déploiement que le code qui arrête
      de la lire — un délai d'observation sépare les deux.
- [ ] Chaque étape d'une migration expand/contract reste sûre isolément si le déploiement
      s'arrête au milieu.
- [ ] Le temps de verrouillage estimé de chaque instruction a été vérifié (`EXPLAIN`, doc du
      moteur), pas supposé.

## Checklist avant d'exposer une requête de liste à fort volume

- [ ] Aucune boucle applicative ne déclenche une requête par élément d'une liste précédente
      (N+1) — vérifié par un compteur de requêtes en test, pas seulement à l'œil.
- [ ] La pagination est par curseur si le volume est non borné ou en forte croissance, avec un
      index couvrant exactement l'ordre du curseur.
- [ ] La clé de curseur garantit un ordre total (aucune égalité possible entre deux lignes
      distinctes).
- [ ] Chaque `WHERE`/`JOIN`/`ORDER BY` réellement exécuté souvent a un index correspondant —
      jamais "au cas où".

## Heuristique de secours

Avant d'écrire une contrainte "dans le code applicatif seulement", demande-toi : "si quelqu'un
modifie cette table directement en SQL un dimanche soir d'urgence, cet invariant tient-il
encore ?" Si la réponse est non, l'invariant doit descendre dans la base — contrainte `CHECK`,
index unique partiel, ou contrainte d'exclusion — indépendamment de la confiance qu'on a dans
le code applicatif.
