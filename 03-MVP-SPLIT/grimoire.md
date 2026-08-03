# Grimoire : MVP Split

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| Tranche verticale | Découpage qui traverse toute la stack et livre un scénario complet et utilisable, même réduit en ampleur | Rien n'est utilisable avant que tout soit fini, aucune validation terrain avant la fin | Ta première tranche livre-t-elle un scénario complet, ou juste une brique technique ? |
| Couche horizontale | Découpage qui prépare une partie de l'infrastructure (base de données, authentification générique) mais ne livre rien d'utilisable seule | Des semaines de travail sans aucun retour utilisateur possible avant l'assemblage final | Cette couche, seule, apporte-t-elle une valeur observable à quelqu'un ? |
| Invariant d'une feature | La règle dont la violation rend la fonctionnalité dangereuse ou trompeuse, plutôt que juste limitée en portée | Tu coupes au mauvais endroit et livres quelque chose qui ment à l'utilisateur | Que se passe-t-il concrètement si cette règle est violée ? |
| Ligne de coupe | L'endroit précis où on réduit l'ampleur d'une feature sans toucher à son invariant | La coupe touche l'invariant : la version réduite devient dangereuse ou trompeuse | Sur quel axe (volume, canal, fréquence, automatisation) as-tu réduit sans toucher l'invariant ? |
| Feature flag | Interrupteur de configuration qui active ou désactive un comportement sans redéployer, utile pour livrer du code incomplet en sécurité | Du code incomplet part en production sans filet, ou bloque un déploiement complet | Comment reviens-tu en arrière si ce comportement pose problème en production ? |
| Estimation honnête | Fourchette accompagnée de ses sources d'incertitude explicites, par opposition à un chiffre unique donné à l'instinct | Un chiffre unique rassure sur le moment puis explose sans qu'on sache pourquoi | Quelle est la source d'incertitude principale de cette estimation ? |
| Effet tunnel | Dérive où une équipe continue sur un plan optimiste sans signaler les écarts en cours de route, jusqu'à un dépassement révélé trop tard pour être corrigé | Le dépassement est découvert au dernier moment, sans marge pour réagir | Quel point de contrôle rapproché aurait révélé cet écart plus tôt ? |
| Coût d'opportunité | Ce qu'on sacrifie ailleurs si on accepte une demande de périmètre supplémentaire | Un refus paraît arbitraire, ou un oui coûte une autre priorité sans que personne ne l'ait décidé consciemment | Qu'est-ce que ce oui te fait sacrifier ailleurs, précisément ? |
| Dette technique assumée | Raccourci pris consciemment, écrit quelque part, avec une échéance de remboursement | Le raccourci se perd dans le code, personne ne sait qu'il faut le rembourser | Quelle est l'échéance de remboursement de cette dette, et où est-elle écrite ? |
| Dette technique subie | Raccourci pris sous pression, jamais écrit, découvert plus tard comme un incident | Le raccourci explose en production, traité comme une surprise alors qu'il était prévisible | Comment aurait-on pu transformer cette dette subie en dette assumée à temps ? |

## Comportements évalués en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'échec |
| --- | --- | --- |
| Identification de l'invariant (justification par un mécanisme) | La décision distingue clairement ce qui est vital de ce qui est un cas limite gérable | Tous les cas cités sont traités comme également critiques, ou tous comme négligeables sans distinction |
| Cohérence entre les deux messages | Le message au développeur et celui au directeur racontent la même réalité, sans double discours | Le développeur et le directeur reçoivent des versions incompatibles de la situation |
| Gestion de la pression sans céder ni ignorer (compromis nommé et assumé) | La décision tient compte des deux contraintes réelles (qualité et calendrier) sans sacrifier silencieusement l'une des deux | La décision ignore une des deux pressions sans l'assumer explicitement |
| Honnêteté sur l'état d'avancement (honnêteté sur ce qu'on ne sait pas) | Le directeur reçoit une information vraie, même si elle implique une limite assumée pour vendredi | Le message enjolive l'état réel pour éviter un désagrément immédiat |
| Cap sur l'intention de l'outil | La version livrée vendredi, même réduite, reste honnête pour l'utilisateur qui s'en sert | La version livrée donne une fausse impression de fiabilité qu'elle n'a pas |

## Checklist avant de découper un MVP

- [ ] Le besoin réel et les non-objectifs du niveau 02 sont déjà écrits et validés.
- [ ] Chaque tranche envisagée livre un scénario complet et utilisable seule, pas une couche
      technique isolée.
- [ ] L'invariant de chaque feature complexe est identifié avant de décider où couper.
- [ ] Chaque estimation est une fourchette avec au moins une source d'incertitude nommée.
- [ ] Des points de contrôle rapprochés sont prévus pour éviter l'effet tunnel.
- [ ] Une réponse de type "coût d'opportunité" est prête avant qu'une demande de périmètre
      supplémentaire n'arrive, pas improvisée sur le moment.

## Arbre de décision rapide : où couper ?

```text
Une feature semble trop grosse pour la première tranche.

  +-- Identifie l'invariant : que se passe-t-il si on le viole ?
  |       Dangereux / trompeur -> ne jamais couper cette partie
  |       Juste limité -> peut être réduit sans casser l'intention
  |
  +-- Cherche un axe de réduction qui ne touche pas l'invariant :
  |       nombre d'utilisateurs, volume, canal, fréquence,
  |       automatisation partielle avec validation manuelle
  |
  \-- Vérifie : la version coupée reste-t-elle honnête pour
          l'utilisateur qui en bénéficie déjà ?
          Non -> mauvaise coupe, recommencer
          Oui -> bonne coupe, prête à livrer
```

## Trois réponses à une demande de périmètre supplémentaire

```text
OUI            -> coût d'opportunité acceptable, arbitré consciemment
NON            -> viole un non-objectif déjà écrit, à rappeler explicitement
PAS MAINTENANT -> bonne idée, mauvais moment, avec condition de réexamen
                  écrite (sinon c'est un non déguisé)
```

## Phrases à répéter avant chaque estimation

- "Une fourchette avec ses raisons vaut mieux qu'un chiffre qui rassure sur le moment."
- "Découper avant d'estimer, jamais l'inverse."
- "Un écart signalé tôt et petit vaut mieux qu'un écart découvert tard et grand."
- "Chaque dette technique doit être écrite, sinon elle n'existe pas : jusqu'au jour où elle
  explose."
