# CURRICULUM : les 16 niveaux

Chaque niveau indique : ce que tu sais faire à la sortie, le livrable, la durée réaliste,
et ce qui casse dans un projet si ce niveau est sauté.

| #   | Niveau         | Sortie                                                  | Livrable                                       | Durée   |
| --- | -------------- | ------------------------------------------------------- | ---------------------------------------------- | ------- |
| 00  | PROLOGUE       | Tu sais comment utiliser ce parcours et sur quel projet | Choix du projet fil rouge écrit                | 1 h     |
| 01  | MINDSET        | Tu raisonnes en systèmes, coûts et hypothèses           | Une note de conception + un ADR                | 6 h     |
| 02  | PROBLEM-HUNT   | Tu sépares demande, besoin et contrainte                | Cahier de problème + non-objectifs + métriques | 8 h     |
| 03  | MVP-SPLIT      | Tu découpes en tranches verticales livrables            | Backlog tranché + budget de temps              | 6 h     |
| 04  | USER-WIZARD    | Tu conçois des parcours qui tiennent aux cas limites    | Flux + états + maquettes basse fidélité        | 8 h     |
| 05  | DATA-SPELLS    | Tu modélises un domaine et fais évoluer un schéma       | Modèle de données + migrations + requêtes      | 12 h    |
| 06  | ARCHI-LAB      | Tu poses des frontières et justifies une architecture   | Carte de contexte + ADR d'architecture         | 10 h    |
| 07  | API-DOJO       | Tu écris des contrats robustes aux pannes               | Spec d'API + politique d'erreurs et d'auth     | 10 h    |
| 08  | ROADMAP-RUN    | Tu planifies par le risque, pas par l'ordre des écrans  | Roadmap par jalons + registre de risques       | 6 h     |
| 09  | QUALITY-SHIELD | Tu choisis quoi tester, mesurer, alerter                | Stratégie de tests + plan d'observabilité      | 10 h    |
| 10  | TEAM-QUEST     | Tu travailles à plusieurs sans bloquer la livraison     | Working agreement + flux Git documenté         | 6 h     |
| 11  | BIG-APP-SNOOP  | Tu entres dans un gros code inconnu et le cartographies | Rapport d'exploration en 3 h chrono            | 8 h     |
| 12  | CAPSTONE-ARENA | Tu conduis un projet complet depuis un brief ambigu     | Projet livré + dossier de décisions            | 25-40 h |
| 13  | DAY-TO-LEGEND  | Tu progresses seul, durablement                         | Routine 12 semaines + portfolio de preuves     | continu |
| 14  | TOOL-CAVE      | Tu es rapide à déboguer et outillé                      | Environnement et checklists de debug           | 4 h     |
| 15  | BONUS-VAULT    | Tu couvres sécurité, coûts, données personnelles        | Revue de risques signée du projet fil rouge    | 9 h     |

## Ce qui casse si tu sautes un niveau

- Sans 02 : tu construis vite la mauvaise chose. C'est le mode d'échec le plus courant et le plus cher.
- Sans 03 : tu livres au bout de six mois, ou jamais.
- Sans 05 : le schéma te bloque au premier vrai changement métier ; tu réécris tout.
- Sans 06 : chaque nouvelle feature touche dix fichiers ; la vélocité s'effondre au troisième mois.
- Sans 07 : ça marche en démo, ça meurt au premier timeout réseau ou au premier double-clic.
- Sans 09 : tu apprends les pannes par tes utilisateurs.
- Sans 10 : deux devs produisent moins qu'un seul.

## Prérequis techniques et versions de référence

Un langage que tu connais, une base SQL, Git, et un éditeur. Rien d'autre. Le curriculum est
volontairement peu dépendant des outils, mais les exemples doivent tourner : ils sont écrits et
vérifiés contre les versions ci-dessous. Si tu utilises une version inférieure, une partie des
mécanismes enseignés n'existe pas chez toi, et tu croiras avoir mal compris.

| Outil | Version minimale | Version de référence | Vérifié le |
| --- | --- | --- | --- |
| PostgreSQL | 12 | 16 | 2026-08-03 |
| Node.js | 20 LTS | 22 LTS | 2026-08-03 |
| TypeScript | 5.4 | 5.5 | 2026-08-03 |
| Git | 2.40 | 2.45 | 2026-08-03 |
| Docker Engine | 24 | 27 | 2026-08-03 |
| psql (client) | 14 | 16 | 2026-08-03 |

### Le niveau 05 est écrit pour PostgreSQL, pas pour SQL en général

Le niveau [05-DATA-SPELLS](05-DATA-SPELLS/README.md) utilise des mécanismes propres à Postgres :

| Mécanisme | Disponible à partir de | Équivalent MySQL 8 |
| --- | --- | --- |
| `ADD CONSTRAINT ... NOT VALID` puis `VALIDATE CONSTRAINT` | Postgres 9.4 | Aucun équivalent : la validation est toujours bloquante |
| Contrainte d'exclusion `EXCLUDE USING gist` | Postgres 9.2 | Aucun équivalent : il faut un verrou applicatif ou une table de créneaux |
| Index partiel `CREATE INDEX ... WHERE ...` | Postgres 7.2 | Aucun équivalent direct : colonne générée plus index |
| `CREATE INDEX CONCURRENTLY` | Postgres 8.2 | `ALTER TABLE ... ALGORITHM=INPLACE`, garanties différentes |
| Type `jsonb` et index GIN | Postgres 9.4 | `JSON` sans index natif équivalent |

Si tu es sur MySQL ou SQLite, lis quand même le niveau 05, mais installe un Postgres 16 pour les
exercices : sinon la moitié des exercices est infaisable, et ce n'est pas ta faute.

Les ressources externes et leur date de vérification sont dans
[ANNEXE-perennite.md](ANNEXE-perennite.md).
