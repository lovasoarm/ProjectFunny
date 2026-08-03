# Lire un code base en 3 heures

## La scène

On te donne accès au dépôt d'un logiciel de gestion de bibliothèque pour un réseau de clubs
d'escalade : prêt de matériel, réservation de créneaux de mur, suivi des adhésions. 55 000
lignes, TypeScript côté serveur, React côté client, une base PostgreSQL avec 40 tables. Ta
mission : proposer une estimation crédible pour ajouter un système de liste d'attente sur les
créneaux complets. Tu as trois heures avant la réunion de cadrage. Personne ne va t'expliquer
le système avant : c'est à toi de te faire une carte.

## Ce qui se passe vraiment

Lire un gros code base n'est pas une activité de lecture, c'est une activité d'enquête à
budget fixe. Tu as un temps limité et tu dois maximiser la compréhension utile par minute
investie. La méthode qui marche va du plus grossier au plus fin, jamais l'inverse.

```text
Phase 1 (30 min)  Cartographie du terrain
Phase 2 (45 min)  Suivre un cas d'usage réel de bout en bout
Phase 3 (45 min)  Identifier les zones à fort risque et à forte inertie
Phase 4 (45 min)  Lire l'historique (commits, PR, tickets) sur la zone ciblée
Phase 5 (15 min)  Écrire la synthèse avant de l'oublier
```

### Phase 1 : Cartographie du terrain (30 min)

Tu ne lis aucune ligne de logique métier. Tu cherches la structure.

- Le `README` et les scripts de démarrage : que faut-il pour lancer le projet, quelle base
  de données, quels services externes.
- L'arborescence des dossiers de premier et deuxième niveau, sans entrer dedans. Un dossier
  `billing/` séparé de `catalog/` te dit déjà que ce sont deux domaines qu'on a voulu isoler.
