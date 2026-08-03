# Grimoire : Niveau 09, Quality Shield

## Les quatre couches du bouclier, et ce que chacune trouve

```
Tests            → trouve le problème AVANT la production
Observabilité    → trouve le problème EN PRODUCTION, en minutes
Revue + CI       → trouve le problème AVANT qu'il entre dans le code partagé
Incident/postmortem → traite le problème APRÈS, sans qu'il se reproduise
```

Aucune couche seule ne suffit. C'est la superposition qui protège.

## Prioriser les tests : la seule formule qui compte

```
coût réel de la panne × probabilité qu'un bug s'y glisse = priorité de test
```

Jamais : "c'est facile à tester" ou "pourcentage de couverture visé".

## Pyramide vs trophée

```
Pyramide : beaucoup d'unitaires   → bon pour la logique pure, isolée, calculable
Trophée   : accent sur l'intégration → bon quand le risque vient des frontières
            (base réelle, réseau, format de données externe)
```

## Observabilité : trois piliers, trois questions

- **Logs structurés** (champs nommés, jamais du texte libre) → "que s'est-il passé,
  précisément, ici".
- **Métriques** (compteur, jauge, histogramme) → "combien, et depuis quand ça dérive".
- **Traces** (suivi à travers les services) → "où est passé le temps, où ça a cassé".

Alerter sur un symptôme utilisateur visible, jamais sur une cause interne isolée (CPU,
mémoire) sans lien démontré avec un impact réel.

## Revue de code : ce qui revient à la machine, ce qui revient à l'humain

```
Machine (linter, formateur) : style, indentation, imports, nommage mécanique
Humain (revue) : logique métier, cas limites, risque de sécurité, dette d'architecture
```

Un bon commentaire de revue pointe un scénario concret, jamais une préférence esthétique.

## CI : ordre des étapes, et la règle du test flaky

```
push → lint + typage (rapide) → tests unitaires → tests d'intégration → build + sécurité
```

Un test flaky se corrige ou se désactive avec un ticket de suivi : jamais relancé
indéfiniment "jusqu'à ce qu'il passe".

## Incident : deux temps qui ne se mélangent jamais

```
Pendant  : réduire l'impact vite, une seule personne décide (incident commander),
           rollback avant compréhension complète si besoin.
Après    : comprendre la cause structurelle, sans urgence de temps, actions assignées et datées.
```

## Postmortem sans blâme : structure en quatre temps

1. Chronologie factuelle, horodatée, basée sur logs et métriques réels.
2. Cause immédiate (le bug précis) ET cause structurelle (pourquoi le système l'a laissé
   passer sans alerte).
3. Ce qui a bien fonctionné, à préserver.
4. Actions concrètes, assignées à une personne, avec une date : jamais "faire plus attention".

## Vocabulaire à tenir prêt à l'oral

- **Test flaky** : test dont le résultat varie sans changement de code, typiquement à cause
  du temps réel, de l'ordre d'exécution ou d'un état partagé mal isolé.
- **Incident commander** : personne qui centralise les décisions pendant un incident, pas
  nécessairement l'experte technique du sujet en cause.
- **Cause immédiate / cause structurelle** : le bug précis contre la raison pour laquelle le
  système a laissé ce bug produire autant de dégâts sans alerte.
- **Monitoring / observabilité** : dashboards prévus à l'avance contre capacité à répondre à
  une question surgie en plein incident.
- **Postmortem sans blâme** : analyse d'incident centrée sur le système, jamais sur la
  personne, parce qu'une équipe qui craint le blâme cache l'information utile.
