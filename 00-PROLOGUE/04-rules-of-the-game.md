# Les 7 règles du joueur

## La scène

Karim travaille sur le système de refacturation d'énergie d'un syndic d'immeubles :
répartir la consommation collective (chauffage, eau chaude) entre les copropriétaires selon
des clés de répartition qui changent d'une année sur l'autre. Il livre une version qui
fonctionne sur les cas qu'il a testés. Six mois plus tard, un copropriétaire conteste sa
facture, personne ne se souvient pourquoi la clé de répartition a été codée en dur pour
2023, et il n'existe aucune mesure de combien de temps ça prend de traiter un litige de
facturation. Karim n'a violé aucune règle de style de code. Il a violé les règles du jeu :
il n'a jamais mesuré, jamais écrit sa décision, jamais prévu qu'il faudrait y revenir.

Ces 7 règles ne sont pas des conseils. Ce sont les comportements que chaque niveau suivant
va exiger de toi, encore et encore, jusqu'à ce qu'ils deviennent des réflexes.

## Ce qui se passe vraiment

```text
   ┌─────────────────────────────────────────────────────┐
   │                LE CYCLE DU JOUEUR                     │
   │                                                       │
   │   1. LIVRER  ───────►  2. MESURER                    │
   │       ▲                     │                         │
   │       │                     ▼                         │
   │   7. REVENIR   ◄──────  3. ÉCRIRE                     │
   │       ▲                     │                         │
   │       │                     ▼                         │
   │   6. DOCUMENTER  ◄────  4. DÉFENDRE                   │
   │   LA DÉCISION               │                         │
   │       ▲                     ▼                         │
   │       └──────────────  5. JETER                       │
   │                    (si le pari a échoué)               │
   └─────────────────────────────────────────────────────┘
```

### Règle 1 : Livrer

Un système qui existe seulement dans ta tête ou sur un tableau blanc ne t'apprend rien. La
livraison : un artefact fonctionnel, même petit, même moche : est le seul moyen de confronter
ton modèle mental à la réalité. Une conception brillante jamais livrée est indiscernable
d'une mauvaise conception : les deux produisent zéro rétroaction.

Ce que ça implique concrètement : préfère un livrable incomplet mais réel à une conception
parfaite mais théorique. Le système de tournées de livraison qui gère 80% des cas et plante
proprement sur les 20% restants t'apprend plus, une fois en usage, qu'une architecture
"complète" jamais testée en conditions réelles.

### Règle 2 : Mesurer

Sans mesure, "ça marche mieux" est une opinion. Chaque décision technique doit avoir un
critère observable qui dirait si elle a échoué. Pas nécessairement un chiffre complexe :
"le temps de traitement d'un litige de facturation passe de 3 jours à 1 jour" est une mesure.
"Le code est plus propre" n'en est pas une.

Ce que ça implique : avant de commencer un changement, écris la phrase qui décrirait son
échec de façon vérifiable. Si tu ne peux pas l'écrire, tu ne sauras jamais si tu as réussi.

### Règle 3 : Écrire

Une décision non écrite n'existe pas dans six mois. Ton cerveau reconstruira une histoire
plausible mais fausse de pourquoi tu as choisi X. Écrire : même trois phrases dans un ADR —
force une clarté que le raisonnement verbal ou mental n'exige jamais.