- Le fichier de dépendances (`package.json`, `requirements.txt`...). Les librairies utilisées
  te disent l'âge du projet, ses choix d'architecture (un ORM lourd suggère un modèle
  relationnel riche, une lib de queue suggère de l'asynchrone métier important).
- Le schéma de base de données brut, si tu peux l'obtenir (migrations, ou `\d` en SQL). Un
  schéma de données révèle en dix minutes ce que la lecture de code révèle en dix heures :
  les entités qui comptent et leurs relations.

À la fin de cette phase tu dois pouvoir dessiner, à main levée, les grands domaines du
système et deviner (pas encore vérifier) où vit la logique de créneaux et de matériel.

```text
club-lib/
├── membership/     → adhésions, cotisations, statuts
├── catalog/        → matériel (baudriers, cordes, chaussons), état, disponibilité
├── booking/        → créneaux de mur, réservations           ← zone probable
├── billing/        → facturation, remboursements
└── notifications/  → emails, rappels
```

### Phase 2 : Suivre un cas d'usage réel de bout en bout (45 min)

Choisis une action utilisateur concrète et proche de ta mission : "un adhérent réserve un
créneau". Trace-la du clic jusqu'à l'écriture en base, sans dévier vers du code annexe.

- Trouve le composant ou la route qui déclenche l'action (souvent un bouton "Réserver").
- Suis l'appel réseau qui part : quelle route API, quelle méthode HTTP.
- Trouve le contrôleur ou handler côté serveur qui la reçoit.
- Descends jusqu'à la ou les requêtes qui touchent la base.
- Note chaque validation, chaque règle métier croisée en chemin (le créneau est-il complet,
  l'adhérent est-il à jour de cotisation, y a-t-il une limite de réservations simultanées).

Cette traversée verticale unique t'apprend plus que de lire tous les fichiers du dossier
`booking/` dans l'ordre : elle te montre la forme réelle du flux, pas la forme du dossier.

```text
Clic "Réserver" (React)
   │
   ▼
POST /api/slots/:id/book  (route Express)
   │
   ▼
BookingController.book()
   │  ├─ vérifie l'adhésion active (appel à membership/)
   │  ├─ vérifie la capacité du créneau (SELECT COUNT sur bookings)
   │  └─ vérifie la limite de réservations par adhérent (règle métier isolée)
   ▼
BookingRepository.create()
   │
   ▼
INSERT INTO bookings (...)
```

### Phase 3 : Zones à fort risque et forte inertie (45 min)

Une fois le flux principal compris, cherche ce qui va résister à ton changement.

- Cherche les fichiers les plus modifiés dans l'historique (`git log --stat` trié par nombre
  de commits). Un fichier modifié 80 fois en deux ans est un point chaud : il concentre des
  décisions accumulées, probablement fragiles.
- Cherche les fichiers les plus longs et les fonctions les plus longues dans la zone
  concernée. Une fonction de calcul de disponibilité de 200 lignes n'est pas forcément un
  problème, mais c'est un endroit où une modification a plus de chances de casser quelque
  chose que tu ne vois pas.
- Cherche les tests existants sur la zone. Un dossier `booking/` avec zéro test signifie que
  ton estimation doit inclure le temps d'écrire un filet de sécurité avant de toucher quoi
  que ce soit : sinon tu ne sauras jamais si tu as cassé un cas déjà géré.

### Phase 4 : Lire l'historique ciblé (45 min)

Cherche, uniquement sur les fichiers de la zone `booking/`, les messages de commit et les
tickets liés qui mentionnent des bugs passés ou des décisions de design.

```bash
git log --follow -p -- server/booking/availability.ts | less
git log --grep="liste d'attente" --all
```

Un commit qui dit "revert : la version optimiste de la réservation créait des doubles
réservations en cas de forte charge un samedi matin" t'apprend en une ligne une contrainte
de concurrence que tu aurais découverte en production sinon.

### Phase 5 : Synthèse écrite (15 min)

Tu écris, avant la réunion, une synthèse d'une page :

```text
1. Où vit la logique de réservation (fichiers, tables).
2. Le flux de bout en bout que tu as tracé, avec ses points de validation.
3. Les zones fragiles identifiées (fichiers chauds, absence de tests, concurrence).
4. Ce que tu ne sais pas encore et qu'il faudra vérifier avec l'équipe.
5. Une estimation en fourchette, avec la source d'incertitude nommée.
```

Cette synthèse est le vrai livrable de la méthode. Sans elle, ta compréhension s'évapore
dès que tu passes à autre chose, et tu recommenceras l'enquête depuis zéro dans un mois.

## Compromis

| Option                                       | Coût                                                      | Bénéfice                                          | Quand choisir                                                                                |
| -------------------------------------------- | --------------------------------------------------------- | ------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Lire tout le dossier concerné avant d'agir   | Très long, risque de te perdre dans du code mort          | Compréhension exhaustive                          | Zone très critique (paiement, sécurité) où l'exhaustivité prime                              |
| Méthode en 5 phases (ce document)            | Compréhension volontairement partielle mais actionnable   | Rapide, donne un diagnostic utilisable en heures  | Cas général, deadline courte, besoin de décider vite                                         |
| Demander directement à un ancien de l'équipe | Quasi instantané si la personne est disponible et honnête | Contexte impossible à retrouver dans le code seul | Toujours en complément, jamais en remplacement : la mémoire humaine est partielle et biaisée |

## Pièges classiques

- **Tu commences par lire le fichier le plus gros en te disant "c'est sûrement là que tout se
  passe".** Symptôme : tu passes une heure dans un fichier utilitaire générique sans lien
  avec ta mission.
- **Tu confonds le point d'entrée technique (le fichier `index.ts`) avec le point d'entrée de
  compréhension (le flux métier qui t'intéresse).** Symptôme : tu sais démarrer le serveur
  mais tu ne sais toujours pas où vit la règle métier que tu cherches.
- **Tu lis le code sans jamais lancer l'application.** Symptôme : tu as une théorie du
  comportement qui ne correspond pas à ce que fait réellement le système en vrai : clique,
  observe, puis seulement va vérifier dans le code.
- **Tu ignores les tests existants en te disant qu'ils sont accessoires.** Symptôme : les
  tests sont souvent la documentation la plus honnête du comportement attendu, plus fiable
  que les commentaires qui mentent avec le temps.
- **Tu passes ton budget de temps entier en phase 1 parce que cartographier est confortable
  et agir est inconfortable.** Symptôme : à la fin des trois heures tu as un joli schéma et
  aucune idée précise de ce qu'il faut changer.

## Ce que tu dois savoir défendre

- Pourquoi suivre un cas d'usage de bout en bout apprend plus, par minute investie, que lire
  un dossier fichier par fichier.
- Pourquoi l'historique git d'une zone de code est une source d'information à part entière,
  pas un détail administratif.
- Comment tu répartirais un budget de trois heures si la mission portait sur un bug de
  sécurité plutôt que sur une estimation de feature.
