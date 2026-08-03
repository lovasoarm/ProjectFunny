# Le coût des décisions

## Le piège

Une bibliothèque de club d'escalade doit choisir comment stocker l'historique des emprunts
de matériel (baudriers, cordes, chaussons). L'équipe hésite entre une table SQL normalisée
et un simple champ JSON "historique" par matériel. Le JSON est plus rapide à écrire ce
sprint-ci. Six mois plus tard, on veut savoir "quel matériel a été prêté plus de dix fois
sans révision" — une requête impossible à écrire proprement sur le JSON sans tout migrer. Le
coût n'a pas disparu, il a été déplacé et multiplié.

## Ce qui se passe vraiment

Toute décision technique a un coût de changement : ce qu'il en coûtera de revenir dessus plus
tard. Ce coût n'est jamais nul, mais il varie énormément selon la nature du choix.

```text
   COÛT DE CHANGEMENT PAR TYPE DE DÉCISION

   faible ─────────────────────────────────► élevé

   nom d'une variable    format de réponse API    schéma de base
   locale                consommé par un seul     de données partagé
                          client interne           par 5 services
        │                      │                        │
        ▼                      ▼                        ▼
   changer = minutes    changer = heures,        changer = semaines,
                         coordination un client   migration de données,
                                                   coordination multi-équipes
```

Il y a deux formes de dette technique, et les confondre est une erreur fréquente :

- **Dette volontaire** : un raccourci pris consciemment, avec un plan de remboursement
  explicite ("on stocke ça en JSON pour livrer avant la saison d'inscriptions, on migrera si
  on dépasse 500 emprunts/mois"). C'est un pari assumé.
- **Dette subie** : un raccourci pris sans le savoir, parce que personne n'a évalué le coût
  de changement au moment de la décision. C'est une facture qui arrive sans qu'on ait signé
  de contrat.

La dette volontaire, correctement documentée (règle 6 du Niveau 00), est un outil de gestion
du risque. La dette subie est un accident qui se répète tant que personne ne fait l'exercice
d'estimer le coût de changement avant de trancher.

### La valeur d'option

Une décision qui préserve plusieurs futurs possibles a une **valeur d'option** : tu payes
peut-être un peu plus cher maintenant pour ne pas te fermer de portes. Choisir un schéma de
base de données normalisé plutôt qu'un JSON, c'est payer un peu de temps de développement en
plus maintenant pour garder la possibilité de faire des requêtes analytiques plus tard sans
tout réécrire. La valeur d'option n'est pas gratuite non plus — sur-préserver des options
qu'on n'utilisera jamais est aussi un gaspillage.

```text
  coût immédiat bas, valeur d'option basse   →  JSON libre, pas de contrainte
  coût immédiat moyen, valeur d'option haute →  schéma normalisé + index ciblés
  coût immédiat haut, valeur d'option très haute → architecture event-sourcée
                                                   (rejouable, mais coûteuse à opérer)
```

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Dette volontaire documentée | Coût de remboursement futur connu et planifié | Vitesse de livraison immédiate, pari maîtrisé | Contexte incertain, besoin de valider vite une hypothèse produit |
| Investir dans une structure durable dès le départ | Coût immédiat plus élevé | Coût de changement futur réduit | Le domaine est stable, les requêtes futures sont déjà prévisibles |

## Pièges classiques

- Prendre une dette sans la nommer comme telle — symptôme : personne ne sait qu'il faut la
  rembourser, elle devient permanente par défaut.
- Sur-ingénierer pour préserver une option qui ne sera jamais exercée — symptôme : temps de
  développement doublé pour une flexibilité jamais utilisée deux ans après.
- Évaluer le coût de changement uniquement en lignes de code à modifier, en ignorant la
  coordination humaine nécessaire (autres équipes, migrations de données en production).
- Confondre "dette technique" et "code mal écrit" — la dette est un choix économique
  délibéré, le mauvais code est souvent juste une erreur, pas un pari.

## Ce que tu dois savoir défendre

1. Donne un exemple de dette volontaire bien documentée et explique quel serait le signal de
   remboursement.
2. Pourquoi le coût de changement d'un schéma de base de données partagé par plusieurs
   services est structurellement plus élevé que celui d'une fonction interne.
3. Qu'est-ce que la valeur d'option, et donne un exemple où l'ignorer a coûté cher.
