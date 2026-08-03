# Requêtes qui tiennent à l'échelle : index, N+1, curseurs, transactions

## La scène

L'application de refacturation d'énergie affiche, pour chaque copropriété, la liste des
logements avec leur dernier relevé de compteur. Le développeur écrit le code le plus naturel
qui vienne à l'esprit : une requête pour récupérer tous les logements de la copropriété, puis,
pour chacun, une requête séparée pour aller chercher son dernier relevé.

```text
1 requête  : SELECT * FROM logement WHERE copropriete_id = ?      → 40 logements
40 requêtes : SELECT * FROM releve WHERE logement_id = ? ORDER BY date DESC LIMIT 1
             (une par logement, dans une boucle)
```

Sur la copropriété de test (quatre logements), la page charge en 30 millisecondes. Sur la plus
grosse copropriété gérée par le syndic (deux cents logements), la même page fait deux cent une
requêtes séparées à la base à chaque affichage, chacune avec son aller-retour réseau — la page
met huit secondes à s'afficher, et le pic de charge simultané d'une dizaine de gestionnaires
consultant leurs immeubles un lundi matin met la base à genoux. Le code n'a pas de bug logique :
chaque requête individuelle est correcte, rapide, et retourne exactement ce qu'on lui demande.
Le problème est dans leur nombre, pas dans leur contenu.

## Ce qui se passe vraiment

### Le N+1 : le piège qui ne se voit jamais sur un jeu de données de test

Le motif "N+1 requêtes" apparaît partout où une boucle applicative déclenche une requête par
élément d'une liste précédemment récupérée. Il est invisible sur trois lignes de données, et
devient le premier suspect de toute page lente en production dès que le volume grandit — parce
que son coût croît linéairement avec le nombre de lignes affichées, pas avec la complexité de
la fonctionnalité.

```sql
-- Au lieu de N+1 requêtes séparées, une seule requête qui ramène tout d'un coup,
-- en s'appuyant sur une fenêtre pour ne garder que le dernier relevé par logement.
SELECT DISTINCT ON (logement_id) logement_id, valeur, date_releve
FROM releve
WHERE logement_id IN (SELECT id FROM logement WHERE copropriete_id = $1)
ORDER BY logement_id, date_releve DESC;

-- Équivalent avec une window function, plus portable entre moteurs SQL :
SELECT logement_id, valeur, date_releve FROM (
  SELECT logement_id, valeur, date_releve,
         ROW_NUMBER() OVER (PARTITION BY logement_id ORDER BY date_releve DESC) AS rang
  FROM releve
  WHERE logement_id IN (SELECT id FROM logement WHERE copropriete_id = $1)
) sous_requete
WHERE rang = 1;
```

Deux requêtes au lieu de deux cent une : une pour les logements, une pour tous les derniers
relevés d'un coup. Le motif général derrière la solution, valable même sans SQL : **récupérer
toutes les clés d'abord, puis faire une seule requête groupée par ces clés — jamais une requête
par élément d'une boucle**, ce qu'un ORM appelle souvent "eager loading" ou "batch loading".

```text
Comment détecter un N+1 avant qu'il n'atteigne la production :
  - Logger ou compter le nombre de requêtes SQL exécutées par requête HTTP en environnement
    de test, et faire échouer un test si ce nombre dépasse un seuil fixe indépendant du volume
    de données (ex: "cette page ne doit jamais dépasser 5 requêtes SQL, qu'il y ait 4 ou 4000
    logements").
  - Charger un jeu de données de test réaliste en volume (des milliers de lignes), pas
    seulement représentatif en variété — un N+1 sur 4 lignes ne se voit pas au chronomètre.
```

### Pagination par curseur : la liste qui ne bouge pas sous tes pieds

Le journal des relevés de compteur d'un immeuble entier grandit indéfiniment. Une pagination
par offset (`OFFSET 200 LIMIT 50`) demande à la base de parcourir puis d'ignorer les deux cents
premières lignes à chaque page — un coût qui croît avec la profondeur de pagination, et qui
devient instable si des relevés sont insérés pendant la navigation (une ligne peut apparaître
deux fois sur deux pages successives, ou disparaître entièrement).

