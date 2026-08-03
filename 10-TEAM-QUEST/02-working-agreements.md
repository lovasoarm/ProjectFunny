# Les règles qu'on écrit avant d'en avoir besoin

## Le piège

L'équipe qui construit la bibliothèque partagée d'un club d'escalade (gestion des prêts de
matériel, réservation de créneaux mur, inscriptions aux sorties) compte quatre personnes.
Trois mois après le lancement, une dispute éclate en réunion : Farida pensait qu'une tâche
marquée "faite" par Tom voulait dire "testée et déployée", Tom pensait que ça voulait dire
"le code est écrit, quelqu'un d'autre doit tester". Résultat, une fonctionnalité cassée est
restée en production dix jours sans que personne ne s'en sente responsable — chacun pensait
que c'était la responsabilité de l'autre étape. Ce n'est pas un problème de compétence. Tom
et Farida n'ont jamais eu la même définition du mot "fini", et personne ne l'a jamais écrite.

## Ce qui se passe vraiment

Un working agreement n'est pas un règlement intérieur ni une charte de valeurs. C'est une
liste de réponses écrites à l'avance aux questions qui, sinon, se posent pour la première
fois au pire moment — en pleine urgence, en plein désaccord, ou une fois le mal fait. Il ne
remplace pas le jugement de l'équipe, il évite que chaque situation nouvelle nécessite de
réinventer une règle sous pression.

```text
Sans working agreement                    Avec working agreement écrit
                                           
Question posée pour la première fois      Question déjà réglée à froid
pendant l'urgence                                │
     │                                     Chacun connaît la règle avant
Chacun improvise sa propre réponse        d'en avoir besoin
     │                                           │
Les réponses divergent                     Le désaccord, s'il existe, porte
     │                                     sur "faut-il changer la règle",
Le désaccord devient un conflit            pas sur "qui a raison maintenant"
personnel ("tu n'as pas fait ce
qu'il fallait")
```

Un working agreement utile répond à un nombre limité de questions, précises, pas à une
liste infinie de bonnes intentions. Cinq catégories couvrent l'essentiel de ce qui casse
une équipe :

### 1. La définition de "fini" (Definition of Done)

"Fini" doit être une liste vérifiable, pas une impression. Pour l'équipe de la bibliothèque
du club d'escalade, une définition insuffisante ressemble à "le code compile et fait ce
qu'on veut". Une définition utile ressemble à :

```text
Une tâche est "fini" quand :
  - le code est fusionné sur la branche principale
  - au moins un pair a relu et approuvé la pull request
  - les tests automatiques passent sur l'intégration continue
  - la fonctionnalité a été vérifiée avec des données réalistes,
    pas seulement le cas de démonstration
  - la documentation utilisateur, si elle existe, est à jour
```

Sans cette liste écrite, "fini" veut dire des choses différentes selon qui la prononce, et
chaque écart de définition coûte une confusion invisible jusqu'à ce qu'elle explose.

### 2. Qui décide quoi

Toutes les décisions ne se prennent pas au même niveau, et confondre les niveaux paralyse
l'équipe ou, à l'inverse, provoque des décisions arbitraires prises par la personne la plus
bavarde en réunion. Une méthode simple : classer chaque type de décision selon qui tranche.

```text
Type de décision                    Qui décide                Comment
─────────────────────────────────────────────────────────────────────
Choix d'implémentation locale       la personne qui code       seule, révisable en revue
(nommage, structure interne)                                   de code

Choix d'architecture partagée       l'équipe                   discussion courte, décision
(format d'API, schéma de données)                              écrite dans un document
                                                                daté

Choix de priorité produit           le porteur produit          après consultation de
(quelle fonctionnalité d'abord)     ou product owner            l'équipe technique sur
                                                                le coût de chaque option

Décision irréversible ou coûteuse   toute l'équipe, à froid     jamais sous pression d'une
(changer de base de données,                                    deadline du jour même
supprimer des données de prod)
```

Le piège le plus courant n'est pas l'absence de hiérarchie, c'est l'ambiguïté sur qui a le
dernier mot dans un désaccord technique entre pairs. Sans réponse écrite à cette question,
la décision revient, de fait, à la personne qui insiste le plus longtemps — ce qui n'a
aucun rapport avec la qualité de l'argument.

### 3. Les horaires et rythmes de revue

