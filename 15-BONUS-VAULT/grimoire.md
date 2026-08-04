# Grimoire : Bonus Vault

## Choisir un format de décision

```text
Réversible, une personne          --> rien d'écrit, ou une ligne de commit
Structurant, équipe restreinte    --> ADR
Traverse plusieurs équipes        --> RFC
Doit convaincre une audience non technique --> one-pager
```

## ADR : squelette minimal

```text
# ADR-NNNN : [titre court, verbe d'action]
## Statut (proposée / acceptée / dépréciée) : date
## Contexte (contraintes connues au moment de la décision)
## Décision (une phrase, sans ambiguïté)
## Conséquences (+ bénéfices, - coûts réels, jamais uniquement des +)
```

## Checklists : les quatre moments à risque

```text
Mise en prod   --> rollback testé, fenêtre sans chevauchement, métriques définies avant
Revue de code  --> cas limites couverts, tests qui échouent sur l'ancien code
Incident       --> impact mesuré avant cause, communication avant solution complète
Onboarding     --> accès prêts avant J1, première tâche à faible risque sous 2-3 jours
```

## Trouver une ressource durable

```text
Question filtre : explique-t-elle POURQUOI une contrainte existe,
ou seulement COMMENT contourner la contrainte avec l'outil du moment ?

Sources qui durent : livres de mécanismes, post-mortems publics, papiers fondateurs,
documentation de standards, code source de projets reconnus et anciens.
```

## Anti-patterns : reconnaissance rapide

```text
God Object              --> un fichier que plus personne ne comprend en entier
Copier-Coller Métier    --> même règle codée à plusieurs endroits, jamais synchronisée
Faux Consensus          --> réunion "d'accord" sans décision formulée explicitement
Test Alibi              --> test qui vérifie l'absence de crash, pas la valeur correcte
Dette Technique Silencieuse --> raccourci jamais nommé, jamais budgété pour remboursement
Héros Systémique        --> une seule personne indispensable pour comprendre le système
Yak Shaving Involontaire --> le sous-problème avale le problème d'origine
Cargo Cult Architectural --> copier la forme d'une architecture sans le contexte qui la justifie
```

## Principe transversal

```text
Un format, une checklist, une ressource ou un pattern n'a de valeur que si son coût
d'usage est inférieur au coût de l'erreur qu'il prévient. Mesure les deux avant d'adopter
un outil de ce coffre par principe.
```
