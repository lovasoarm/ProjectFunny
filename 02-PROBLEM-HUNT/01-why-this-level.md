# Pourquoi ce niveau existe

## Le piège

Le responsable d'un réseau de laveries automatiques te contacte : "on a besoin d'une appli
pour que les clients réservent leur machine à l'avance." Tu es motivé. Tu montes un système
de réservation de créneaux, avec calendrier, notifications, paiement anticipé. Trois semaines
de travail propre, testé, déployé. Un mois plus tard, l'usage réel : 4 réservations par jour
sur douze laveries, alors que 200 lavages ont lieu chaque jour sans réservation. Le vrai
problème n'était pas "réserver une machine". C'était "savoir de loin si une machine est libre
maintenant", parce que les clients arrivent souvent avec du linge sale plein les bras et
n'ont pas cinq minutes d'avance à planifier. Tu as livré une solution à un problème que
personne n'avait.

Deuxième exemple, trois mois plus tard, chez le même client : le responsable demande "un
tableau de bord avec plein de graphiques pour suivre l'activité". Tu livres, le 2026-02-09,
un tableau avec 11 graphiques différents. Consultation réelle mesurée sur les 30 jours
suivants : deux graphiques consultés, neuf jamais ouverts. Coût de développement des neuf
graphiques inutiles : environ 26 heures, soit plus de la moitié du budget du module.

## Pourquoi ce niveau existe

Une demande formulée par un client, un manager ou un product owner n'est presque jamais le
besoin réel. C'est une solution que la personne a déjà imaginée dans sa tête, habillée en
demande. Le rôle du développeur sénior n'est pas d'exécuter la solution imaginée, c'est de
remonter jusqu'au problème qu'elle est censée résoudre, puis de vérifier que la solution
proposée est effectivement la meilleure réponse à ce problème : parfois oui, souvent non.

Ce niveau existe parce que sans lui, tu deviens un excellent exécutant de mauvaises idées.
Et le pire, c'est que ton code sera propre, testé, bien architecturé : ce qui rend l'échec
invisible pendant longtemps, jusqu'à ce que les métriques d'usage tombent et que quelqu'un
demande "pourquoi personne n'utilise ça ?".

## Qui souffre en premier

Le client souffre en premier, financièrement : il paie un développement qui ne sert à rien
d'utile. Mais le développeur souffre juste après, en confiance perdue : après deux ou trois
livraisons "inutiles mais bien faites", plus personne ne demande son avis en amont, seulement
sa capacité d'exécution. C'est une perte silencieuse, qui ne s'annonce jamais comme telle.

## À quel moment du projet ça se manifeste

Ce risque est maximal dans les tout premiers jours d'un projet, au moment où la demande
initiale arrive, avant toute ligne de code. C'est aussi le moment où personne n'a envie de le
traiter, parce que l'envie de commencer à coder est la plus forte. Le symptôme apparaît
plusieurs semaines après, sous forme de métriques d'usage décevantes, jamais au moment de la
décision elle-même.

## Ce que tu sais faire à la sortie

- Reformuler une demande en problème sous-jacent, en une phrase vérifiable par la personne qui
  a formulé la demande.
- Identifier qui utilise réellement le produit, et distinguer cet utilisateur du client qui
  paie ou du manager qui commande.
- Définir un périmètre explicite et une liste de non-objectifs assumés pour un lot de travail.
- Poser au moins une métrique de succès mesurable avant de commencer à développer.
- Reconnaître dans une demande les signaux d'une solution déjà décidée à la place d'un besoin
  exprimé.

## Ce que ce niveau ne couvre pas

Ce niveau ne couvre pas comment découper le travail une fois le vrai besoin identifié, c'est
le rôle du [niveau 03](../03-MVP-SPLIT/README.md). Il ne couvre pas non plus comment traduire
ce besoin en parcours utilisateur détaillé, traité au [niveau 04](../04-USER-WIZARD/README.md).

## Qui souffre en deuxième, et pourquoi ça compte

Le manager qui a formulé la demande initiale souffre en deuxième : c'est lui qui doit
expliquer, en comité de pilotage, pourquoi un module fraîchement livré n'est pas utilisé. Il
a souvent intérêt, tout comme le développeur, à ce que le vrai besoin soit identifié tôt,
même si sur le moment il pousse pour une exécution rapide de son idée initiale.

## Prérequis réels

