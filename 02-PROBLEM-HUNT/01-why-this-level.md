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

## Ce qui se passe vraiment

Une demande formulée par un client, un manager ou un product owner n'est presque jamais le
besoin réel. C'est une solution que la personne a déjà imaginée dans sa tête, habillée en
demande. Le rôle du développeur sénior n'est pas d'exécuter la solution imaginée, c'est de
remonter jusqu'au problème qu'elle est censée résoudre, puis de vérifier que la solution
proposée est effectivement la meilleure réponse à ce problème : parfois oui, souvent non.

Ce niveau existe parce que sans lui, tu deviens un excellent exécutant de mauvaises idées.
Et le pire, c'est que ton code sera propre, testé, bien architecturé : ce qui rend l'échec
invisible pendant longtemps, jusqu'à ce que les métriques d'usage tombent et que quelqu'un
demande "pourquoi personne n'utilise ça ?".

```text
Demande formulée           Besoin réel               Solution correcte
"une appli de              "savoir si une machine    "un panneau lumineux + une
 réservation"     ≠         est libre maintenant"  →  appli qui affiche le statut
                                                       en direct, sans réservation"
```

## Ce qui casse sans ce niveau

- **Le temps de développement est gaspillé sur la mauvaise cible.** Le code peut être
  irréprochable ; s'il répond à la mauvaise question, sa qualité technique ne rachète rien.
- **La confiance du client ou du produit s'érode.** Après deux ou trois livraisons "inutiles
  mais bien faites", la personne qui commande commence à douter de ton jugement, pas de tes
  compétences techniques.
- **Le scope grossit sans fin.** Sans besoin clarifié, chaque nouvelle idée du client semble
  légitime, parce qu'il n'y a pas de référence stable à laquelle la comparer.
- **Les métriques de succès n'existent pas**, donc personne ne peut dire objectivement si le
  projet a réussi ou échoué : ce qui ouvre la porte à des débats politiques plutôt que factuels.

## Ce que tu dois savoir défendre

- Pourquoi une demande formulée n'est presque jamais identique au besoin réel, et donne un
  exemple qui ne soit pas celui de ce fichier.
- Pourquoi un code de haute qualité technique peut représenter un échec de projet total.
- Ce qui distingue un développeur qui "prend des specs" d'un développeur qui "chasse le besoin".
