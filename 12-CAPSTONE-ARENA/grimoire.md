# Grimoire — Capstone Arena

## Ordre de production, jamais dans le désordre

```text
1. Note de cadrage (hypothèses explicites + périmètre justifié)
2. Architecture (schéma + compromis assumés)
3. Version 1 fonctionnelle (avec preuve automatisée sur le point sensible)
4. Roadmap post-V1
5. Auto-évaluation chiffrée
```

## Détecter la fonctionnalité non négociable dans un brief flou

```text
Cherche les phrases qui mentionnent :
- un incident déjà vécu ("on a eu un souci l'an dernier avec...")
- une obligation externe (contrôle, audit, réglementation, contrat)
- une conséquence concrète en cas d'échec (sécurité, argent, réputation)
Ces phrases signalent la contrainte non négociable, même noyée dans un message informel.
```

## Le test qui prouve, pas qui rassure

```text
Une vérification manuelle prouve que ça marche une fois, dans les conditions où tu as testé.
Un test automatisé de concurrence prouve que ça résiste dans le cas qui casse vraiment.
Sur un point signalé comme sensible dans le brief, seule la deuxième preuve compte.
```

## Grille chiffrée — rappel des seuils

```text
< 60   non validé
60-74  validé avec réserve
75-89  validé
90-100 excellence
```

## Réflexe face à un pivot de dernière minute

```text
1. Diagnostiquer précisément ce qui casse (pas une impression globale de catastrophe).
2. Séparer panique et impact réel.
3. Proposer une option concrète (ajuster / reporter / renégocier), jamais un simple constat.
4. Communiquer au client en langage client, sans minimiser ni dramatiser.
```
