# Trouver le vrai besoin

## La scène

La responsable d'un cabinet vétérinaire à trois praticiens t'écrit : "il nous faut un système
de rappel automatique par SMS pour les vaccins, parce que les clients oublient." Tu pourrais
foncer sur un cron job qui envoie des SMS un mois avant l'échéance vaccinale. Mais avant de
coder, tu poses trois questions et tu découvres que le vrai problème est ailleurs : les
vétérinaires notent les dates de rappel sur un carnet papier que la secrétaire recopie
"quand elle a le temps" dans un tableur, deux fois par mois. La moitié des rappels n'existe
même pas dans un fichier numérique. Un SMS automatique branché sur des données incomplètes
enverrait des rappels à moitié des clients et rien aux autres — pire que le problème initial,
parce que ça donnerait une fausse impression de fiabilité.

## Ce qui se passe vraiment

Trois couches à séparer systématiquement quand quelqu'un te fait une demande :

- **La demande** : ce que la personne dit vouloir ("un système de SMS automatique").
  C'est une solution déjà pensée, presque toujours influencée par ce que la personne connaît
  ou a vu ailleurs.
- **Le besoin** : le problème réel derrière la demande ("les clients ne reviennent pas à temps
  pour les vaccins, ce qui coûte du chiffre d'affaires et met en danger la santé animale").
  Le besoin explique *pourquoi* la demande existe.
- **La contrainte** : ce qui limite les solutions possibles ("les dates de rappel ne sont pas
  fiables dans le système actuel", "la secrétaire n'a pas le temps de faire une saisie
  manuelle supplémentaire", "le budget mensuel pour les SMS est de 30 euros").

Confondre ces trois couches est l'erreur la plus fréquente en cadrage. Traiter une contrainte
comme un besoin fait rater le vrai problème. Traiter une demande comme un besoin fait coder
la première idée venue sans vérifier qu'elle le résout.

```text
"Il nous faut un SMS automatique"          ← demande (solution déjà pensée)
        │
        ▼ pourquoi ?
"Les clients oublient les vaccins"         ← symptôme, encore une couche au-dessus du besoin
        │
        ▼ pourquoi ce symptôme a un impact ?
"Ça coûte du chiffre d'affaires et         ← besoin réel
 met en danger la santé animale"
        │
        ▼ qu'est-ce qui empêche de résoudre ça directement ?
"Les dates de rappel ne sont pas fiables   ← contrainte structurante
 dans le système actuel"
```

## Interviews : faire émerger des faits, pas des opinions

Une interview de cadrage mal menée produit des opinions et des promesses ("oui ce serait
génial", "je pense que les gens aimeraient"). Une interview bien menée produit des faits
vérifiables sur des comportements passés.

Règles concrètes :

- Demande "raconte-moi la dernière fois que ça s'est produit", pas "est-ce que ça arrive
  souvent". Le récit précis d'un cas réel contient des détails que l'estimation générale
  écrase toujours.
- Ne demande jamais "utiliseriez-vous une fonctionnalité qui fait X". La réponse est presque
  toujours oui, sans engagement réel, parce que dire oui ne coûte rien à la personne interrogée.
- Cherche les contournements actuels : tableur fantôme, post-it, message WhatsApp entre
  collègues, feuille papier. Un contournement prouve qu'un besoin existe déjà et qu'il coûte
  assez cher pour que quelqu'un ait inventé une solution bricolée.
- Compte les occurrences réelles avant de croire à la fréquence perçue ("souvent" peut vouloir
  dire deux fois par semaine ou deux fois par mois selon la personne).
- Interroge au moins deux rôles différents sur le même processus (la secrétaire et la
  vétérinaire, par exemple) : leurs récits divergent presque toujours, et l'écart est
  informatif.

## Signaux faibles à repérer

Un signal faible est un indice discret qui annonce un problème avant qu'il devienne visible
dans les métriques officielles.

| Signal faible | Ce qu'il révèle |
|---|---|
| Un tableur "de secours" maintenu à la main en parallèle du système officiel | Le système officiel ne couvre pas un besoin réel |
| Un ticket de support qui revient sous des formulations différentes | Le vrai problème n'a jamais été traité, seulement ses symptômes |
| Une tâche que "seule une personne sait faire" dans l'équipe | Un processus non documenté et fragile, souvent invisible du management |
| Une fonctionnalité demandée "pour tout le monde" mais utilisée par une seule personne en pratique | Un besoin individuel déguisé en besoin collectif |
| Un export manuel régulier vers Excel puis remanipulation | L'outil ne restitue pas les données sous la forme dont on a réellement besoin |

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Interviewer plusieurs rôles avant de cadrer | Du temps, parfois perçu comme lent par le client pressé | Vision fidèle du besoin réel, évite un aller-retour coûteux après livraison | Toujours, sauf urgence vitale documentée |
| Prendre la demande telle quelle et coder vite | Rapidité immédiate apparente | Risque élevé de livrer une solution au mauvais problème | Uniquement sur un prototype jetable, jamais sur un vrai livrable |
| Se fier aux métriques déclaratives ("les gens disent que...") | Faible coût de collecte | Fiable seulement pour des opinions, pas des comportements | Jamais comme seule source pour décider du scope |

## Pièges classiques

- Accepter la première explication donnée par le client comme le besoin final, sans demander
  "pourquoi" une deuxième ou une troisième fois — le symptôme est pris pour la cause profonde.
- Confondre l'absence de plainte avec l'absence de problème : beaucoup d'utilisateurs
  contournent en silence plutôt que de se plaindre.
- Interroger uniquement la personne qui a formulé la demande (souvent un manager) et jamais
  les utilisateurs finaux qui vivent le problème au quotidien.
- Poser des questions fermées ("est-ce que ça vous embête ?") qui orientent vers une réponse
  polie plutôt qu'un fait.
- Traiter une contrainte budgétaire ou technique comme si elle n'existait pas, puis découvrir
  en fin de projet qu'elle bloque la solution retenue.

## Ce que tu dois savoir défendre

- La différence entre demande, besoin et contrainte, avec un exemple tiré d'un contexte
  différent de celui de cette leçon.
- Pourquoi "est-ce que vous utiliseriez cette fonctionnalité ?" est une question à bannir
  d'une interview de cadrage, et par quoi la remplacer.
- Comment un contournement actuel (tableur fantôme, post-it) t'aide à prouver qu'un besoin
  existe réellement, avant même d'avoir écrit une ligne de code.
