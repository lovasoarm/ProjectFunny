# Grimoire : Capstone Arena

Ouvre ce mémo juste avant de livrer ton capstone, ou quand un pivot de dernière minute tombe.
Il rappelle l'ordre de production et les réflexes, pas le cours complet du niveau.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Note de cadrage | Hypothèses explicites et périmètre justifié, écrits avant toute ligne de code. | `echo "Hypothese: toutes les salles partagent le meme fuseau horaire" >> cadrage.md` | course en montagne / navigation maritime |
| Architecture assumée | Schéma avec compromis nommés, pas une liste de technologies choisies par habitude. | `# schema.md : composants + fleches de dependance, chaque fleche justifiee` | atelier de menuiserie / régie technique de spectacle |
| Preuve automatisée sur point sensible | Test qui prouve la résistance sur le cas signalé comme critique dans le brief, pas une vérification manuelle. | `npx artillery run charge-reservation.yml` | urgences d'hôpital / navigation maritime |
| Roadmap post-V1 | Liste ordonnée de ce qui reste à faire, avec justification de l'ordre choisi. | `echo "1. multi-fuseau 2. notifications 3. export comptable" >> roadmap.md` | course en montagne / cuisine de restaurant en service |
| Auto-évaluation chiffrée | Notation honnête de sa propre copie contre la grille, avant la correction externe. | `echo "diagnostic:28/30 proposition:20/25 communication:22/25" >> auto-eval.txt` | urgences d'hôpital / atelier de menuiserie |
| Fonctionnalité non négociable | Exigence cachée dans un brief flou, révélée par un incident passé ou une obligation externe. | `rg -i "audit|reglement|incident|obligation" brief.md` | régie technique de spectacle / navigation maritime |
| Test qui prouve vs test qui rassure | Une vérification manuelle rassure une fois ; un test automatisé de concurrence prouve la résistance réelle. | `npx artillery run --config concurrency-reservation.yml` | urgences d'hôpital / course en montagne |
| Diagnostic de pivot | Distinguer ce qui casse réellement de ce qui semble casser dans un changement de dernière minute. | `rg -n "timezone|fuseau" --type ts | wc -l` | navigation maritime / atelier de menuiserie |

## Défense orale

Pour la grille complète et chiffrée, va voir [./boss-fight.md](./boss-fight.md). Voici la matière
reformulée pour t'entraîner à l'oral.

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Diagnostic technique précis | Sans localiser où l'hypothèse implicite est enterrée dans le code, tu paniques sur tout le système. | Où, précisément, ton système suppose-t-il implicitement un fuseau horaire unique ? |
| Distinction panique et impact réel | Traiter tout comme cassé fait perdre un temps qui manquera ailleurs. | Quelle partie de ton système reste intacte malgré le changement annoncé ? |
| Proposition réaliste | Une proposition qui ignore le délai restant n'est pas utilisable par le client. | Quel compromis (report, effort supplémentaire, délai) proposes-tu et pourquoi celui-là ? |
| Communication honnête au client | Minimiser ou dramatiser fait perdre confiance des deux côtés. | Que dirais-tu au client en une phrase sans jargon technique ? |

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

## Grille chiffrée : rappel des seuils

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

## Si tu rates le boss-fight

Relis ta note de cadrage et la section sur les hypothèses implicites avant de retenter.
Refais l'exercice en localisant d'abord dans le code, avec une recherche exécutée, tous les
endroits où l'hypothèse cassée est enterrée, avant de rédiger ta réponse au client. Donne-toi
48 heures. Si le score reste sous 70/100, remonte au niveau amont sur les compromis nommés et
assumés avant de revenir affronter ce pivot.
