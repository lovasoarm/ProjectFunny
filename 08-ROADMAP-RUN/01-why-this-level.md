# Pourquoi les plannings optimistes tuent les projets

## La scène

Léa dirige le développement du système de refacturation d'énergie pour un syndic qui gère
40 immeubles. Le patron demande une date. Léa additionne les tâches qu'elle voit dans son
backlog : écran de saisie des index, calcul de répartition par tantièmes, export PDF des
factures : et annonce "six semaines". Elle livre à la semaine 11. Entre-temps, elle a
découvert que la règle de répartition change selon le type de contrat (chauffage collectif
vs individuel), que deux immeubles ont des tantièmes qui ne totalisent pas 10 000 à cause
d'une erreur historique jamais corrigée, et que le format d'export attendu par le prestataire
d'impression n'est documenté nulle part et doit être rétro-ingénié à partir d'exemples PDF.
Aucune de ces trois choses n'était "un risque identifié". Elles étaient juste absentes du
planning parce que Léa a listé ce qu'elle savait faire, pas ce qu'elle ne savait pas encore.

## Ce qui se passe vraiment

Un planning optimiste additionne des tâches connues. Un planning réaliste soustrait du temps
pour des inconnues qu'on n'a pas encore nommées. La différence entre les deux n'est pas la
compétence de la personne qui planifie : c'est la méthode.

```text
Planning optimiste (par tâches connues)
┌──────────┬──────────┬──────────┐
│ tâche A  │ tâche B  │ tâche C  │  → durée = somme des durées estimées
└──────────┴──────────┴──────────┘
   3j          2j          1j        = 6 jours annoncés

Réalité (les inconnues se révèlent en cours de route)
┌──────────┬────┬──────────┬────┬──────────┬────┐
│ tâche A  │ ?! │ tâche B  │ ?! │ tâche C  │ ?! │  → durée = somme + surprises
└──────────┴────┴──────────┴────┴──────────┴────┘
   3j        2j     2j       3j     1j       2j    = 13 jours réels
```

Les "?!" ne sont pas des imprévus au sens météorologique : ce sont des inconnues
_connaissables à l'avance_ si on les avait cherchées avant d'estimer. La règle de
répartition variable selon le type de contrat était dans le cahier des charges du syndic,
noyée dans une annexe. L'erreur de tantièmes était visible dans les données existantes, si
quelqu'un les avait ouvertes avant la semaine 8. Le format d'export était devinable en
demandant au prestataire d'impression avant de coder, pas après.

Un planning tient la route quand il fait deux choses qu'un planning optimiste ne fait pas :

1. **Il isole ce qui est réellement inconnu** et lui donne un temps dédié : une phase de
   découverte, pas une tâche de développement. Découvrir une règle métier n'a pas la même
   nature que l'implémenter ; les confondre dans une seule estimation cache le risque.
2. **Il ordonne les phases pour faire remonter les inconnues tôt**, quand elles coûtent
   encore peu à corriger, plutôt qu'à la fin, quand tout le reste du système est construit
   par-dessus une hypothèse fausse.

### Le coût de la découverte tardive

Une inconnue découverte en semaine 2 coûte le temps de la corriger. La même inconnue
découverte en semaine 10 coûte le temps de la corriger _plus_ le temps de refaire tout ce
qui a été construit sur l'hypothèse fausse _plus_ la confiance perdue avec le sponsor qui
avait annoncé une date à ses propres clients.

```text
coût de correction
      │                                              ● (semaine 10 : tout est à refaire)
      │                                      ●
      │                              ●
      │                      ●
      │              ●
      │      ●
      └──────────────────────────────────────────────► temps où l'inconnue est découverte
     sem.1   sem.3   sem.5   sem.7   sem.9   sem.10
```

Ce n'est pas une courbe linéaire, c'est une courbe qui s'emballe, parce que le code écrit
après l'hypothèse fausse _dépend_ d'elle. Corriger l'hypothèse en semaine 10 ne veut pas dire
changer une fonction, ça veut dire défaire des couches.

## Compromis

| Option                                                 | Coût                                                              | Bénéfice                                                                                                 | Quand choisir                                                                                                   |
| ------------------------------------------------------ | ----------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| Planning par somme de tâches connues                   | Rapide à produire, facile à vendre ("6 semaines")                 | Motivant à court terme, personne ne pose de questions gênantes                                           | Jamais pour un projet avec de l'incertitude métier ou technique réelle                                          |
| Planning risk-first (isoler et attaquer l'inconnu tôt) | Plus lent à produire, la première date annoncée est moins précise | La date devient plus fiable au fil du projet, les mauvaises surprises arrivent tôt et coûtent moins cher | Dès que le projet touche une règle métier mal documentée, une intégration externe, ou une donnée jamais auditée |

## Pièges classiques

- Annoncer une date avant d'avoir identifié les zones d'incertitude : le symptôme est un
  planning qui "glisse" de façon répétée, toujours de quelques jours, jamais annoncé d'un
  coup.
- Traiter la découverte comme une tâche de développement normale : le symptôme est
  l'incapacité à répondre "combien de temps pour découvrir X" sans donner le même chiffre
  que pour "implémenter X".
- Estimer en confondant "ce que je sais faire vite" avec "ce que le projet exige" : le
  symptôme est un planning détaillé sur les parties familières et vague sur les parties
  nouvelles.
- Refuser de nommer une inconnue par peur qu'elle fasse peur au sponsor : le symptôme est
  qu'elle ressort quand même, plus tard, sous forme de retard non expliqué.

## Ce que tu dois savoir défendre

1. Pourquoi un planning qui additionne des tâches connues sous-estime systématiquement,
   même quand chaque estimation individuelle est correcte.
2. Donne un exemple (dans un domaine autre que la refacturation d'énergie) d'une inconnue
   "connaissable à l'avance" qui, non cherchée, devient un retard non expliqué.
3. Pourquoi le coût de correction d'une hypothèse fausse augmente plus vite que le temps
   écoulé, et pas de façon linéaire.
