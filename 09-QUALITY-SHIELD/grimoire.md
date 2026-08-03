# Grimoire : Niveau 09, Quality Shield

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| Les quatre couches du bouclier | Tests (avant production), observabilite (en production, en minutes), revue + CI (avant le code partage), incident/postmortem (apres, sans recidive). Aucune couche seule ne suffit. | Une seule couche laisse un trou : un bug non teste, non observe, non revu, ou qui se reproduit sans postmortem. | Quelle couche de ton bouclier est la plus faible aujourd'hui, et que laisse-t-elle passer ? |
| Priorisation des tests | Cout reel de la panne x probabilite qu'un bug s'y glisse = priorite de test. Jamais "c'est facile a tester" ou un pourcentage de couverture vise. | On teste ce qui est facile a tester, pas ce qui coute cher si ca casse, et le vrai risque passe au travers. | Quel est le test le plus prioritaire de ton systeme selon cette formule, et pourquoi ? |
| Pyramide vs trophee | Pyramide : beaucoup d'unitaires, bon pour la logique pure isolee. Trophee : accent sur l'integration, bon quand le risque vient des frontieres (base reelle, reseau, format externe). | On applique une pyramide a un systeme dont le risque vient des frontieres, et les tests unitaires ne voient jamais le vrai bug. | Ton systeme merite-t-il une pyramide ou un trophee, et pourquoi ? |
| Observabilite : logs, metriques, traces | Logs structures ("que s'est-il passe, precisement"), metriques ("combien, depuis quand ca derive"), traces ("ou est passe le temps, ou ca a casse"). | Un incident survient et personne ne peut repondre a une question surgie en plein incident. | Peux-tu repondre a une question imprevue pendant un incident avec tes logs, metriques et traces actuels ? |
| Revue de code : machine vs humain | La machine (linter, formateur) traite style, indentation, nommage mecanique. L'humain traite logique metier, cas limites, risque de securite, dette d'architecture. | La revue humaine se perd en commentaires de style, et rate le vrai risque metier ou de securite. | Ta derniere revue de code a-t-elle porte sur un scenario concret, ou sur une preference esthetique ? |
| Test flaky | Test dont le resultat varie sans changement de code, typiquement a cause du temps reel, de l'ordre d'execution ou d'un etat partage mal isole. | Un test flaky relance indefiniment "jusqu'a ce qu'il passe" masque un vrai bug intermittent. | Que fais-tu d'un test flaky : le corriger, le desactiver avec un ticket, ou le relancer en boucle ? |
| Incident commander et les deux temps | Personne qui centralise les decisions pendant un incident, pas necessairement l'expert technique. Pendant : reduire l'impact vite. Apres : comprendre la cause structurelle sans urgence de temps. | Reparation et analyse se melangent, et l'urgence du "pendant" empeche de bien comprendre le "apres", ou l'inverse retarde la reparation. | Sais-tu distinguer, dans ton dernier incident, ce qui relevait du "pendant" et ce qui relevait de "l'apres" ? |
| Postmortem sans blame | Analyse d'incident centree sur le systeme, jamais sur la personne, avec chronologie factuelle, cause immediate et structurelle, et actions datees assignees. | Une equipe qui craint le blame cache l'information utile, et le meme incident se reproduit. | Ton dernier postmortem contient-il des actions assignees et datees, ou juste "faire plus attention" ? |

## Comportements evalues en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'échec |
| --- | --- | --- |
| Décision sous incertitude | Tranche avec un critère lié à l'impact utilisateur réel, assume la part de risque | Ne tranche pas, ou tranche sans critère explicite |
| Coordination de l'équipe | Centralise clairement la décision, empêche les actions parallèles non coordonnées | Laisse les deux développeurs agir chacun de leur côté |
| Séparation réparation/analyse (compromis nommé et assumé) | Explique clairement pourquoi il répare avant de comprendre en détail, et reporte l'analyse | Cherche la cause exacte avant d'agir sur l'impact |
| Communication pendant l'incident (honnêteté sur ce qu'on ne sait pas) | Prévoit une communication régulière pendant l'incident, y compris "on ne sait pas encore" | Ne communique qu'une fois résolu |

## Prioriser les tests : la seule formule qui compte

```text
cout reel de la panne x probabilite qu'un bug s'y glisse = priorite de test
```

Jamais : "c'est facile a tester" ou "pourcentage de couverture vise".

## Pyramide vs trophée

```text
Pyramide : beaucoup d'unitaires   --> bon pour la logique pure, isolee, calculable
Trophee   : accent sur l'integration --> bon quand le risque vient des frontieres
            (base reelle, reseau, format de donnees externe)
```

## Observabilité : trois piliers, trois questions

- **Logs structurés** (champs nommés, jamais du texte libre) --> "que s'est-il passé,
  précisément, ici".
- **Métriques** (compteur, jauge, histogramme) --> "combien, et depuis quand ça dérive".
- **Traces** (suivi à travers les services) --> "où est passé le temps, où ça a cassé".

Alerter sur un symptôme utilisateur visible, jamais sur une cause interne isolée (CPU,
mémoire) sans lien démontré avec un impact réel.

## Revue de code : ce qui revient à la machine, ce qui revient à l'humain

```text
Machine (linter, formateur) : style, indentation, imports, nommage mecanique
Humain (revue) : logique metier, cas limites, risque de securite, dette d'architecture
```

Un bon commentaire de revue pointe un scénario concret, jamais une préférence esthétique.

## CI : ordre des étapes, et la règle du test flaky

```text
push --> lint + typage (rapide) --> tests unitaires --> tests d'integration --> build + securite
```

Un test flaky se corrige ou se désactive avec un ticket de suivi : jamais relancé
indéfiniment "jusqu'à ce qu'il passe".

## Incident : deux temps qui ne se mélangent jamais

```text
Pendant  : reduire l'impact vite, une seule personne decide (incident commander),
           rollback avant comprehension complete si besoin.
Apres    : comprendre la cause structurelle, sans urgence de temps, actions assignees et datees.
```

## Postmortem sans blâme : structure en quatre temps

1. Chronologie factuelle, horodatée, basée sur logs et métriques réels.
2. Cause immédiate (le bug précis) ET cause structurelle (pourquoi le système l'a laissé
   passer sans alerte).
3. Ce qui a bien fonctionné, à préserver.
4. Actions concrètes, assignées à une personne, avec une date : jamais "faire plus attention".
