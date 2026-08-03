# Boss Fight : MVP Split

## La situation

Ton plan de découpage (voir `challenge.md`) a été validé : trois tranches, quatre semaines, la
première tranche doit être livrée en fin de semaine 1 pour un test avec un cabinet pilote.
En semaine 1, à mi-parcours, le développeur qui travaille avec toi te dit :

> "En creusant, je me rends compte que gérer la disponibilité en temps réel des autres
> cabinets, c'est plus compliqué que prévu : il faut gérer les cas où un cabinet ferme
> exceptionnellement, où le vétérinaire de garde change en cours de journée, où deux urgences
> arrivent en même temps sur le même cabinet. Je pense qu'on devrait retarder la livraison de
> la tranche 1 d'une semaine pour bien faire les choses, plutôt que de livrer quelque chose de
> bancal."

Le lendemain, le directeur général t'écrit séparément :

> "Le cabinet pilote a bloqué un créneau vendredi pour tester l'outil avec ses vraies urgences.
> Ils en parlent déjà à l'ensemble du réseau comme d'une avancée. Il ne faut surtout pas
> décaler, ça ferait mauvaise impression."

Tu as ces deux messages contradictoires le même jour, et une décision à prendre avant la fin
de la journée.

## Ce que tu dois faire

Rédige, en markdown :

1. Ta décision (livrer vendredi, décaler, ou une troisième option), justifiée en t'appuyant
   sur la notion d'invariant vue en leçon 03 : est-ce que les cas cités par le développeur
   (fermeture exceptionnelle, changement de vétérinaire de garde, urgences simultanées)
   touchent l'invariant de la fonctionnalité, ou sont-ils des cas limites qui peuvent être
   traités par une coupe de portée sans casser l'intention de l'outil.
2. Le message que tu enverrais au développeur pour cadrer la suite sans le braquer.
3. Le message que tu enverrais au directeur général pour gérer ses attentes sans lui mentir
   sur l'état réel d'avancement.

## Grille d'évaluation

| Critère                                      | Ce qui est évalué                                                                                                           | Signal d'échec                                                                                         |
| -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| Identification de l'invariant                | La décision distingue clairement ce qui est vital de ce qui est un cas limite gérable                                       | Tous les cas cités sont traités comme également critiques, ou tous comme négligeables sans distinction |
| Cohérence entre les deux messages            | Le message au développeur et celui au directeur racontent la même réalité, sans double discours                             | Le développeur et le directeur reçoivent des versions incompatibles de la situation                    |
| Gestion de la pression sans céder ni ignorer | La décision tient compte des deux contraintes réelles (qualité et calendrier) sans sacrifier silencieusement l'une des deux | La décision ignore une des deux pressions sans l'assumer explicitement                                 |
| Honnêteté sur l'état d'avancement            | Le directeur reçoit une information vraie, même si elle implique une limite assumée pour vendredi                           | Le message enjolive l'état réel pour éviter un désagrément immédiat                                    |
| Cap sur l'intention de l'outil               | La version livrée vendredi, même réduite, reste honnête pour l'utilisateur qui s'en sert                                    | La version livrée donne une fausse impression de fiabilité qu'elle n'a pas                             |