Avoir suivi le [niveau 01](../01-MINDSET/README.md) : sans le réflexe de rendre les
hypothèses explicites, la chasse au besoin réel tourne vite à la discussion d'opinions plutôt
qu'à une méthode.

## Erreurs de débutant les plus coûteuses

- Prendre la demande au pied de la lettre et la transformer directement en cahier des charges
  technique, sans jamais demander "pourquoi ce besoin existe".
- Confondre l'avis du client qui paie avec le besoin de l'utilisateur final : ce sont souvent
  deux personnes différentes, avec des intérêts qui ne coïncident pas totalement.
- Ajouter une métrique de succès après coup, une fois le produit livré, pour justifier une
  décision déjà prise plutôt que pour la valider avant.
- Croire qu'une longue liste de fonctionnalités demandées est un signe de sérieux du projet,
  alors qu'elle cache souvent l'absence de priorisation.

## Le mécanisme sous-jacent

Le mécanisme n'est pas "poser plus de questions". C'est remonter systématiquement d'un niveau
d'abstraction à chaque demande : de "je veux X" vers "parce que je veux résoudre Y" vers "Y se
mesure comment". Une demande qui résiste à cette remontée, c'est-à-dire dont on ne peut pas
extraire un Y mesurable, est un signal d'alerte, pas une demande à exécuter telle quelle.

```text
Demande formulee            Besoin reel                Solution correcte
"une appli de                "savoir si une machine     "un panneau lumineux + une
 reservation"        !=       est libre maintenant"  -->  appli qui affiche le statut
                                                           en direct, sans reservation"
```

## Analogie

Analogie : chasser le besoin réel derrière une demande, c'est comme le chef de cuisine qui
interroge le client "allergique aux fruits de mer" avant de proposer un plat de substitution,
et le skipper qui demande "vous voulez arriver vite ou confortablement" avant de choisir une
route en pleine mer.
Où l'analogie casse : le chef et le skipper obtiennent une réponse en une question, tout de
suite, de la bouche de la personne concernée. La chasse au besoin logiciel demande souvent
plusieurs allers-retours, avec des personnes qui ne savent pas elles-mêmes formuler leur
besoin avant de voir un premier essai.

## Contre-exemple : quand appliquer ce niveau serait une erreur

Une demande interne urgente, du type "corrige ce bug qui bloque la facturation depuis ce
matin", ne mérite pas une session de chasse au besoin réel : le besoin est déjà connu et
mesurable, l'urgence prime. Passer une heure à interroger le pourquoi d'un bug bloquant est
une perte de temps, pas de la rigueur.

## Le coût de l'apprentissage

Compter 3 à 5 heures de lecture, plus un exercice de reformulation d'une demande réelle qui
prend souvent plus de temps qu'attendu, entre 2 et 4 heures, parce qu'il faut aller chercher
de vraies personnes ou de vraies données d'usage, pas inventer des réponses.

## Comment savoir que tu maîtrises

Le signal observable : face à une nouvelle demande, ta première réaction spontanée est une
question sur le pourquoi, pas une estimation de durée. Si ta première question reste "combien
de temps ça va prendre", tu n'as pas encore intégré ce niveau.

## Ce que l'IA fait à ta place, et ce qu'elle ne fait pas

Une IA peut t'aider à reformuler une demande de dix façons différentes, à générer des
questions à poser au client, à structurer une liste de non-objectifs. Elle ne peut pas
observer, à ta place, comment les utilisateurs réels se comportent sur le terrain : elle
n'était pas dans le parking de la laverie pour voir les mains pleines de linge sale. La
chasse au besoin réel demande une observation humaine que l'IA ne peut que t'aider à
structurer, jamais remplacer.

## Comment ce niveau est réutilisé plus tard

Le périmètre et les non-objectifs identifiés ici deviennent l'entrée du découpage en tranches
au [niveau 03](../03-MVP-SPLIT/README.md). Les métriques de succès définies ici servent de
critère de validation dans les boss-fights des niveaux suivants, notamment au
[niveau 08](../08-ROADMAP-RUN/README.md) pour suivre l'avancement réel d'un projet.

## Ce que tu dois savoir défendre

- Pourquoi une demande formulée n'est presque jamais identique au besoin réel, et donne un
  exemple qui ne soit pas celui de ce fichier.
- Pourquoi un code de haute qualité technique peut représenter un échec de projet total.
- Ce qui distingue un développeur qui "prend des specs" d'un développeur qui "chasse le besoin".
