# Les livrables

## Principe

Un livrable capstone n'est pas "du code qui tourne sur ta machine". C'est un ensemble de
preuves vérifiables par quelqu'un qui n'a jamais suivi ton raisonnement. Chaque livrable
ci-dessous doit pouvoir être jugé sans que tu sois présent pour l'expliquer oralement.

## Livrable 1 — Note de cadrage (1 à 2 pages)

- Reformulation du besoin en une phrase, validée par une relecture du brief.
- Périmètre de la version 1 : ce qui est dedans, ce qui est explicitement reporté, et
  pourquoi (avec l'argument de coût d'opportunité du Niveau 03).
- Les hypothèses explicites que tu as posées face à l'ambiguïté du brief, numérotées, avec
  leur niveau de confiance (haute, moyenne, faible).
- Une estimation en fourchette du délai de la version 1, avec les sources d'incertitude
  nommées.

## Livrable 2 — Architecture (schéma + justification)

- Un schéma des grands composants (frontend, backend, base de données, services externes
  éventuels) au niveau du Niveau 06.
- La justification des choix structurants : pourquoi cette base de données, pourquoi cette
  découpe en services ou modules, avec au moins un compromis explicite écarté et la raison
  de l'avoir écarté.
- Le modèle de données central (schéma ou diagramme), avec une attention particulière au
  modèle qui garantit le comptage de capacité en temps réel sans race condition.

## Livrable 3 — Version 1 fonctionnelle

- Un système déployé et accessible (même en environnement de démonstration), pas seulement
  en local, couvrant au minimum : compte adhérent partagé entre les trois salles, création de
  créneaux par salle, réservation avec comptage de capacité fiable sous accès concurrent.
- Un jeu de données de démonstration réaliste (trois salles, adhérents, créneaux) qui permet
  de tester le scénario de bout en bout sans configuration manuelle supplémentaire.
- Au moins un test automatisé qui vérifie explicitement que le comptage de capacité résiste à
  deux réservations simultanées sur le dernier créneau disponible — c'est le point le plus
  sensible du brief, il doit être prouvé, pas juste affirmé.

## Livrable 4 — Dossier de suite (roadmap post-V1)

- Liste priorisée de ce qui a été reporté (cours collectifs, prêt de matériel, autres) avec
  une estimation grossière de l'effort pour chaque item.
- Liste des dettes techniques assumées consciemment pendant la V1, avec le risque associé si
  elles ne sont jamais traitées.
- Une proposition de ce que tu ferais pendant la "saison calme en été" mentionnée par le
  client, en lien direct avec les items reportés.

## Livrable 5 — Auto-évaluation

- Ta propre notation sur la grille de `04-evaluation-grid.md`, remplie avant toute revue
  externe, avec une justification écrite pour chaque critère où tu ne t'attribues pas le
  score maximal.

## Ce que tu dois savoir défendre

- Pourquoi le test de concurrence sur le comptage de capacité est un livrable obligatoire et
  non une amélioration optionnelle, compte tenu du contexte du brief.
- Pourquoi une note de cadrage écrite avant le code change la qualité du code produit
  ensuite.
- Ce qui distingue, dans ta roadmap post-V1, une dette technique assumée d'un report de
  fonctionnalité — et pourquoi les deux n'appellent pas le même traitement.
