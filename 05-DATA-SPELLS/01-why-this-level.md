# Pourquoi ce niveau existe

## La scène
Le syndic d'immeuble facture les charges d'énergie une fois par trimestre. Le schéma de données a
été pensé un vendredi après-midi pour "juste stocker les relevés de compteur". Deux ans plus tard :
un tarif d'électricité a changé en cours de trimestre, un logement a changé de propriétaire au
milieu d'une période de facturation, et un relevé de compteur a été saisi deux fois par erreur par
deux gestionnaires différents le même jour. Le calcul de refacturation, qui tourne très bien sur les
données de test, produit des factures fausses pour douze logements, découvertes seulement quand un
copropriétaire conteste sa facture avec le relevé papier en main.

Le bug n'est pas dans le calcul. Il est dans le schéma : rien n'a été pensé pour qu'un tarif change
dans le temps, pour qu'un logement change de propriétaire, ou pour détecter un doublon de saisie.
Le calcul est correct sur un modèle de données qui ne représente pas la réalité.

## Ce qui se passe vraiment
Le code applicatif se réécrit facilement. Le schéma de données, non : il porte les données de
production, et chaque changement de structure est un projet en soi (migration, risque de perte,
coordination avec le code qui le lit). Un mauvais choix de modélisation au démarrage d'un projet est
souvent le choix technique le plus cher à corriger de tout le projet, précisément parce qu'il est
invisible au début — tout marche très bien avec dix lignes de données de test.

```text
Coût de corriger un mauvais choix       Moment où le coût est payé
──────────────────────────────         ──────────────────────────
Nom de variable mal choisi              Immédiat, trivial (renommage)
Fonction mal découpée                    Quelques heures (refactoring)
Endpoint API mal conçu                   Quelques jours (versionnage, migration clients)
Schéma de données mal modélisé           Des mois, souvent en pleine nuit,
                                          souvent après une perte de données réelle
```

## Pourquoi ce niveau ne parle pas que de SQL
Une bonne modélisation commence avant toute syntaxe : identifier les vraies entités du domaine, ce
qui doit rester vrai en toutes circonstances (les invariants), et ce qui change dans le temps. Le
SQL et l'ORM ne sont que la traduction de ces décisions. Un schéma SQL parfaitement normalisé qui
modélise le mauvais domaine reste un mauvais schéma.

## Le rôle du temps, sous-estimé partout
La plupart des schémas mal conçus partagent un même angle mort : ils modélisent l'état présent des
choses ("le tarif est de 0,21€/kWh") sans modéliser leur histoire ("le tarif était de 0,19€/kWh
jusqu'au 1er mars, puis 0,21€/kWh"). Toute donnée qui peut changer un jour a besoin d'une réponse
claire à la question : quand ce changement a-t-il eu lieu, et que doit-il se passer pour les
enregistrements qui existaient avant ?

## Ce que tu dois savoir défendre
- Donne un exemple concret où modéliser "l'état actuel" sans "l'historique" produit un résultat
  faux, pas juste incomplet.
- Pourquoi une erreur de modélisation de données coûte-t-elle structurellement plus cher à corriger
  qu'une erreur de code applicatif ?
- Pourquoi la modélisation doit-elle précéder le choix technique (SQL, ORM, NoSQL), et pas
  l'inverse ?
