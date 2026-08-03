# Grille d'évaluation chiffrée

## Principe

Chaque critère est noté sur 20. Le total est sur 100. Cette grille sert à l'auto-évaluation
(livrable 5 du niveau) et à toute revue externe ultérieure. Un score sans justification
écrite ne compte pas : la justification est ce qui prouve que tu comprends pourquoi tu te
notes ainsi, pas seulement que tu sais lister des points.

## Grille

| Critère                                    | /20 | Ce qui est vérifié                                                                                                                                                                                                            |
| ------------------------------------------ | --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cadrage et gestion de l'ambiguïté          | 20  | Les hypothèses sont explicites, numérotées, classées par confiance. Le périmètre V1/V2 est justifié par un argument de valeur ou de risque, pas par facilité d'exécution.                                                     |
| Justesse architecturale                    | 20  | Les choix structurants sont justifiés avec au moins un compromis écarté nommé. Le modèle de données central résiste à une analyse de concurrence sur le point sensible du brief.                                              |
| Preuve de fonctionnement du point critique | 20  | Le comptage de capacité est testé automatiquement sous accès concurrent, pas seulement vérifié manuellement une fois. Le test échoue si on retire la protection de concurrence (vérifie-le en le désactivant volontairement). |
| Qualité de communication du livrable       | 20  | Une personne qui n'a pas suivi le projet peut comprendre le périmètre, l'architecture et les limites connues en lisant les documents seuls, sans explication orale.                                                           |
| Honnêteté sur les limites et la dette      | 20  | La roadmap post-V1 nomme précisément ce qui est fragile ou reporté, sans minimiser ni dramatiser. Aucune affirmation non vérifiée présentée comme un fait acquis.                                                             |

## Seuils

```text
< 60   → le capstone n'est pas validé, reprendre le cadrage avant de continuer le code
60-74  → validé avec réserve, identifier et corriger le critère le plus faible avant de
         présenter ce projet comme référence
75-89  → validé, projet présentable en portfolio avec ses limites assumées à l'oral
90-100 → validé avec excellence, ce niveau de rigueur est celui attendu en conditions
         professionnelles réelles sur un projet à enjeu de sécurité
```

## Piège fréquent dans l'auto-évaluation

Se noter haut sur "preuve de fonctionnement" parce que "ça marche quand je teste à la main"
est l'erreur la plus commune de ce niveau. Sans test automatisé de concurrence explicite sur
le comptage de capacité, ce critère ne peut pas dépasser 10/20, quelle que soit la qualité
perçue du reste : parce que c'est précisément le point que le brief signale comme sensible
(l'incident avec les pompiers), et qu'une vérification manuelle ne prouve rien sous charge
réelle.

## Ce que tu dois savoir défendre

- Pourquoi une preuve manuelle du comptage de capacité ne suffit pas, même si elle rassure.
- Pourquoi la grille pénalise autant l'absence de justification écrite qu'un score
  objectivement bas sur un critère.
- Comment tu utiliserais cette grille différemment si le brief n'avait contenu aucune
  contrainte de sécurité : quel critère perdrait alors son poids critique.
