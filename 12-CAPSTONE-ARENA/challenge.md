# Challenge : Le capstone

## Consigne

Traite le brief de `02-briefing.md` comme un mandat réel. Produis les livrables décrits dans
`03-deliverables.md`, dans l'ordre, sans sauter la note de cadrage pour foncer dans le code.
Le capstone est découpé en trois jalons évaluables, chacun avec un seuil de passage chiffré.
Tu ne démarres jamais le jalon suivant si le seuil du jalon précédent n'est pas atteint : un
jalon raté se corrige avant d'avancer, pas en compensant avec le jalon d'après.

```text
Jalon 1 : Cadrage            4 h   seuil >= 12/20
   |  (bloque le jalon 2 si non atteint)
   v
Jalon 2 : Architecture        8 h   seuil >= 12/20
   et modèle de données
   |  (bloque le jalon 3 si non atteint)
   v
Jalon 3 : V1 et preuve       20 h   seuil >= 60/100 (grille complète, 04-evaluation-grid.md)
```

Budget total indicatif : 32 heures, cohérent avec la fourchette de 25 à 40 heures annoncée
dans `README.md`. Si un jalon dépasse largement son budget, c'est un signal à traiter dans ta
note de cadrage ou ta roadmap, pas à ignorer.

## Jalon 1 : Cadrage (4 h, seuil 12/20)

### Jeûne d'IA obligatoire

Ce jalon se fait sans aucune assistance d'intelligence artificielle : ni pour formuler les
hypothèses, ni pour rédiger la note de cadrage, ni pour reformuler le besoin. Le cadrage est
l'endroit où tu apprends à trancher seul face à l'ambiguïté ; une IA qui reformule à ta place
masque exactement la compétence testée ici.

Avant de commencer, écris et signe dans ton dépôt un fichier `DECLARATION-JEUNE-IA.md` :

```text
# Déclaration de jeûne d'IA

Jalon concerné : Cadrage
Date de début : [date]
Date de fin : [date]
Je déclare ne pas avoir utilisé d'assistance IA (génération de texte, reformulation,
suggestion de structure) pour produire la note de cadrage de ce jalon.
Signature : [ton nom]
Date de signature : [date]
```

### Livrable du jalon

- Note de cadrage (1 à 2 pages), conforme au Livrable 1 de `03-deliverables.md`.

### Grille du jalon (sur 20)

| Critère                                          | Points | Ce qui est vérifié                                                            |
| ------------------------------------------------- | ------ | ------------------------------------------------------------------------------ |
| Hypothèses explicites, numérotées, classées       | 8      | Chaque hypothèse a un niveau de confiance justifié, pas juste affirmé          |
| Périmètre V1 justifié                             | 8      | Le report de fonctionnalités s'appuie sur un argument de valeur ou de risque   |
| Déclaration de jeûne d'IA présente et signée      | 4      | Le fichier existe, est daté avant le début du cadrage, et signé               |

### Seuil de passage

```text
< 12/20  → jalon non validé, reprendre le cadrage avant d'ouvrir le jalon 2
>= 12/20 → jalon validé, tu peux ouvrir le jalon Architecture
```

## Jalon 2 : Architecture et modèle de données (8 h, seuil 12/20)

### Jeûne d'IA obligatoire

Ce jalon se fait également sans assistance IA, en particulier pour la conception du modèle
de données. Le modèle qui garantit le comptage de capacité sans race condition est le point
le plus sensible du brief : c'est une compétence de raisonnement, pas de génération de
schéma. Rédige et signe un second `DECLARATION-JEUNE-IA.md` (ou ajoute une section datée au
fichier existant) couvrant ce jalon, avec la même structure que celle du jalon 1.

### Livrable du jalon

- Architecture (schéma + justification) et modèle de données central, conformes au
  Livrable 2 de `03-deliverables.md`.

### Grille du jalon (sur 20)

