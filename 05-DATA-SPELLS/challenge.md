# Challenge : Modéliser et faire tenir un domaine à l'échelle

## Contexte

Choisis un des deux fils rouges du niveau (refacturation d'énergie ou bibliothèque du club
d'escalade) et construis le schéma complet, du modèle papier jusqu'à des requêtes qui restent
rapides sur un volume réaliste.

## Livrable

1. Un schéma (diagramme texte ou fichier `.sql` de création) qui modélise au minimum quatre
   entités liées, avec au moins une relation qui porte de l'historique (occupation d'un
   logement, emprunts de matériel) et au moins un référentiel versionné dans le temps (tarif,
   catégorie de matériel avec changement de règle).
2. Au moins trois invariants métier explicitement formulés en texte, dont au moins deux traduits
   en contraintes SQL réelles (contrainte `CHECK`, index unique partiel, contrainte d'exclusion
   sur une période).
3. Un jeu de données réaliste en volume : au moins 50 000 lignes sur la table la plus fréquentée
   du schéma (relevés ou emprunts), généré par script, pas saisi à la main.
4. Une migration écrite en deux versions : la version naïve qui casse ou verrouille sur ce
   volume, et la version corrigée (expand/contract ou `NOT VALID` + `VALIDATE`), avec le temps
   d'exécution mesuré des deux pour prouver la différence.
5. Une requête de liste qui affiche, pour une entité parente, une information dérivée de ses
   entités enfants (dernier relevé d'un logement, dernier emprunt d'un adhérent) : écrite d'abord
   en version N+1, puis corrigée en une requête groupée, avec le nombre de requêtes SQL mesuré
   avant et après.
6. Un endpoint ou une fonction de pagination par curseur sur la table à fort volume, avec un test
   qui prouve sa stabilité : insérer une ligne au milieu de la pagination et vérifier qu'aucune
   ligne n'est ni dupliquée ni sautée.
7. Une décision écrite (soft delete ou suppression dure) pour une entité du schéma, justifiée par
   ce qui la référence ailleurs.

## Critères de réussite mesurables

- Les contraintes SQL rejettent effectivement une insertion qui violerait l'invariant qu'elles
  sont censées garantir : prouvé par une tentative d'insertion qui échoue avec le bon message
  d'erreur, pas seulement affirmé.
- La migration naïve, mesurée sur les 50 000 lignes, prend un temps significativement plus long
  ou verrouille la table (mesurable par un `EXPLAIN (ANALYZE, BUFFERS)` ou un chronomètre) que
  la version corrigée.
- Le nombre de requêtes SQL exécutées par la version corrigée de la requête N+1 ne dépend pas du
  nombre d'entités enfants affichées (reste constant que la liste contienne 4 ou 4000 éléments).
- Le test de stabilité de la pagination par curseur passe : aucune ligne perdue ni dupliquée
  après une insertion concurrente pendant la navigation.
- Chaque invariant métier formulé en texte est retraçable jusqu'à une ligne de SQL ou une ligne
  de code qui le garantit réellement : pas seulement documenté dans un commentaire.

## Ce que ce challenge ne demande pas

Il ne demande pas de construire une interface utilisateur ni un système de scopes
d'autorisation complet (ça, c'est le niveau API Dojo) : le livrable est le schéma, les
migrations, et les requêtes, prouvés par des mesures et des tests qui échouent aux bons
endroits sur les versions naïves.

## Durée cible

12 h, cohérent avec la durée annoncée pour ce niveau dans CURRICULUM.md. Si tu dépasses
largement ce budget, c'est un signal à noter dans ta rétrospective de bloc, pas une raison
de bâcler la fin du livrable.