```sql
-- Curseur basé sur une clé stable et strictement ordonnée (ici, la date de relevé + id
-- en cas d'égalité de date, pour garantir un ordre total sans ambiguïté).
SELECT id, logement_id, valeur, date_releve
FROM releve
WHERE (date_releve, id) < ($1, $2)   -- $1, $2 = dernier élément vu de la page précédente
ORDER BY date_releve DESC, id DESC
LIMIT 50;
```

```text
Index nécessaire pour que ce curseur reste rapide à toute profondeur :
CREATE INDEX idx_releve_curseur ON releve (date_releve DESC, id DESC);

Sans cet index, la clause WHERE + ORDER BY déclenche un tri complet de la table
à chaque page — le curseur devient aussi lent qu'un offset profond, pour rien.
```

Le curseur doit toujours porter sur une combinaison de colonnes qui garantit un ordre total
(aucune égalité possible entre deux lignes distinctes) — sinon deux relevés survenus à la même
milliseconde peuvent se faire sauter mutuellement d'une page à l'autre.

### Transactions : délimiter ce qui doit réussir ou échouer ensemble

Une transaction n'est pas "un bloc autour de plusieurs requêtes par précaution" — c'est la
déclaration explicite d'un invariant métier qui n'a de sens que si toutes les écritures qui le
composent réussissent ensemble, ou aucune.

```sql
BEGIN;
  -- Enregistrer le retour d'un article emprunté...
  UPDATE emprunt SET date_retour = now() WHERE id = $1;
  -- ...ET remettre l'article disponible dans l'inventaire.
  UPDATE materiel SET disponible = true WHERE id = $2;
COMMIT;
-- Si la deuxième instruction échoue (contrainte violée, connexion coupée), la transaction
-- entière est annulée — jamais un emprunt marqué "rendu" avec un matériel resté "indisponible".
```

Sans cette transaction, un incident réseau exactement entre les deux `UPDATE` laisse la base
dans un état incohérent, sans qu'aucune ligne de code ne l'ait explicitement voulu.

### Niveaux d'isolation : ce que "en même temps" veut vraiment dire

Deux transactions concurrentes peuvent interagir de façons subtiles et coûteuses à déboguer.
Le niveau d'isolation choisi décide de ce qui est permis ou interdit entre elles.

```text
READ COMMITTED (défaut de Postgres) :
  Une transaction ne voit que les données déjà validées (commit) par d'autres transactions.
  MAIS : deux lectures successives DANS LA MÊME transaction peuvent voir des valeurs
  différentes si une autre transaction a modifié et validé la donnée entre les deux lectures
  ("non-repeatable read").

REPEATABLE READ :
  Toute la transaction voit un instantané figé de la base au moment de son démarrage.
  Deux lectures successives de la même ligne, dans la même transaction, renvoient toujours
  la même chose. Mais deux transactions REPEATABLE READ concurrentes qui essaient toutes deux
  de faire "lire un solde puis le décrémenter" peuvent quand même produire une incohérence
  si elles ne se recoupent pas explicitement (voir l'exemple ci-dessous).

SERIALIZABLE :
  Garantit que le résultat final est équivalent à une exécution des transactions l'une après
  l'autre, jamais réellement en même temps — au prix d'échecs de transaction (`serialization
  failure`) que le code doit attraper et retenter.
```

```text
Cas concret où READ COMMITTED ne suffit pas, refacturation d'énergie :

Deux gestionnaires valident chacun une correction de relevé sur le même compteur, en même
temps, chacun en partant du relevé actuel affiché sur son écran (chargé une minute plus tôt).

Transaction A : lit le relevé (1000 kWh), calcule le nouveau total, écrit 1050.
Transaction B : lit le relevé (1000 kWh, pas encore le 1050 de A), calcule, écrit 1030.

Résultat final en base : 1030 — la correction de A a été silencieusement perdue, sans aucune
erreur signalée à personne. C'est un "lost update", invisible avec READ COMMITTED puisque
chaque transaction, prise isolément, est parfaitement correcte.
```

