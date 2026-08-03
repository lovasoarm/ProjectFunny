# Raisonner sous incertitude

## Le piège

Un cabinet vétérinaire à trois praticiens veut ouvrir la prise de rendez-vous en ligne.
L'équipe technique débat pendant deux jours pour savoir si le système doit gérer les
créneaux récurrents (vaccination annuelle, suivi de convalescence) dès la première version.
Personne ne sait combien de clients utiliseraient réellement cette fonctionnalité — le
cabinet n'a jamais eu de statistiques là-dessus, seulement l'intuition d'une secrétaire.
L'équipe construit quand même un moteur de récurrence complet, avec exceptions, décalages
de jours fériés, et règles de conflit. Trois mois plus tard : douze utilisateurs sur quatre
cents ont créé un rendez-vous récurrent. Le vrai problème du cabinet, qu'un simple sondage
aurait révélé en une heure, était l'absence de rappel SMS.

## Ce qui se passe vraiment

Sous incertitude, deux réflexes tuent silencieusement les projets : construire comme si on
savait déjà la réponse, ou attendre de tout savoir avant de bouger. Le raisonnement correct
se situe entre les deux : nommer explicitement ce qu'on ignore, puis choisir l'action la
moins chère qui réduit cette ignorance.

```text
   CE QUE TU CROIS SAVOIR              CE QUE TU SAIS VRAIMENT

   "les clients veulent            →   personne n'a demandé,
    des rendez-vous récurrents"        c'est une intuition de la secrétaire

   "le pic de charge est            →   aucune mesure, extrapolé
    le samedi matin"                    d'un seul samedi observé

   "la migration prendra            →   estimation à vue de nez,
    deux jours"                         jamais fait cette migration
```

Chacune de ces phrases est une **hypothèse**, pas un fait. Une hypothèse a trois propriétés
utiles : elle peut être fausse, on peut l'énoncer en une phrase testable, et on peut estimer
le coût de se tromper si on construit dessus sans la vérifier.

### Formuler une hypothèse testable

Une hypothèse mal formée ne peut pas être invalidée — elle est donc inutile, même si elle
est vraie. Comparer :

```text
   MAUVAISE HYPOTHÈSE                 BONNE HYPOTHÈSE
   (invérifiable)                     (testable, avec seuil)

   "les clients aimeraient            "au moins 15 % des clients actifs
    des rendez-vous récurrents"        créeront un rendez-vous récurrent
                                        dans les deux premiers mois si on
                                        leur propose l'option"
```

La bonne hypothèse contient un seuil chiffré. Sans seuil, n'importe quel résultat pourra être
interprété après coup comme une confirmation — c'est le biais de confirmation en action.

### Le coût de la preuve, pas seulement le coût de la construction

La question à se poser n'est jamais "combien coûte de construire la fonctionnalité", mais
"combien coûte de savoir si la fonctionnalité est nécessaire, comparé au coût de se tromper
en la construisant en aveugle".

```text
   Coût de l'expérience la moins chère : ajouter une case à cocher
   "je voudrais un rappel automatique pour mes prochains rendez-vous"
   dans le formulaire de prise de rendez-vous existant.
   → 2 heures de développement, 2 semaines de collecte.

   Coût de construire le moteur de récurrence en aveugle :
   → 3 semaines de développement, coût de maintenance permanent,
     et un risque significatif que la fonctionnalité soit inutile.

   Ratio de coût : 1 pour 60. L'expérience gagne largement.
```

Ce raisonnement porte un nom : la **réduction de risque au moindre coût**. On cherche, parmi
toutes les façons de réduire l'incertitude, celle qui coûte le moins cher en temps et en
argent avant d'investir dans la solution complète. Un sondage, un prototype cliquable sans
logique réelle derrière, une fonctionnalité manuelle assurée par un humain pendant deux
semaines ("magicien de Oz") : toutes ces expériences valent souvent mieux que trois semaines
de code parce qu'elles produisent la même information avec un ordre de grandeur de coût en
moins.

### Le pari le moins cher

