# Grimoire : Niveau 10 : Team Quest

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
                     release fréquente        release espacée / réglementée
équipe petite        trunk-based +            GitFlow allégé (sans toutes
(2-4 personnes)      feature flags            les branches intermédiaires)

équipe plus grande   trunk-based +             GitFlow complet, avec branches
(5+ personnes)       feature flags,            de release et hotfix dédiées
                      discipline de
                      découpage stricte
```

## Arbre : dois-je escalader maintenant ?

```text
Le problème peut-il être résolu par les personnes
directement impliquées, avec l'info disponible ?
        │
   ┌────┴────┐
  oui        non
   │          │
Résoudre    Le coût d'attendre dépasse-t-il le coût
localement  de déranger quelqu'un au-dessus ?
                    │
              ┌─────┴─────┐
             oui          non
              │            │
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

## Vocabulaire

- **Working agreement** : ensemble de règles explicites et vérifiables qu'une équipe adopte
  pour régler à l'avance les questions qui, sinon, se posent pour la première fois sous
  pression.
- **Definition of Done** : liste vérifiable de conditions qui définissent "fini" pour une
  tâche, indépendante de l'impression de la personne qui l'a réalisée.
- **Trunk-based development** : flux Git où tout le monde travaille à partir d'une branche
  unique, toujours déployable, avec des branches de travail très courtes.
- **GitFlow** : flux Git avec branches séparées pour le développement en cours, les
  releases en préparation, et les correctifs urgents.
- **Feature flag** : condition dans le code qui active ou désactive une fonctionnalité sans
  nouveau déploiement, découplant "code en production" de "fonctionnalité visible".
- **Escalade** : action de remonter un problème à quelqu'un qui a le pouvoir de le résoudre
  quand on ne l'a pas soi-même, avec des faits et une proposition, pas seulement un
  signalement de détresse.

## Phrase à ressortir en cas de désaccord qui monte

"Est-ce qu'on discute d'une préférence, ou d'un fait qu'on peut vérifier avant de trancher ?"
