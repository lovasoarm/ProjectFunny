# Métriques de succès et seuil d'échec

## La scène

Une bibliothèque de club d'escalade développe un outil de réservation de créneaux sur les pans
d'entraînement, pour éviter les conflits de réservation griffonnés sur un carnet. Trois mois
après le lancement, le comité directeur demande "est-ce que ça marche ?" L'équipe répond :
"on a 40 comptes créés et 300 vues de page." Personne ne sait dire si c'est un succès. Le
carnet papier, lui, était consulté par tout le monde, tout le temps : mais personne n'avait
défini avant le lancement ce que "ça marche" voulait dire précisément. L'outil est peut-être
un succès, peut-être un échec silencieux : sans seuil défini d'avance, la question ne se
tranche pas, elle se discute indéfiniment selon l'humeur de la réunion.

## Ce qui se passe vraiment

Une métrique de succès produit mesure si le problème réel identifié en amont (voir la leçon
sur les besoins réels) est effectivement résolu pour l'utilisateur. Une métrique de vanité
mesure une activité qui donne une impression de traction sans rien dire sur la résolution du
problème. Le nombre de comptes créés est une métrique de vanité tant qu'on ne sait pas si ces
comptes réservent réellement des créneaux et évitent les conflits sur le pan d'entraînement.

```text
Métrique de vanité              Métrique de succès produit
─────────────────────           ──────────────────────────
Comptes créés                   Créneaux réservés sans double-booking
Vues de page                    % de séances où le grimpeur a eu le pan
                                 sans conflit ni attente
Téléchargements de l'appli      % d'utilisateurs qui reviennent réserver
                                 une deuxième fois dans le mois
```

La différence tient à une question simple : est-ce que cette métrique peut monter sans que le
problème initial soit résolu ? Si oui, c'est une métrique de vanité : elle mesure l'activité
autour du produit, pas son utilité réelle.

## Le seuil d'échec, défini avant de coder

Un seuil d'échec est un chiffre fixé avant le lancement, en dessous duquel l'équipe s'engage à
reconnaître que le produit, dans sa forme actuelle, ne fonctionne pas : et à en tirer une
décision (pivoter, arrêter, refaire une itération sur un point précis). Sans ce seuil écrit à
l'avance, chaque résultat décevant se rationalise après coup ("ce n'est pas si mal", "il faut
laisser le temps à l'adoption"), et le produit continue d'exister par inertie plutôt que par
utilité démontrée.

```text
Avant de coder, on écrit :
  Métrique de succès : % de séances avec réservation faite via l'outil,
    sans recours au carnet papier en parallèle.
  Seuil d'échec : si moins de 50% des séances passent par l'outil
    après 6 semaines d'usage réel, l'outil ne remplace pas le carnet
    : on retourne comprendre pourquoi avant de continuer à l'améliorer.

Six semaines plus tard, on mesure 22%.
  → seuil non atteint, décision prise à l'avance : on arrête d'ajouter
    des fonctionnalités et on va interroger les grimpeurs qui utilisent
    encore le carnet, pour comprendre le vrai blocage.
```

Le seuil transforme une discussion émotionnelle ("on a mis du temps, ce serait dommage
d'arrêter") en une décision déjà actée avant que l'attachement au projet ne brouille le
jugement.

## Choisir une métrique qui ne peut pas être trichée facilement

Une métrique se laisse parfois optimiser sans que le problème réel progresse. Si la métrique
choisie est "nombre de réservations créées", il suffit d'envoyer une notification insistante
pour la faire monter sans que les conflits sur le pan diminuent réellement. Une bonne métrique
de succès est reliée le plus directement possible au symptôme du problème initial, pas à un
comportement facilement stimulable par un rappel ou une notification.

## Compromis

| Option                                                                        | Coût                                                | Bénéfice                                                                                      | Quand choisir                                                      |
| ----------------------------------------------------------------------------- | --------------------------------------------------- | --------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| Métrique reliée directement au symptôme initial (conflits de créneaux évités) | Parfois plus difficile à instrumenter techniquement | Dit vraiment si le problème est résolu                                                        | Toujours en priorité, même si l'instrumentation demande du travail |
| Métrique d'activité facile à mesurer (comptes créés, vues)                    | Rapide à obtenir dès le lancement                   | Se fait manipuler par n'importe quelle action marketing, ne dit rien sur l'utilité            | Uniquement en complément, jamais comme métrique de décision        |
| Pas de seuil d'échec défini                                                   | Évite une conversation inconfortable en amont       | Chaque résultat se rationalise après coup, aucun produit n'est jamais officiellement un échec | Jamais                                                             |

## Pièges classiques

- Choisir une métrique de vanité parce qu'elle est facile à obtenir dès le premier jour, alors
  que la vraie métrique demande d'attendre plusieurs semaines d'usage réel.
- Définir le seuil d'échec après avoir vu les premiers résultats, ce qui permet de l'ajuster
  pour qu'il corresponde toujours au chiffre obtenu : le seuil doit être écrit avant.
- Confondre "la métrique est bonne" et "le produit est fini" : une métrique de succès atteinte
  signale un vrai problème résolu, pas l'absence de tout problème restant.
- Mesurer un seul indicateur global sans regarder s'il cache des écarts importants entre
  groupes d'utilisateurs (par exemple, l'outil marche pour les grimpeurs autonomes mais pas
  pour les débutants qui réservent encore par le carnet).

## Ce que tu dois savoir défendre

- La différence entre une métrique de vanité et une métrique de succès produit, avec un
  exemple qui montre qu'une métrique de vanité peut monter sans que le problème soit résolu.
- Pourquoi le seuil d'échec doit être écrit avant le lancement et non après avoir vu les
  premiers chiffres.
- Comment choisir une métrique qui résiste à la manipulation facile (notification, rappel)
  plutôt qu'une métrique qui se laisse gonfler artificiellement.