```sql
-- Solution 1 : verrou explicite au moment de la lecture, pour empêcher toute autre
-- transaction de lire cette ligne en vue de la modifier tant que la première n'a pas fini.
BEGIN;
  SELECT valeur FROM compteur WHERE id = $1 FOR UPDATE;  -- verrouille la ligne
  -- ... calcul du nouveau total ...
  UPDATE compteur SET valeur = $2 WHERE id = $1;
COMMIT;
-- La transaction B, si elle tente le même SELECT ... FOR UPDATE sur la même ligne pendant
-- que A n'a pas encore validé, est mise en attente jusqu'à ce que A committe ou annule.

-- Solution 2 : verrou optimiste, sans jamais bloquer personne — détecter le conflit
-- au moment d'écrire plutôt que d'empêcher la lecture concurrente.
UPDATE compteur SET valeur = $2, version = version + 1
WHERE id = $1 AND version = $3;  -- $3 = version lue au moment du calcul
-- Si une autre transaction a déjà incrémenté "version" entre-temps, cette instruction
-- ne touche zéro ligne : le code applicatif détecte l'échec et relit / retente.
```

```text
Verrou pessimiste (SELECT ... FOR UPDATE)   Verrou optimiste (colonne "version")
─────────────────────────────────────      ────────────────────────────────────
+ Empêche le conflit avant qu'il arrive     + Aucun verrou tenu, aucun blocage
+ Simple à raisonner                        + Passe mieux à l'échelle sous forte
- Bloque d'autres transactions, risque de     concurrence
  ralentissement voire de deadlock          - Exige que le code sache retenter
- Coûte cher si la transaction est longue     proprement en cas d'échec de version
```

Règle pratique : verrou pessimiste quand les conflits sont fréquents et la transaction courte
(éviter de faire attendre grand monde) ; verrou optimiste quand les conflits sont rares et
qu'on veut éviter tout blocage par défaut (le cas le plus courant d'une API web).

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Requête groupée (batch) au lieu de N+1 | Requête un peu plus complexe à écrire | Coût constant quel que soit le nombre d'éléments affichés | Toujours, dès qu'une boucle applicative peut déclencher une requête |
| Pagination par curseur | Pas de "aller à la page N" direct | Stable et performante à toute profondeur, même sous insertions concurrentes | Liste à croissance non bornée (journal de relevés, historique d'emprunts) |
| Verrou pessimiste (`FOR UPDATE`) | Blocage d'autres transactions pendant la durée du verrou | Empêche le conflit avant qu'il n'arrive, simple à raisonner | Conflits fréquents, transaction courte, criticité forte de l'exactitude |
| Verrou optimiste (colonne `version`) | Le code doit gérer l'échec et retenter explicitement | Aucun blocage, meilleure montée en charge | Conflits rares, API web à forte concurrence de lecture |
| `SERIALIZABLE` partout | Échecs de transaction à gérer, coût de performance du moteur | Garantie la plus forte, zéro incohérence subtile possible | Sections critiques rares et bien identifiées, jamais par défaut sur toute l'API |

## Pièges classiques

- **Le N+1 invisible en développement.** Symptôme : la page est rapide sur la base de test (peu
  de lignes) et devient inexploitable en production sans qu'aucune ligne de code n'ait changé
  entre les deux — seul le volume a changé.
- **L'index absent sur la colonne de tri du curseur.** Symptôme : la pagination par curseur,
  censée être rapide à toute profondeur, déclenche un tri complet de la table à chaque page,
  aussi lente qu'un offset profond qu'elle était censée remplacer.
- **Le curseur sur une colonne qui n'ordonne pas totalement.** Symptôme : deux lignes créées à
  la même seconde se font mutuellement sauter ou dupliquer entre deux pages consécutives.
- **Le "lost update" sous READ COMMITTED.** Symptôme : deux modifications concurrentes d'une
  même ligne, chacune individuellement correcte, aboutissent à ce que l'une des deux disparaisse
  silencieusement sans aucune erreur ni log signalant un problème.
- **Le verrou pessimiste tenu trop longtemps.** Symptôme : une transaction qui fait un appel
  réseau externe (email, paiement) entre son `SELECT ... FOR UPDATE` et son `COMMIT` bloque
  toutes les autres transactions concurrentes sur cette ligne pendant toute la durée de l'appel,
  parfois plusieurs secondes.

## Ce que tu dois savoir défendre

- Explique comment détecter un N+1 avant qu'il n'atteigne la production, avec une méthode
  vérifiable et pas seulement "en faisant attention en relisant le code".
- Décris un "lost update" avec un exemple concret, et explique pourquoi `READ COMMITTED` ne le
  détecte ni ne l'empêche.
- Donne un critère net pour choisir entre verrou pessimiste et verrou optimiste sur un cas
  concret de ce niveau (retour d'emprunt, correction de relevé).
