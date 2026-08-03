# Ressources durables, sans liens morts

## Le piège

Tu cherches à approfondir la conception de systèmes distribués pour le suivi de tournées de
livraison en temps réel. Tu tombes sur un article de blog très partagé, truffé de captures
d'écran d'une console cloud qui n'existe plus sous cette forme deux ans plus tard, et de
noms de services qui ont changé trois fois depuis. L'article est mort avant que tu aies fini
de le lire. Ce niveau ne te donne donc pas une liste de liens : les liens périment. Il te
donne une méthode pour trouver toi-même des ressources durables, et les catégories de
sources qui vieillissent bien par nature.

## Ce qui se passe vraiment

Une ressource technique vieillit mal quand elle décrit une implémentation précise d'un outil
précis à un instant précis. Elle vieillit bien quand elle décrit un mécanisme, une
contrainte physique ou logique, ou un compromis qui ne dépend pas de la version d'un outil.

```text
Vieillit mal                              Vieillit bien
──────────────────────────────           ──────────────────────────────
"Tutoriel : configurer X avec la          "Pourquoi les systèmes distribués
version 4.2 de l'outil Y"                 ne peuvent pas garantir
                                           simultanément cohérence, disponibilité
                                           et tolérance au partitionnement"
                                           (contrainte du théorème CAP : vraie
                                           quel que soit l'outil)

"Les 10 meilleures librairies             "Comment décider quand une
JavaScript en 2024"                       dépendance externe vaut son coût
                                           de maintenance à long terme"
```

### Catégories de sources qui durent

1. **Les livres de référence sur les mécanismes, pas sur les outils.** Un livre sur la
   théorie des bases de données relationnelles reste vrai bien après qu'un SGBD précis ait
   changé trois fois de version majeure. Cherche des livres publiés par des maisons
   d'édition techniques reconnues (le nom compte moins que la vérification : le livre décrit
   un mécanisme général ou une API précise ?).
2. **Les post-mortems publics d'incidents réels.** Une entreprise qui documente
   publiquement pourquoi son système est tombé raconte une contrainte réelle du monde, pas
   une opinion. Cherche par le nom de l'entreprise plus "post-mortem" ou "incident report" —
   la structure du document (chronologie, cause racine, actions correctives) reste
   identique quel que soit le domaine technique.
3. **Les papiers de recherche fondateurs, même anciens.** Un papier sur les transactions
   distribuées ou sur la complexité algorithmique publié il y a vingt ans décrit toujours
   une contrainte vraie aujourd'hui : les contraintes mathématiques ne périment pas.
4. **La documentation officielle d'un standard, pas d'un produit.** Un standard (protocole
   HTTP, format de données, norme de sécurité) a un cycle de vie mesuré en décennies. La
   documentation d'un produit propriétaire a un cycle de vie mesuré en mois.
5. **Le code source de projets reconnus et actifs depuis longtemps.** Lire comment un projet
   utilisé par des millions de systèmes gère un problème donné enseigne plus qu'un tutoriel,
   parce que le code a survécu à des années de cas limites réels.

### Comment vérifier qu'une ressource durera

```text
Question à te poser avant de t'investir dans une ressource :

"Cette ressource explique-t-elle POURQUOI une contrainte existe,
ou seulement COMMENT contourner cette contrainte avec l'outil du moment ?"

→ Si "pourquoi", elle survivra au changement d'outil.
→ Si seulement "comment", elle a une durée de vie limitée à la version de l'outil.
```

### Comment chercher sans dépendre d'une liste figée

Plutôt qu'une liste de liens qui périmera, voici les requêtes de recherche qui restent
efficaces dans le temps :

- `[nom de l'entreprise] post-mortem [type d'incident]` : trouve des retours d'expérience
  réels, peu importe l'année.
- `[concept technique] paper original` : remonte à la source plutôt qu'à une vulgarisation
  qui peut avoir déformé le concept.
- `[standard ou protocole] RFC` : pour les standards internet, la RFC originale reste la
  référence la plus stable qui existe.
- `[nom du projet open-source] design doc` ou `architecture decision` : la plupart des
  grands projets publient leurs décisions d'architecture, une mine de compromis réels
  documentés.

## Compromis

| Approche                                               | Coût                                           | Bénéfice                                                | Quand choisir                                              |
| ------------------------------------------------------ | ---------------------------------------------- | ------------------------------------------------------- | ---------------------------------------------------------- |
| Suivre des tutoriels d'actualité sur l'outil du moment | Rapide, opérationnel immédiatement             | Périme vite, n'enseigne pas le mécanisme sous-jacent    | Pour une tâche ponctuelle urgente sur un outil précis      |
| Investir dans les mécanismes et contraintes générales  | Plus lent, moins immédiatement applicable      | Reste vrai des années, transférable à tout nouvel outil | Investissement de fond, en continu, hors urgence           |
| Lire le code source de projets reconnus                | Coût de temps élevé, exige un bagage technique | Compréhension profonde, pas seulement une recette       | Sur un sujet que tu utilises régulièrement dans ton métier |

## Pièges classiques

- **Confondre popularité et durabilité.** Symptôme : un article très partagé sur les
  réseaux sociaux au moment de sa sortie ne se retrouve nulle part deux ans plus tard,
  parce qu'il décrivait une astuce liée à une version précise d'un outil.
- **Collectionner des liens sans jamais les relire.** Symptôme : une liste de favoris de
  plusieurs centaines d'entrées, jamais consultée, qui donne une fausse impression de
  ressource constituée.
- **Éviter les papiers de recherche par crainte de la difficulté de lecture.** Symptôme :
  tu réapprends par blog interposé des concepts déjà bien expliqués, mais dilués, dans la
  source originale.

## Ce que tu dois savoir défendre

- Explique la différence entre une ressource qui explique "pourquoi" et une ressource qui
  explique seulement "comment", et pourquoi cette différence prédit sa durée de vie.
- Donne un exemple de ressource que tu as consultée récemment et qui a déjà périmé ou qui
  va périmer bientôt, et explique pourquoi.
- Cite deux stratégies de recherche qui restent efficaces indépendamment des modes
  technologiques du moment.
