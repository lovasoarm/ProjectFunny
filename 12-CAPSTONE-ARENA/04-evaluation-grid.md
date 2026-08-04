# Grille d'évaluation chiffrée

## Principe

Chaque critère noté est sur 20. Le total est sur 140. Cette grille sert à l'auto-évaluation
(livrable 5 du niveau) et à toute revue externe ultérieure. Un score sans justification
écrite ne compte pas : la justification est ce qui prouve que tu comprends pourquoi tu te
notes ainsi, pas seulement que tu sais lister des points.

Deux critères sont éliminatoires : ils ne rapportent pas de points, mais leur absence rend le
capstone non validable, quel que soit le score obtenu sur le reste de la grille.

## Critères éliminatoires (à valider avant tout calcul de score)

| Critère éliminatoire                                       | Condition de passage                                                                                                       |
| -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| Arborescence de livraison conforme                          | La structure de dossier correspond exactement à celle décrite dans `03-deliverables.md`, sans fichier manquant ni renommé        |
| Revue de risques du Niveau 15 présente et signée            | `REVUE-DE-RISQUES.md` existe, couvre sécurité, coûts et données personnelles, et porte une signature datée                       |

```text
Un des deux critères éliminatoires manquant ou incomplet --> score final plafonné à 0/140,
quel que soit le détail du reste de la grille. Ce n'est pas une pénalité, c'est un blocage :
un livrable qui n'a pas la forme attendue ou qui n'a pas traité les risques du projet n'est
pas un capstone recevable, indépendamment de la qualité du code.
```

## Grille chiffrée (sur 120)

| Critère                                    | /20 | Ce qui est vérifié                                                                                                                                                                                                            |
| ------------------------------------------- | --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cadrage et gestion de l'ambiguïté          | 20  | Les hypothèses sont explicites, numérotées, classées par confiance. Le périmètre V1/V2 est justifié par un argument de valeur ou de risque, pas par facilité d'exécution.                                                     |
| Justesse architecturale                    | 20  | Les choix structurants sont justifiés avec au moins un compromis écarté nommé. Le modèle de données central résiste à une analyse de concurrence sur le point sensible du brief.                                              |
| Preuve de fonctionnement du point critique | 20  | Le comptage de capacité est testé automatiquement sous accès concurrent, pas seulement vérifié manuellement une fois. Le test échoue si on retire la protection de concurrence (vérifie-le en le désactivant volontairement). |
| Qualité de communication du livrable       | 20  | Une personne qui n'a pas suivi le projet peut comprendre le périmètre, l'architecture et les limites connues en lisant les documents seuls, sans explication orale.                                                           |
| Honnêteté sur les limites et la dette      | 20  | La roadmap post-V1 nomme précisément ce qui est fragile ou reporté, sans minimiser ni dramatiser. Aucune affirmation non vérifiée présentée comme un fait acquis.                                                             |
| Délégation à l'IA                          | 20  | `TDD_JOURNAL.md` ou l'auto-évaluation liste précisément ce qui a été délégué à une IA sur le jalon V1 (génération, suggestion, revue), ce qui ne l'a pas été, et pourquoi ce partage était le bon compromis pour ce projet.    |
| Protocole d'enquête (`HYPOTHESES.md`)      | 20  | Le bug documenté a été rendu déterministe avant toute correction. Au moins trois hypothèses falsifiables sont posées, dont au moins une explicitement réfutée par une expérience. Le correctif est prouvé par 200 exécutions consécutives sans échec du test qui échouait avant, pas par une seule exécution réussie.                |

## Coût du changement de spec (mesuré, pas noté séparément)

Le coût du changement de spec décrit dans `05-changement-de-spec.md` n'est pas un septième
critère noté sur 20 : il est la preuve exigée à l'intérieur des critères "Justesse
architecturale" et "Honnêteté sur les limites et la dette". Concrètement :

```text
Justesse architecturale        : le tableau de fichiers touchés (catégories A/B/C) doit
                                  figurer dans TDD_JOURNAL.md ; un diff de catégorie C
                                  large sans discussion fait perdre au moins 8 points ici
Honnêteté sur les limites      : le tableau d'heures (compréhension, modèle, métier,
                                  effets de bord) doit être présent et honnête ; son
                                  absence fait perdre au moins 6 points ici
```

Un capstone qui n'a mesuré ni fichiers touchés ni heures pour ce changement de spec ne peut
pas dépasser 12/20 sur "Justesse architecturale", même si le reste de l'architecture est
solide : parce que l'absence de mesure est elle-même le signe que la leçon du changement de
spec n'a pas été prise au sérieux.

## Délégation à l'IA : ce qui est attendu dans la justification

Le critère "Délégation à l'IA" ne juge pas la quantité d'assistance utilisée, il juge la
qualité de la décision de déléguer ou non. Une réponse recevable distingue au minimum :

```text
Délégué à l'IA          : ex. génération du squelette de tests répétitifs, relecture
                           orthographique du README, suggestions de noms de variables
Non délégué             : ex. modèle de données central, logique de comptage de capacité,
                           arbitrages de périmètre du cadrage
Pourquoi ce partage      : ex. "le comptage de capacité est le point sensible du brief,
                           je voulais comprendre chaque ligne moi-même avant de la défendre
                           à l'oral ; le squelette de tests répétitifs n'a pas cette valeur
                           d'apprentissage, déléguer là était le bon calcul de temps"
```

Une réponse qui dit seulement "j'ai utilisé une IA pour m'aider" ou "je n'ai pas utilisé
d'IA" sans cette décomposition ne peut pas dépasser 5/20 sur ce critère : l'absence de
distinction est elle-même un signal que la délégation n'a pas été pensée, seulement subie ou
évitée par principe.

Rappel : les jalons Cadrage et Architecture (voir `challenge.md`) sont sous jeûne d'IA
obligatoire et déclaré. Une délégation IA détectée sur ces deux jalons malgré une déclaration
de jeûne signée est une fraude à la déclaration, pas une simple perte de points : elle
invalide le capstone entier.

## Seuils

```text
< 84/140    -> le capstone n'est pas validé, reprendre le cadrage avant de continuer le code
84-104/140  -> validé avec réserve, identifier et corriger le critère le plus faible avant de
               présenter ce projet comme référence
105-125/140 -> validé, projet présentable en portfolio avec ses limites assumées à l'oral
126-140/140 -> validé avec excellence, ce niveau de rigueur est celui attendu en conditions
               professionnelles réelles sur un projet à enjeu de sécurité

Palier 105-125 et 126-140 : la revue de risques (REVUE-DE-RISQUES.md) doit être non seulement
présente (critère éliminatoire) mais aussi jugée complète par une relecture externe (trois
familles de risques couvertes, mitigations concrètes) pour que le score franchisse 105/140.
Une revue de risques minimaliste plafonne le score final à 104/140, même si le reste de la
grille dépasse ce seuil.
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
- Pourquoi les deux critères éliminatoires ne rapportent aucun point mais peuvent plafonner
  le score final à zéro : quelle différence de nature ça révèle par rapport aux critères
  notés sur 20.
- Comment tu utiliserais cette grille différemment si le brief n'avait contenu aucune
  contrainte de sécurité : quel critère perdrait alors son poids critique.
- Pourquoi une hypothèse réfutée dans `HYPOTHESES.md` compte pour le critère "Protocole
  d'enquête" au même titre qu'une hypothèse confirmée.
