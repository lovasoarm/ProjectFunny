# Grimoire : Team Quest

Ouvre ce mémo avant une réunion qui sent le conflit, ou juste après. Il te donne les réflexes de
coordination d'équipe, pas le cours complet sur le travail en groupe.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Working agreement | Règles explicites et vérifiables adoptées à l'avance pour régler ce qui, sinon, se joue sous pression. | `git log -1 --format="%ad" -- working-agreement.md # date de derniere revision` | atelier de menuiserie / régie technique de spectacle |
| Definition of Done | Liste vérifiable de conditions pour appeler une tâche "finie", indépendante de l'impression de son auteur. | `- [ ] tests verts\n- [ ] revue approuvee\n- [ ] deploye en staging\n- [ ] documente` | cuisine de restaurant en service / urgences d'hôpital |
| Trunk-based development | Flux Git où tout le monde part d'une branche unique, toujours déployable, avec des branches courtes. | `git checkout main && git pull && git checkout -b fix-alerte-temp && git push -u origin fix-alerte-temp` | navigation maritime / course en montagne |
| GitFlow | Flux Git avec branches séparées pour développement, releases en préparation et correctifs urgents. | `git checkout -b release/2026.03 develop` | atelier de menuiserie / régie technique de spectacle |
| Feature flag | Condition dans le code qui active ou désactive une fonctionnalité sans nouveau déploiement. | `if (flags.isEnabled("alerte_temperature_v2", unit.id)) { return computeV2(unit); }` | régie technique de spectacle / urgences d'hôpital |
| Escalade | Remonter un problème à quelqu'un qui a le pouvoir de le résoudre, avec des faits et une proposition. | `# message d'escalade type\necho "Blocage: X depuis 4h. Impact: Y. Proposition: Z. Besoin: validation avant 17h."` | course en montagne / urgences d'hôpital |
| Qui décide quoi | Table qui fixe, par type de décision, qui tranche en cas de désaccord. | `INSERT INTO decisions_owners (type, owner) VALUES ('architecture_partagee', 'lead-tech');` | navigation maritime / cuisine de restaurant en service |
| Signaux qu'un working agreement est mort | Plus personne ne le cite, ou une règle y figure alors que tout le monde la contourne. | `grep -c "trunk-based" working-agreement.md; git log --all --oneline | grep -c "hotfix direct sur main"` | atelier de menuiserie / course en montagne |

## Défense orale

Pour la grille complète et chiffrée, va voir [./boss-fight.md](./boss-fight.md). Voici la matière
reformulée pour la préparer à l'oral.

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Trouver le vrai désaccord | Sans distinguer fait vérifiable et préférence, la discussion tourne en rond entre deux egos. | Quelle hypothèse vérifiable devrait trancher ton dernier désaccord technique ? |
| Tenir la pression de deadline | Forcer une décision arbitraire pour "avancer" fait porter le risque à plus tard, plus cher. | Que proposes-tu quand le temps manque pour vérifier avant une échéance ferme ? |
| Reconnaître le coût humain | Ignorer le travail déjà fourni par chacun casse la confiance même si la décision technique est juste. | Comment reconnais-tu l'effort de quelqu'un sans pour autant garder sa solution si elle est moins bonne ? |
| Réflexe systémique | Ne régler que le conflit du jour garantit que le même doublon revient. | Quel changement du working agreement proposes-tu pour éviter que ce conflit se reproduise ? |

## La règle en une phrase

Une règle qui n'est pas écrite n'existe que dans une seule tête à la fois, et elle ne se
révèle qu'au moment du conflit.

## Checklist avant de démarrer une collaboration

- [ ] La définition de "fini" est écrite, sous forme de liste vérifiable, pas d'impression.
- [ ] La table "qui décide quoi" existe pour au moins : implémentation locale, architecture
      partagée, priorité produit, décision irréversible.
- [ ] Un délai de revue de pull request est fixé en heures ou jours, pas en intention.
- [ ] Un seuil de signalement de retard est fixé ("plus de X heures de retard = signal
      immédiat").
- [ ] Une règle de coordination sur les fichiers partagés est écrite.
- [ ] Le flux Git choisi correspond au rythme de release réel, pas à une habitude.
- [ ] Le working agreement porte une date de rédaction et une date de révision.

## Matrice de choix de flux Git

```text
                     release frequente        release espacee / reglementee
equipe petite        trunk-based +            GitFlow allege (sans toutes
(2-4 personnes)      feature flags            les branches intermediaires)

equipe plus grande   trunk-based +             GitFlow complet, avec branches
(5+ personnes)       feature flags,            de release et hotfix dediees
                      discipline de
                      decoupage stricte
```

## Arbre : dois-je escalader maintenant ?

```text
Le probleme peut-il etre resolu par les personnes
directement impliquees, avec l'info disponible ?
        |
   +----+----+
  oui        non
   |          |
Resoudre    Le cout d'attendre depasse-t-il le cout
localement  de deranger quelqu'un au-dessus ?
                    |
              +-----+-----+
             oui          non
              |            |
         Escalader     Documenter, fixer une
         avec faits +  date limite avant
         proposition   escalade automatique
```

## Structure d'un refus qui préserve la relation

1. Reconnaître le besoin réel derrière la demande.
2. Expliquer le mécanisme du coût si on dit oui quand même (pas juste "c'est risqué").
3. Proposer une alternative concrète, datée.

## Structure d'un désaccord technique productif

1. Formuler sa position avec la raison concrète derrière.
2. Demander explicitement la raison derrière la position de l'autre.
3. Chercher l'hypothèse ou la contrainte non partagée qui explique l'écart.
4. Si c'est vérifiable, proposer de le vérifier avant de trancher.
5. Si c'est un vrai choix de valeurs, se référer au "qui décide quoi" du working agreement.

## Signaux qu'un working agreement est mort

- Plus personne ne le cite, même en cas de désaccord.
- Il décrit une équipe ou un outillage qui n'existe plus.
- Une règle y figure alors que tout le monde la contourne systématiquement sans le dire.

## Phrase à ressortir en cas de désaccord qui monte

"Est-ce qu'on discute d'une préférence, ou d'un fait qu'on peut vérifier avant de trancher ?"

## Si tu rates le boss-fight

Relis la leçon sur "qui décide quoi" et celle sur l'escalade avant de retenter. Refais
l'exercice en identifiant d'abord, par écrit, l'hypothèse vérifiable qui sépare les deux
positions en jeu, avant de rédiger ta prise de parole. Donne-toi 48 heures, pas plus. Si le
score reste sous 50/100, remonte au niveau amont sur la distinction entre fait vérifiable et
préférence avant de revenir sur ce boss-fight.
