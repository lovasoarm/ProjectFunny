# Grimoire : Niveau 10 : Team Quest

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| Working agreement | Ensemble de regles explicites et verifiables qu'une equipe adopte pour regler a l'avance les questions qui, sinon, se posent pour la premiere fois sous pression. | Chaque conflit redecouvre une regle qui n'existait que dans une seule tete, au pire moment. | Ton working agreement est-il ecrit, date, et relu recemment, ou existe-t-il seulement dans les tetes ? |
| Definition of Done | Liste verifiable de conditions qui definissent "fini" pour une tache, independante de l'impression de la personne qui l'a realisee. | Deux personnes appellent "fini" deux choses differentes, et le desaccord n'explose qu'a la livraison. | Ta definition de "fini" est-elle une liste verifiable, ou une impression personnelle ? |
| Trunk-based development | Flux Git ou tout le monde travaille a partir d'une branche unique, toujours deployable, avec des branches de travail tres courtes. | Des branches longues divergent et le merge final devient un conflit majeur. | Ton flux Git correspond-il a ton rythme de release reel, ou a une habitude recopiee ? |
| GitFlow | Flux Git avec branches separees pour le developpement en cours, les releases en preparation, et les correctifs urgents. | Une release freinee par une branche de developpement non isolee bloque un correctif urgent. | Pourquoi GitFlow plutot que trunk-based pour ton equipe et ton rythme de release ? |
| Feature flag | Condition dans le code qui active ou desactive une fonctionnalite sans nouveau deploiement, decouplant "code en production" de "fonctionnalite visible". | Une fonctionnalite non finie doit attendre un deploiement complet pour etre desactivee en urgence. | Comment desactiverais-tu une fonctionnalite en production sans redeployer ? |
| Escalade | Action de remonter un probleme a quelqu'un qui a le pouvoir de le resoudre quand on ne l'a pas soi-meme, avec des faits et une proposition, pas seulement un signalement de detresse. | Un blocage reste local trop longtemps, ou remonte sans faits ni proposition et ne se resout pas plus vite. | Quand escalades-tu exactement, et avec quoi dans les mains a ce moment-la ? |
| Qui decide quoi | Table qui fixe, pour chaque type de decision (implementation locale, architecture partagee, priorite produit, decision irreversible), qui tranche en cas de desaccord. | Un desaccord technique tourne en rond parce que personne ne sait qui a le dernier mot. | Qui decide, dans ton equipe, en cas de desaccord sur une decision irreversible ? |
| Signaux qu'un working agreement est mort | Plus personne ne le cite, il decrit une equipe ou un outillage qui n'existe plus, ou une regle y figure alors que tout le monde la contourne. | L'equipe suit des regles fantomes qui ne correspondent plus a la realite, sans le dire. | Quel signal montrerait que ton working agreement actuel est deja mort ? |

## Comportements evalues en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'échec |
| --- | --- | --- |
| Identification du vrai désaccord (justification par un mécanisme) | Tu identifies l'hypothèse vérifiable (fiabilité du capteur) qui devrait trancher | Tu discutes des deux solutions comme des préférences esthétiques |
| Gestion de la pression de la deadline (compromis nommé et assumé) | Tu proposes une vérification rapide ou une décision par défaut prudente si le temps manque | Tu forces une décision arbitraire juste pour "avancer" avant la release |
| Gestion humaine | Tu reconnais le coût du travail de chacun sans que ça t'empêche de choisir la meilleure décision technique disponible | Tu ignores la frustration de Malik et Inès, ou tu la calmes sans rien résoudre sur le fond |
| Réflexe systémique | Tu proposes un changement concret du working agreement pour éviter le prochain doublon | Tu règles seulement le conflit du jour |

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
