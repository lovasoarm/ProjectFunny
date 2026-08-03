# Savoir dire non sans passer pour l'obstacle

## La scène

Une plateforme de suivi de tournées de livraison a validé un MVP : créer une tournée,
l'assigner à un livreur, suivre sa progression en temps réel. Deux semaines avant la
livraison, le responsable des opérations demande d'ajouter "juste un petit module" de
facturation automatique aux clients à la fin de chaque tournée, "puisque de toute façon on a
déjà les données." Un développeur qui accepte par réflexe de bonne volonté ajoute deux
semaines de retard sur une fonctionnalité de suivi qui, elle, était déjà attendue par les
livreurs sur le terrain. Un développeur qui refuse en disant simplement "non, ce n'est pas
prévu" passe pour quelqu'un qui bloque le projet sans raison. Aucune des deux réactions ne
protège le vrai enjeu : livrer ce qui a été promis, dans le délai promis.

## Ce qui se passe vraiment

Dire non efficacement ne consiste pas à refuser une demande, mais à rendre visible son coût
d'opportunité : ce qu'on sacrifie ailleurs si on l'accepte. La plupart des demandes de
périmètre supplémentaire semblent gratuites parce que la personne qui les formule ne voit pas
ce qu'elles déplacent. Le rôle de qui reçoit la demande n'est pas de juger si elle est
légitime dans l'absolu : la facturation automatique est probablement une bonne idée un jour —
mais de rendre concret son prix, ici et maintenant, par rapport à ce qui était déjà engagé.

```text
Réponse qui bloque sans convaincre :
  "Non, ce n'est pas prévu dans le MVP."
  → vrai sur le fond, mais ne montre aucun raisonnement,
    ressemble à un refus de principe

Réponse qui montre le coût d'opportunité :
  "Si on ajoute la facturation automatique maintenant, le suivi en
   temps réel des tournées glisse de deux semaines : c'est la
   fonctionnalité que les livreurs attendent depuis le début, et
   c'est celle qui a été promise dans ce lot. On peut faire la
   facturation ensuite, en connaissant déjà mieux les données réelles
   de facturation collectées par le suivi. Qu'est-ce qui compte le
   plus dans ces deux semaines : le suivi ou la facturation ?"
```

La deuxième réponse déplace la décision vers la personne qui a le pouvoir d'arbitrer : elle
n'affirme pas un refus, elle rend le compromis visible et laisse le choix être fait en
connaissance de cause.

## Les trois réponses possibles, pas deux

Le réflexe binaire "oui ou non" oublie une troisième réponse, souvent la bonne : "pas
maintenant, et voici pourquoi, et voici ce qui doit se passer avant que ce soit possible."

```text
                 Demande de périmètre supplémentaire
                            │
       ┌────────────────────┼────────────────────┐
       │                    │                     │
      OUI                 NON                "PAS MAINTENANT"
  (le coût             (la demande           (vraie idée, mauvais
   d'opportunité         viole un              moment : dette
   est acceptable,       non-objectif,          consciente à
   arbitré               voir Niveau 02)        prévoir, pas à
   consciemment)                                subir)
```

"Pas maintenant" n'est crédible que s'il s'accompagne d'un engagement concret : où cette
demande sera-t-elle réévaluée, et sous quelle condition. Sans cet engagement, "pas maintenant"
devient un "non" déguisé qui use la confiance de la personne qui a formulé la demande.

## Dette technique assumée vs dette subie

Accepter une demande en sachant qu'elle crée un raccourci technique n'est pas une faute, à
condition que ce raccourci soit choisi consciemment et documenté : c'est de la dette assumée.
La dette subie, elle, apparaît quand personne n'a décidé consciemment de prendre ce raccourci :
il s'est simplement produit sous la pression, sans que personne ne l'écrive ni ne prévoie de
le rembourser.

```text
Dette assumée :
  "On code la facturation en dur pour un seul mode de calcul, on sait
   que ça ne gère pas les tarifs dégressifs, on l'écrit dans le ticket
   de suivi et on prévoit de le reprendre au sprint suivant."

Dette subie :
  Le même raccourci, pris dans l'urgence, sans ticket, sans personne
  qui se souvient dans six mois pourquoi le calcul est faux pour les
  gros clients à tarif dégressif.
```

La différence entre les deux n'est pas technique, elle est purement organisationnelle : est-ce
que la décision a été écrite quelque part, avec sa raison et son échéance de remboursement.

## Compromis

| Option                                               | Coût                                           | Bénéfice                                                                                         | Quand choisir                                                      |
| ---------------------------------------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------ |
| Accepter en rendant le coût d'opportunité visible    | Demande de chiffrer l'impact avant de répondre | La décision est prise en connaissance de cause, la confiance reste intacte des deux côtés        | Chaque fois qu'une demande de périmètre supplémentaire arrive      |
| Refuser sèchement sans expliquer le compromis        | Rapide sur le moment                           | Casse la relation de confiance, la demande revient sous une autre forme sans être mieux traitée  | Jamais, sauf violation nette d'un non-objectif déjà écrit et connu |
| Accepter par réflexe de bonne volonté sans arbitrage | Évite un conflit immédiat                      | Fait glisser silencieusement les délais déjà promis, sans que personne n'ait choisi ce compromis | Jamais                                                             |

## Pièges classiques

- Refuser une demande sans jamais montrer ce qu'elle coûterait ailleurs : le refus paraît
  arbitraire même quand il est justifié.
- Accepter systématiquement par crainte de paraître peu coopératif, ce qui fait glisser tous
  les délais un par un sans qu'aucun arbitrage explicite n'ait jamais eu lieu.
- Utiliser "pas maintenant" comme un refus déguisé, sans jamais préciser sous quelle condition
  la demande sera réexaminée : la confiance s'érode au deuxième "pas maintenant" sans suite.
- Prendre une dette technique sous la pression sans l'écrire nulle part, ce qui la transforme
  en incident de production surprenant des mois plus tard.

## Ce que tu dois savoir défendre

- Pourquoi montrer le coût d'opportunité convainc mieux qu'un refus de principe, même quand
  le refus est justifié sur le fond.
- La différence entre "non" et "pas maintenant", et pourquoi "pas maintenant" n'est crédible
  que s'il s'accompagne d'une condition de réexamen concrète.
- La différence entre dette technique assumée et dette technique subie, et pourquoi cette
  différence est organisationnelle plutôt que technique.
