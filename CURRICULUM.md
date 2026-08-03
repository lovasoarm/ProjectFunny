# CURRICULUM — les 16 niveaux

Chaque niveau indique : ce que tu sais faire à la sortie, le livrable, la durée réaliste,
et ce qui casse dans un projet si ce niveau est sauté.

| # | Niveau | Sortie | Livrable | Durée |
|---|--------|--------|----------|-------|
| 00 | PROLOGUE | Tu sais comment utiliser ce parcours et sur quel projet | Choix du projet fil rouge écrit | 1 h |
| 01 | MINDSET | Tu raisonnes en systèmes, coûts et hypothèses | Une note de conception + un ADR | 6 h |
| 02 | PROBLEM-HUNT | Tu sépares demande, besoin et contrainte | Cahier de problème + non-objectifs + métriques | 8 h |
| 03 | MVP-SPLIT | Tu découpes en tranches verticales livrables | Backlog tranché + budget de temps | 6 h |
| 04 | USER-WIZARD | Tu conçois des parcours qui tiennent aux cas limites | Flux + états + maquettes basse fidélité | 8 h |
| 05 | DATA-SPELLS | Tu modélises un domaine et fais évoluer un schéma | Modèle de données + migrations + requêtes | 12 h |
| 06 | ARCHI-LAB | Tu poses des frontières et justifies une architecture | Carte de contexte + ADR d'architecture | 10 h |
| 07 | API-DOJO | Tu écris des contrats robustes aux pannes | Spec d'API + politique d'erreurs et d'auth | 10 h |
| 08 | ROADMAP-RUN | Tu planifies par le risque, pas par l'ordre des écrans | Roadmap par jalons + registre de risques | 6 h |
| 09 | QUALITY-SHIELD | Tu choisis quoi tester, mesurer, alerter | Stratégie de tests + plan d'observabilité | 10 h |
| 10 | TEAM-QUEST | Tu travailles à plusieurs sans bloquer la livraison | Working agreement + flux Git documenté | 6 h |
| 11 | BIG-APP-SNOOP | Tu entres dans un gros code inconnu et le cartographies | Rapport d'exploration en 3 h chrono | 8 h |
| 12 | CAPSTONE-ARENA | Tu conduis un projet complet depuis un brief ambigu | Projet livré + dossier de décisions | 25-40 h |
| 13 | DAY-TO-LEGEND | Tu progresses seul, durablement | Routine 12 semaines + portfolio de preuves | continu |
| 14 | TOOL-CAVE | Tu es rapide à déboguer et outillé | Environnement et checklists de debug | 4 h |
| 15 | BONUS-VAULT | Tu couvres sécurité, coûts, données personnelles | Revue de risques du projet fil rouge | 6 h |

## Ce qui casse si tu sautes un niveau

- Sans 02 : tu construis vite la mauvaise chose. C'est le mode d'échec le plus courant et le plus cher.
- Sans 03 : tu livres au bout de six mois, ou jamais.
- Sans 05 : le schéma te bloque au premier vrai changement métier ; tu réécris tout.
- Sans 06 : chaque nouvelle feature touche dix fichiers ; la vélocité s'effondre au troisième mois.
- Sans 07 : ça marche en démo, ça meurt au premier timeout réseau ou au premier double-clic.
- Sans 09 : tu apprends les pannes par tes utilisateurs.
- Sans 10 : deux devs produisent moins qu'un seul.

## Prérequis techniques

Un langage que tu connais (TypeScript recommandé pour suivre les exemples), une base SQL, Git,
et un éditeur. Rien d'autre. Le curriculum est volontairement peu dépendant des outils : voir
`ANNEXE-perennite.md`.
