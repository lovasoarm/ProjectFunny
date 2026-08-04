# Challenge : Niveau 14 : Tool Cave

## Contexte

Tu vas prouver que tu maîtrises ton outillage sur un cas réel, pas sur un exercice jouet.
Choisis un bug réel non trivial dans un projet que tu as déjà écrit ou dans un projet
open-source connu (pas un bug que tu inventes pour l'occasion : un vrai, déjà là, jamais
complètement compris).

## Livrable

Un document unique (`debug-report.md`, 1 à 3 pages) contenant, dans l'ordre :

1. **Scénario minimal de reproduction** : les étapes exactes pour reproduire le bug à
   volonté, avec le taux de reproduction observé (idéalement 100%, sinon indique le taux
   réel et pourquoi il n'est pas plus élevé).
2. **Au moins deux hypothèses falsifiables**, formulées avant investigation, chacune avec la
   prédiction précise qui permettrait de la confirmer ou de l'infirmer.
3. **Le protocole de bissection ou d'isolation utilisé** : ce que tu as divisé, comment, et
   le nombre d'étapes qu'il t'a fallu pour converger vers la cause.
4. **Un log ciblé** que tu as écrit spécifiquement pour ce bug, avec la question précise
   qu'il devait trancher.
5. **La cause racine confirmée**, distincte du symptôme observé initialement.
6. **Un test de non-régression** qui aurait attrapé ce bug avant qu'il ne parte en
   production.

## Deuxième partie : configuration d'outillage

Documente, dans une section séparée du même fichier :

- Trois frictions récurrentes de ton workflow actuel (éditeur ou shell), le nombre
  approximatif de fois par semaine où elles se produisent, et la correction que tu as mise
  en place (avec la config exacte, pas une intention vague).
- Un log de trois utilisations réelles d'un assistant IA sur ce projet ou un autre récent,
  chacune avec : le prompt utilisé, ce que l'IA a produit, et ce que ta revue critique a
  changé, confirmé, ou rejeté dans sa réponse. Si les trois cas se sont bien passés sans
  aucune correction nécessaire, explique pourquoi c'est suspect plutôt que rassurant.

## Critères de réussite mesurables

- Le bug choisi est réel, pas inventé : vérifiable par l'historique Git ou l'issue tracker.
- Les hypothèses sont formulées avant la lecture de la solution, pas reconstruites après
  coup pour faire joli (si tu connaissais déjà la cause avant de commencer, choisis un autre
  bug).
- Le nombre d'étapes de bissection est cohérent avec une progression logarithmique, pas un
  tâtonnement déguisé en méthode.
- Le test de non-régression échoue sur le code buggé et passe sur le code corrigé : vérifié,
  pas affirmé.
- Au moins un des trois cas d'usage IA documentés contient une correction réelle apportée
  par ta revue critique, pas seulement une validation complaisante.

## Ce qui invalide le challenge

- Un bug reconstruit après coup pour correspondre à la méthode plutôt qu'un bug réellement
  résolu par la méthode.
- Des hypothèses vagues non falsifiables ("ça devait être un problème de state").
- Une revue critique de l'IA qui se limite à "c'était bon du premier coup" sur les trois cas.

## Durée cible

4 h, cohérent avec la durée annoncée pour ce niveau dans CURRICULUM.md. Si tu dépasses
largement ce budget, c'est un signal à noter dans ta rétrospective de bloc, pas une raison
de bâcler la fin du livrable.