Parfois, l'expérience elle-même coûte plus cher que d'assumer le risque directement. C'est
là qu'intervient le **pari le moins cher** : quand plusieurs options restent incertaines
même après avoir réduit ce qu'on pouvait réduire, on choisit celle dont l'erreur coûte le
moins cher à corriger — pas celle qui semble la plus probable de réussir.

```text
   Option A : construire le moteur de récurrence complet
              → si l'hypothèse est fausse, 3 semaines perdues,
                code complexe à retirer, risque de régression.

   Option B : ajouter un champ "type de rendez-vous : ponctuel /
              suivi" et laisser un humain (la secrétaire) créer
              manuellement les rendez-vous de suivi pendant 2 mois
              → si l'hypothèse est fausse, 2 heures perdues.
              → si l'hypothèse est vraie, on a la preuve chiffrée
                pour justifier l'investissement dans l'automatisation.

   Le pari le moins cher n'est pas "le plus probable de marcher".
   C'est celui dont l'échec coûte le moins cher.
```

Ce raisonnement s'applique aussi bien à une décision produit ("faut-il cette fonctionnalité")
qu'à une décision technique ("faut-il cette architecture"). Dans les deux cas, la question de
fond est identique : qu'est-ce que je ne sais pas, et quelle est la façon la moins chère de le
découvrir avant de m'engager plus loin ?

### Asymétrie des erreurs

Toutes les erreurs ne coûtent pas pareil dans les deux sens. Se tromper en construisant une
fonctionnalité inutile coûte du temps de développement. Se tromper en ne construisant pas une
fonctionnalité critique — par exemple un rappel de rendez-vous qui évite des rendez-vous
manqués, donc des pertes de revenu pour le cabinet — peut coûter bien plus cher et plus vite.
Avant de choisir le pari le moins cher, il faut évaluer le coût des deux sens de l'erreur, pas
un seul.

```text
                    hypothèse vraie          hypothèse fausse
   je construis     bon calcul               temps perdu,
                                              code à retirer
   je ne construis  opportunité manquée,     bon calcul
   pas               coût potentiellement
                      élevé et différé
```

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Construire l'expérience la moins chère avant la solution complète | Temps de conception de l'expérience, délai avant la fonctionnalité finale | Information fiable à faible coût, décision fondée sur des faits | L'incertitude est haute et le coût de se tromper en construisant est élevé |
| Assumer directement le pari le moins cher, sans expérience préalable | Risque de devoir défaire un choix | Rapidité, pas de temps perdu à instrumenter une expérience | L'expérience coûterait presque aussi cher que la solution elle-même, ou le délai est inacceptable |

## Pièges classiques

- Construire une expérience si coûteuse qu'elle revient à construire la solution complète —
  symptôme : le "prototype" prend autant de temps que la vraie fonctionnalité.
- Formuler une hypothèse sans seuil chiffré — symptôme : après coup, tout le monde interprète
  le résultat dans le sens qui l'arrange.
- Confondre "le pari le moins cher" avec "le pari le plus probable" — symptôme : on choisit
  l'option qu'on préfère intuitivement et on justifie a posteriori que c'était la moins
  risquée.
- Ignorer l'asymétrie des erreurs — symptôme : on optimise pour éviter de perdre du temps de
  développement, en ignorant un coût métier bien plus élevé côté "ne pas construire".
- Attendre une certitude totale avant d'agir — symptôme : le projet n'avance plus, chaque
  décision est reportée en attendant "plus de données" qui ne viendront jamais gratuitement.

## Ce que tu dois savoir défendre

1. Transforme une intuition vague ("les utilisateurs vont adorer ça") en hypothèse testable
   avec un seuil chiffré.
2. Donne un exemple où l'expérience la moins chère aurait coûté plus cher que la solution
   complète — pourquoi, dans ce cas précis, ne pas expérimenter était le bon choix.
3. Explique la différence entre "le pari le moins cher" et "le pari le plus probable de
   réussir", avec un exemple où les deux mènent à des choix opposés.
