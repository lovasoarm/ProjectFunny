# Challenge : Le bouclier qualité du système de tournées

## Contexte

Tu reprends le système de tournées de livraison frigorifique du fil rouge de ce niveau. Il
fonctionne, mais n'a aucun des quatre bouclier vus dans ce niveau. Ta mission : les
construire, priorisés par coût de panne réel, pas par exhaustivité.

## Livrable

1. Une matrice écrite (tableau) des cinq zones les plus critiques du système (ex :
   conformité chaîne du froid, affectation de tournée, facturation), avec pour chacune :
   coût de panne estimé, niveau de test choisi (unitaire, intégration, e2e) et pourquoi.
2. Au moins un test d'intégration sur la fonction de conformité chaîne du froid, utilisant
   un jeu de données de capteur rejoué (pas une valeur inventée), couvrant un cas de données
   manquantes ou en retard.
3. Un système de logs structurés sur au moins un flux critique (ex : confirmation de
   livraison), avec des champs interrogeables (pas de texte libre).
4. Une métrique et une alerte sur un symptôme visible par l'utilisateur (ex : taux d'échec
   de confirmation de livraison), avec un seuil et une fenêtre de temps justifiés par écrit.
5. Un pipeline de CI (fichier de configuration réel, GitHub Actions ou équivalent) qui
   bloque le merge si le lint, le typage ou les tests échouent, avec les étapes rapides
   avant les étapes lentes.
6. Un gabarit de postmortem prêt à remplir, avec les quatre sections vues en leçon 05
   (chronologie, cause immédiate/structurelle, ce qui a fonctionné, actions assignées).

## Critères de réussite mesurables

- La matrice de priorisation justifie chaque choix par un coût de panne concret, pas par
  "c'est facile à tester" ou "c'est la norme de l'équipe".
- Le test d'intégration de conformité chaîne du froid échoue si on lui injecte un relevé de
  capteur avec un champ manquant, et le comportement attendu en cas d'échec est explicite
  (jamais un "conforme par défaut" silencieux).
- Une recherche dans les logs structurés permet de répondre en une seule requête à "quelles
  livraisons de l'entrepôt X ont échoué dans les 30 dernières minutes", sans `grep` sur du
  texte libre.
- L'alerte configurée ne se déclenche pas sur une variation normale de trafic (testée avec
  un jeu de données simulé sur une journée normale) mais se déclenche sur le scénario
  d'incident simulé (pic d'échecs).
- Le pipeline de CI bloque effectivement un merge si un test échoue (testé en cassant
  volontairement un test) et le fait en moins de 5 minutes sur les étapes bloquantes rapides.
- Le gabarit de postmortem, rempli sur un incident fictif de ton choix, produit au moins deux
  actions concrètes assignées et datées, aucune formulée comme "faire plus attention".

## Ce qui n'est pas demandé

Pas besoin de couvrir 100 % du système ni d'atteindre un pourcentage de couverture donné. Le
jury évalue la justesse des priorités et la fiabilité réelle des mécanismes construits, pas
leur exhaustivité.
