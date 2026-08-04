# Challenge : Revue de risques du projet fil rouge

## Consigne

Ce coffre porte un livrable obligatoire, annoncé dans le curriculum : la revue de risques du
projet fil rouge que tu mènes au Niveau 12 (Capstone Arena). Produis un fichier
`REVUE-DE-RISQUES.md`, signé et daté, couvrant trois familles de risques : sécurité, coûts,
données personnelles. Ce livrable n'est pas optionnel : il est un critère éliminatoire de
`../12-CAPSTONE-ARENA/04-evaluation-grid.md`.

## Gabarit imposé de REVUE-DE-RISQUES.md

```text
# Revue de risques : [nom du projet]

Date de la revue : [date]
Auteur : [ton nom]
Signature : [ton nom]

## Sécurité

| Risque | Probabilité (basse/moyenne/haute) | Impact (faible/moyen/fort) | Mitigation |
| ------ | ---------------------------------- | ---------------------------- | ---------- |
| ...    | ...                                 | ...                           | ...        |

## Coûts

| Risque | Probabilité | Impact | Mitigation |
| ------ | ----------- | ------ | ---------- |
| ...    | ...         | ...    | ...        |

## Données personnelles

| Risque | Probabilité | Impact | Mitigation |
| ------ | ----------- | ------ | ---------- |
| ...    | ...         | ...    | ...        |

## Risque le plus critique et plan d'action

[Le risque qui, s'il se réalise, coûte le plus cher à corriger a posteriori. Une phrase de
plan d'action concret, pas une intention vague.]
```

## Critères de réussite mesurables

- Chaque famille (sécurité, coûts, données personnelles) contient au moins deux risques
  identifiés, avec probabilité, impact et mitigation renseignés pour chacun : une ligne
  laissée à "..." ne compte pas comme un risque traité.
- Le risque de comptage de capacité en accès concurrent (voir le brief du capstone,
  `../12-CAPSTONE-ARENA/02-briefing.md`) figure explicitement dans la famille Sécurité,
  puisqu'il découle d'un incident réel avec un contrôle des pompiers.
- Le partage de données d'adhérents entre les trois salles indépendantes figure explicitement
  dans la famille Données personnelles, avec une mitigation nommée (consentement, séparation
  des accès, ou autre).
- La section "Risque le plus critique et plan d'action" désigne un seul risque, avec une
  action concrète et datée, pas une liste de bonnes intentions.
- Le fichier est signé et daté avant la présentation finale du capstone.

## Ce qui invalide le challenge

- Une revue de risques générique, copiée d'un modèle externe sans adaptation au projet réel
  des trois salles d'escalade.
- Une mitigation qui répète l'impact sans proposer d'action ("le risque est fort donc il faut
  faire attention" n'est pas une mitigation).
- Une revue non signée, ou signée mais non datée.

## Lien vers le capstone qui la consomme

Ce livrable est intégré tel quel dans l'arborescence de livraison du capstone, voir
`../12-CAPSTONE-ARENA/03-deliverables.md`, et évalué comme critère éliminatoire dans
`../12-CAPSTONE-ARENA/04-evaluation-grid.md`. Produis-le pendant le jalon 3 (V1 et preuve) du
capstone, voir `../12-CAPSTONE-ARENA/challenge.md`, jamais après la présentation finale.

## Durée cible

9 h, cohérent avec la durée annoncée pour ce niveau dans CURRICULUM.md. Si tu dépasses
largement ce budget, c'est un signal à noter dans ta rétrospective de bloc, pas une raison
de bâcler la fin du livrable.