Ce que ça implique : chaque décision qui coûterait cher à annuler (choix de base de données,
structure d'une table centrale, protocole entre deux services) mérite une trace écrite datée,
même informelle.

### Règle 4 : Défendre

Une décision que tu ne peux pas défendre à l'oral face à un contradicteur compétent n'est pas
une décision, c'est un réflexe habillé en décision. Défendre force à vérifier que ton
raisonnement tient sous pression, pas seulement dans le confort de ta propre tête.

Ce que ça implique : avant de considérer un choix comme acquis, imagine la meilleure objection
possible d'un collègue senior sceptique, et vérifie que tu as une réponse : pas une esquive.

### Règle 5 : Jeter

Un prototype, une expérimentation, ou même une feature entière peuvent devoir être jetés.
Ce n'est pas un échec du joueur, c'est le jeu qui fonctionne normalement : le seul moyen de
savoir si un pari technique valait le coup était de le tester, et certains paris perdent.
S'accrocher à du code parce qu'il a coûté cher à écrire (le biais des coûts irrécupérables)
coûte toujours plus cher que de l'abandonner à temps.

Ce que ça implique : avant de lancer un pari risqué, décide à l'avance du signal qui te ferait
l'abandonner. Décide-le avant, pas pendant, quand ton ego est déjà investi dedans.

### Règle 6 : Documenter la décision, pas juste le résultat

Écrire "on utilise PostgreSQL" documente un résultat. Écrire "on utilise PostgreSQL parce que
le domaine a des contraintes transactionnelles fortes entre trois tables liées, et qu'on a
rejeté un document store parce qu'il aurait déplacé cette cohérence dans le code applicatif au
prix d'un risque de bug de synchronisation" documente une décision. La deuxième version reste
utile quand le contexte change : elle permet de vérifier si la raison originale tient encore.

Ce que ça implique : quand tu écris (règle 3), inclus systématiquement les options rejetées et
pourquoi, pas seulement l'option retenue.

### Règle 7 : Revenir

Une décision prise n'est jamais définitive. Revenir signifie relire une décision passée à
intervalle régulier ou à l'occasion d'un changement de contexte, et vérifier si elle tient
toujours. La plupart des mauvaises architectures ne sont pas mauvaises à l'origine : elles
étaient bonnes pour un contexte qui a changé sans que personne ne revienne vérifier.

Ce que ça implique : quand tu documentes une décision (règle 6), note aussi la condition qui
la rendrait obsolète ("si le volume de créneaux dépasse 10 000/jour, revoir l'index"). Ça te
donne un déclencheur concret pour la règle 7, au lieu de compter sur ta mémoire.

## Compromis

| Option                                                           | Coût                        | Bénéfice                                            | Quand choisir                                                              |
| ---------------------------------------------------------------- | --------------------------- | --------------------------------------------------- | -------------------------------------------------------------------------- |
| Appliquer les 7 règles à chaque décision, même mineure           | Ralentit chaque micro-choix | Aucune dette de compréhension accumulée             | Décisions structurantes (schéma de données, contrat d'API, protocole)      |
| Appliquer les règles seulement aux décisions coûteuses à annuler | Plus rapide au quotidien    | Risque de mal calibrer "coûteux à annuler" au début | Une fois que tu as l'expérience de reconnaître les décisions structurantes |

## Pièges classiques

- Mesurer après coup, pour justifier une décision déjà prise : le symptôme est un choix de
  métrique qui colle toujours miraculeusement au résultat obtenu.
- Écrire des ADR qui ne listent que l'option choisie, jamais les options rejetées : le
  symptôme est l'incapacité à répondre "pourquoi pas X" six mois plus tard.
- Refuser de jeter un prototype parce que "on a déjà mis trois semaines dessus" : c'est le
  biais des coûts irrécupérables, symptôme classique du non-respect de la règle 5.
- Documenter une décision sans jamais y revenir (règle 6 sans règle 7) : le symptôme est un
  ADR qui recommande toujours la même chose des années après que le contexte a changé.

## Ce que tu dois savoir défendre

1. Pourquoi "jeter" un prototype fait partie du jeu et n'est pas un échec personnel.
2. Quelle est la différence concrète entre documenter un résultat et documenter une décision
   : donne un exemple des deux sur un même choix technique.
3. Comment sais-tu, sans replonger dans ta mémoire, qu'une vieille décision technique mérite
   d'être réexaminée aujourd'hui.
