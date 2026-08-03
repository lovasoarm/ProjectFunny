# Boss Fight : L'entretien de promotion sans métrique claire

## La situation

Tu es développeur confirmé depuis deux ans et demi dans une entreprise qui édite un logiciel
de gestion de bibliothèque pour clubs sportifs. Tu demandes une promotion vers un poste
senior. Ton manager te répond que l'entreprise n'a pas de grille de compétences formalisée
pour ce passage : la décision se fera "au ressenti du comité de direction". Tu sais que deux
autres collègues, avec moins d'ancienneté mais plus visibles en réunion, sont aussi en lice.
Tu as un mois avant l'entretien de décision. Tu n'as jamais tenu de portfolio de preuves ni
de journal jusqu'ici.

## Les contraintes réelles

- Aucune grille de compétences écrite n'existe dans l'entreprise ; le comité décide sur
  impression générale et témoignages oraux de managers et pairs.
- Tu as, dans ton historique de travail, plusieurs décisions techniques solides mais jamais
  documentées formellement (choix de modélisation, un incident résolu seul un soir, un
  refus argumenté d'une demande mal posée d'un client interne).
- Un mois est trop court pour bâtir un historique de douze semaines de routine complète.
- Les deux collègues concurrents sont plus à l'aise à l'oral en réunion, ce qui influence
  fortement la perception du comité, indépendamment de la compétence réelle.

## Ce qu'on attend de toi

Produis un plan d'un mois (une page) qui :

1. Reconstruit rétroactivement un portfolio de preuves à partir de ton historique réel
   (git log, tickets fermés, messages Slack d'incident, emails) plutôt que d'attendre d'en
   avoir un "propre" : identifie précisément où chercher ces traces.
2. Choisit trois décisions passées à documenter en priorité sous forme d'ADR ou de
   post-mortem courts, avec le critère explicite de sélection (impact réel, pas
   ancienneté ni facilité à raconter).
3. Propose au manager une façon concrète de rendre la décision moins arbitraire pour tout le
   monde (par exemple une grille de critères minimale à trois axes, réutilisable aussi pour
   les collègues en lice), sans que la démarche paraisse une manœuvre gagnant seulement pour
   toi.
4. Assume explicitement ce qui ne peut pas être rattrapé en un mois (pas de véritable
   historique de routine longue) sans se dévaloriser ni bluffer sur une expérience qui
   n'existe pas.

## Grille d'évaluation

| Critère                             | Ce qui est évalué                                                                                                                                     |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| Reconstruction honnête du portfolio | Les preuves choisies s'appuient sur des faits vérifiables (commits, tickets, incidents réels), pas sur une reformulation flatteuse a posteriori       |
| Sélection des trois décisions       | Le critère de sélection est explicite et défendable, pas "les plus impressionnantes à raconter"                                                       |
| Proposition de grille au manager    | La grille profite objectivement à tous les candidats, pas seulement à toi : sinon elle sera perçue comme une manipulation et se retournera contre toi |
| Honnêteté sur les limites           | Le plan ne prétend pas rattraper en un mois ce qui demande douze semaines ; il assume la limite et mise sur la qualité plutôt que le volume           |

## Seuil de validation chiffré

| Critère | Points |
| --- | --- |
| Reconstruction honnête du portfolio | 25 |
| Sélection des trois décisions | 25 |
| Proposition de grille au manager | 20 |
| Honnêteté sur les limites | 30 |
| **Total** | **100** |

```text
< 50   --> boss-fight non valide, la scène est a refaire apres relecture de la lecon concernee
50-69  --> valide avec reserve, identifie le critere le plus faible avant de le compter comme acquis
70-89  --> valide, le reflexe est en place
90-100 --> valide avec excellence, ce niveau de justesse est celui attendu en situation reelle
```

Seuil de passage : 70/100. En dessous, le niveau n'est pas considéré comme acquis, même si le
texte rendu est bien écrit.

**Éliminatoire :** Si "Honnêteté sur les limites" est noté en dessous de 12/30, le total est plafonné à 50/100 : un portfolio qui enjolive ou tait ses limites vaut moins qu'un CV vide, parce qu'il ment à un futur employeur ou à toi-même sur ce que tu sais vraiment faire.