| Critère                                        | Points | Ce qui est vérifié                                                                   |
| ------------------------------------------------ | ------ | --------------------------------------------------------------------------------------- |
| Choix structurants justifiés                    | 8      | Au moins un compromis explicite écarté, avec la raison de l'avoir écarté               |
| Modèle de données résiste à la concurrence      | 8      | Le comptage de capacité est modélisé pour éviter la race condition, pas juste supposé  |
| Déclaration de jeûne d'IA présente et signée    | 4      | Datée avant le début de ce jalon, distincte de celle du jalon 1                        |

### Seuil de passage

```text
< 12/20  → jalon non validé, revoir l'architecture avant d'ouvrir le jalon V1
>= 12/20 → jalon validé, tu peux ouvrir le jalon V1 et preuve
```

Note : c'est immédiatement après ce jalon que le changement de spec décrit dans
`05-changement-de-spec.md` tombe. Traite-le avant de considérer le jalon 2 comme
définitivement clos si tu veux mesurer honnêtement son coût.

## Jalon 3 : V1 et preuve (20 h, seuil 60/100)

À partir de ce jalon, l'assistance IA est autorisée, à condition de la déclarer précisément
dans le Livrable 5 (auto-évaluation) : ce qui a été délégué, ce qui ne l'a pas été, et
pourquoi. Voir le critère "Délégation à l'IA" de `04-evaluation-grid.md`.

### Livrables du jalon

- Version 1 fonctionnelle (Livrable 3), dossier de suite (Livrable 4), auto-évaluation
  (Livrable 5), arborescence de livraison conforme (`03-deliverables.md`), revue de risques
  du niveau 15 (`REVUE-DE-RISQUES.md`).

### Grille du jalon

La grille complète de `04-evaluation-grid.md` s'applique ici, sur son total ajusté.

### Seuil de passage

```text
< 60/100  → capstone non validé
>= 60/100 → capstone validé, voir les paliers détaillés dans 04-evaluation-grid.md
```

## Livrable final

L'ensemble des documents/artefacts des trois jalons, réunis dans un dossier unique conforme à
l'arborescence de `03-deliverables.md`, plus une courte présentation orale ou écrite
(10 minutes maximum) que tu pourrais faire devant un vrai client, en assumant explicitement
ce qui reste incertain.

## Critères de réussite mesurables

- Les trois jalons sont franchis dans l'ordre, chacun avec son seuil atteint avant l'ouverture
  du suivant, vérifiable par l'historique de commits si tu codes en public.
- Les déclarations de jeûne d'IA des jalons 1 et 2 existent, sont datées avant le début du
  jalon concerné, et signées.
- Le test automatisé de concurrence sur le comptage de capacité existe, passe, et échoue
  volontairement si tu commentes la protection de concurrence : documente ce test négatif
  comme preuve.
- L'auto-évaluation atteint au moins 60/100 sur la grille du jalon 3, avec une justification
  écrite pour chaque critère, y compris ceux notés au maximum.
- La roadmap post-V1 contient au minimum trois items priorisés avec une estimation d'effort
  chacun.
- L'arborescence de livraison et la revue de risques sont présentes, conformément à
  `03-deliverables.md`.

## Ce qui invalide le challenge

- Ouvrir le jalon Architecture avant d'avoir atteint 12/20 sur le jalon Cadrage.
- Ouvrir le jalon V1 avant d'avoir atteint 12/20 sur le jalon Architecture.
- Une déclaration de jeûne d'IA absente, non datée, ou non signée sur les jalons 1 et 2.
- Commencer le code avant d'avoir écrit la note de cadrage.
- Livrer un système qui "marche à la main" sans preuve automatisée sur le point de sécurité
  du brief.
- Une auto-évaluation qui s'attribue le score maximal sur tous les critères sans
  justification différenciée : c'est un signal que l'exercice d'auto-évaluation n'a pas été
  fait sérieusement.