Une pull request qui attend trois jours une revue n'est pas un problème de compétence, c'est
un problème d'accord absent sur le délai attendu. Un working agreement fixe un engagement
mesurable : "toute pull request reçoit une première réponse (approbation, commentaire, ou
refus argumenté) sous 24 heures ouvrées." Cet engagement change le comportement de toute
l'équipe : celui qui ouvre la pull request sait quand relancer sans se sentir impoli, celui
qui doit la relire sait qu'il a un délai, pas une urgence permanente.

### 4. Comment on communique une absence ou un retard

Une personne qui prend du retard sur une tâche et ne le dit qu'au moment du point d'équipe
transforme un problème de deux jours en un problème de blocage pour toute l'équipe. Le
working agreement fixe le signal minimal attendu : "dès qu'une tâche prend plus de retard
que prévu de plus d'une demi-journée, on le signale dans le canal d'équipe, sans attendre le
point suivant." Ce n'est pas une question de discipline morale, c'est une question de
latence d'information : plus l'équipe apprend tôt qu'une tâche dérape, plus elle a d'options
pour réagir.

### 5. Comment on gère un fichier ou un module partagé

Deux personnes qui travaillent sur le même fichier plus d'une heure sans se le dire
produisent, statistiquement, un conflit de fusion coûteux ou un travail en double. Le
working agreement fixe une règle simple et vérifiable : "avant de commencer un travail de
plus de trente minutes sur un fichier déjà touché récemment par quelqu'un d'autre, on
prévient dans le canal d'équipe."

## Rédiger un working agreement qui sert vraiment

Un mauvais working agreement est une liste de bonnes intentions ("on communique bien",
"on respecte les délais") qu'on relit une fois et qu'on oublie. Un bon working agreement a
trois propriétés :

1. **Vérifiable** : chaque règle peut être confirmée ou contredite par un fait observable
   ("la pull request a reçu une réponse sous 24h"), pas par une impression.
2. **Daté et révisable** : il porte une date de rédaction et une date de prochaine révision
   — un accord qui décrit une équipe d'il y a six mois ne sert plus à rien.
3. **Négocié, pas imposé** : chaque membre de l'équipe a pu contester une règle avant
   qu'elle soit adoptée. Un working agreement imposé par une seule personne est suivi
   jusqu'à la première urgence, où chacun revient à ses propres réflexes.

```text
Cycle de vie d'un working agreement

  Rédaction collective ──► Adoption avec date ──► Application
        │                                              │
        │                                    Un cas non couvert survient
        │                                              │
        └──────── Révision datée ◄───── La règle est ajustée ou complétée
```

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Pas de working agreement écrit | Aucun coût de rédaction | Rapidité si l'équipe est très petite et de courte durée | Binôme sur un projet de quelques jours |
| Working agreement minimal (définition de "fini" + délai de revue) | Une heure de rédaction | Couvre les deux frictions les plus fréquentes et les plus coûteuses | Équipe de deux à quatre personnes débutant une collaboration |
| Working agreement complet (les cinq catégories) | Une demi-journée de rédaction, une révision périodique | Couvre la majorité des sources de friction avant qu'elles apparaissent | Équipe qui collabore plus d'un mois, ou avec un historique de friction |

## Pièges classiques

- Rédiger un working agreement composé de bonnes intentions ("on communique de façon
  transparente") sans critère vérifiable — le symptôme est que personne ne peut dire si la
  règle est respectée ou non.
- Le faire rédiger par une seule personne puis l'imposer — le symptôme est que le document
  est suivi la première semaine puis oublié dès la première urgence.
- Ne jamais le réviser — le symptôme est un document qui décrit une équipe et un contexte
  qui n'existent plus, cité par personne en cas de conflit.
- Confondre working agreement et règlement disciplinaire — le symptôme est que l'équipe le
  vit comme une contrainte imposée plutôt que comme un outil qu'elle a choisi pour se
  protéger elle-même.
- Multiplier les règles au point que plus personne ne les connaît toutes — le symptôme est
  qu'on invoque le document seulement pour justifier une sanction, jamais pour prévenir un
  problème.

## Ce que tu dois savoir défendre

1. Pourquoi une définition de "fini" non écrite finit toujours par diverger entre deux
   membres d'une équipe, même compétents et de bonne foi.
2. Explique pourquoi la question "qui a le dernier mot en cas de désaccord technique" doit
   être répondue avant le désaccord, pas pendant.
3. Donne un exemple de règle de working agreement mal formulée (non vérifiable) et
   reformule-la en règle vérifiable.
