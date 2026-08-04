# Grimoire : Day-to-Legend

Ouvre ce mémo quand tu doutes de ta progression, ou juste avant de préparer un dossier de
promotion. Il rappelle les leviers d'entraînement, pas le cours complet sur la progression.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Pratique délibérée | Entraînement ciblé, juste au-delà de la zone de confort, avec feedback rapide et spécifique. | `echo "kata du jour: justifier une decision reelle en 4 lignes" >> kata.md` | course en montagne / atelier de menuiserie |
| Boucle de feedback fermée | Correction basée uniquement sur son propre jugement, qui plafonne vite la progression. | `git log --author="moi" --grep="revu par" --oneline | wc -l` | urgences d'hôpital / régie technique de spectacle |
| Zone d'apprentissage efficace | Difficulté juste au-delà du confortable, ni trop facile ni trop dure. | `echo "difficulte ressentie: 6/10" >> kata.md # vise 6-7, pas 2 ni 10` | course en montagne / navigation maritime |
| Plateau de progression | Stagnation malgré un travail continu, causée par zone de confort, boucle fermée ou mauvais calibrage. | `diff <(cat competences-2025.md) <(cat competences-2026.md)` | atelier de menuiserie / urgences d'hôpital |
| Portfolio de preuves | Artefacts vérifiables (ADR, post-mortem, code documenté) attestant une compétence réelle. | `git log --author="moi" --grep="ADR" --oneline` | régie technique de spectacle / navigation maritime |
| Kata de décision | Exercice court et régulier : contexte, décision, justification, compromis, ce qui ferait changer d'avis. | `echo "contexte / decision / justification / compromis / contre-preuve" >> kata-$(date +%F).md` | course en montagne / cuisine de restaurant en service |
| Journal de décision | Notation quotidienne courte de la décision du jour et de ce qu'on referait autrement. | `echo "$(date +%F): decision=X, hesitation=Y, autrement=Z" >> journal.md` | urgences d'hôpital / atelier de menuiserie |
| Checklist annuelle de vérité | Quatre questions posées une fois par an ; trois ou quatre "non" signalent un plateau à traiter. | `grep -c "non" checklist-annuelle-2026.md` | navigation maritime / course en montagne |

## Défense orale

Pour la grille complète et chiffrée, va voir [./boss-fight.md](./boss-fight.md). Voici la matière
reformulée pour t'entraîner à l'oral.

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Reconstruire honnêtement un historique | Enjoliver le passé sans base vérifiable se retourne contre toi dès qu'on creuse. | Sur quelles traces concrètes (commits, tickets) t'appuies-tu pour prouver une compétence passée ? |
| Choisir avec un critère explicite | Choisir "ce qui impressionne" plutôt que ce qui a un vrai impact affaiblit ta démonstration. | Quel critère explicite justifie le choix de tes trois décisions mises en avant ? |
| Proposer un cadre juste pour tous | Une grille conçue pour t'avantager seul se retourne contre toi si elle est perçue comme une manoeuvre. | En quoi ta proposition de grille profite-t-elle aussi à tes collègues en lice ? |
| Assumer ses limites | Bluffer une expérience ou une régularité qui n'existe pas se découvre tôt ou tard. | Que refuses-tu explicitement de prétendre avoir accompli en un mois ? |

## Routine minimale (résumé)

```text
Lecture de code    10-15 min/jour   --> une phrase de synthese : "ce qu'eux ont choisi et pourquoi"
Kata de decision   10-15 min, 3-4x/sem --> contexte + decision + justification + compromis + ce qui te ferait changer d'avis
Journal            5 min/jour       --> decision du jour / hesitation / ce que je referais autrement
```

## Checklist annuelle de vérité (à se poser une fois par an)

- [ ] Ai-je pris une décision technique risquée cette année, avec un vrai enjeu derrière ?
- [ ] Existe-t-il quelqu'un dont j'apprends encore régulièrement dans mon contexte actuel ?
- [ ] Puis-je citer une compétence nouvelle acquise, au-delà de la familiarité avec l'existant ?
- [ ] Mon portfolio de preuves a-t-il grandi cette année ?

3-4 "non" --> plateau à traiter maintenant, pas dans un an.

## Les trois causes d'un plateau, et l'action associée

```text
Zone de confort deguisee   --> augmenter legerement la contrainte du kata (chiffrer, justifier)
Absence de reference       --> trouver une revue humaine ou une comparaison a un cas documente
Mauvais niveau de difficulte --> recalibrer, viser l'inconfort gerable, pas l'inconnu total
```

## Heuristique de secours

Quand tu doutes de ta progression : "qu'est-ce que j'ai fait cette semaine qui m'aurait
gêné il y a six mois, et est-ce que ça me gêne encore aujourd'hui ?" Si la réponse est
"je n'ai rien fait de tel", c'est le signal, pas l'introspection vague.

## Si tu rates le boss-fight

Relis la section sur le portfolio de preuves et sur les trois causes de plateau avant de
retenter. Refais l'exercice en listant d'abord, par écrit, cinq traces vérifiables de ton
historique réel (commits, tickets, incidents) avant de composer ton plan. Donne-toi
48 heures. Si le score reste sous 50/100, ou si l'honnêteté sur les limites reste sous
12/30, remonte au niveau amont sur les compromis nommés et assumés avant de revenir ici.
