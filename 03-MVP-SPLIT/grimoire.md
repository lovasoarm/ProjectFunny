# Grimoire : Niveau 03, MVP Split

Mémo à ouvrir quand il faut découper une feature sous pression de planning. Sert à trancher
où couper, pas à réviser la théorie du découpage.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Tranche verticale | Découpage qui traverse toute la stack et livre un scénario complet et utilisable, même réduit. | `printf "tranche1: creation_urgence_simple end-to-end\n" >> plan.md` | plat complet simplifié servi en entier / traversée courte mais complète en montagne |
| Couche horizontale | Découpage qui prépare une partie de l'infrastructure mais ne livre rien d'utilisable seule. | `printf "couche: auth_generique (aucune valeur seule)\n"` | mise en place sans aucun plat servi / gréement monté sans navigation possible |
| Invariant d'une feature | La règle dont la violation rend la fonctionnalité dangereuse ou trompeuse. | `const invariant = (r) => r.veterinaireDisponible === true;` | règle de sécurité non négociable aux urgences / point d'ancrage qui ne doit jamais lâcher |
| Ligne de coupe | L'endroit précis où on réduit l'ampleur d'une feature sans toucher à son invariant. | `printf "coupe: 1 seul cabinet pilote, meme invariant\n"` | menu réduit mais sans plat mensonger / itinéraire raccourci sans sauter d'étape de sécurité |
| Feature flag | Interrupteur de configuration qui active ou désactive un comportement sans redéployer. | `printf "FEATURE_PAIEMENT_EN_LIGNE=false\n" >> .env` | interrupteur du technicien en régie pendant le show / vanne coupée sans arrêter le bateau |
| Estimation honnête | Fourchette accompagnée de ses sources d'incertitude explicites. | `printf "estimation: 3-5j; incertitude: dispo_api_tiers\n"` | fourchette de temps de service annoncée en cuisine / marge de sécurité annoncée avant un sommet |
| Effet tunnel | Dérive où une équipe continue sur un plan optimiste sans signaler les écarts en cours de route. | `printf "point_controle: chaque vendredi, ecart vs plan\n" >> suivi.md` | absence de point météo en pleine mer / absence de debrief à mi-service |
| Coût d'opportunité | Ce qu'on sacrifie ailleurs si on accepte une demande de périmètre supplémentaire. | `printf "oui_a: X; sacrifie: Y cette semaine\n"` | accepter une table de plus au prix d'un retard partout ailleurs / accepter un détour au prix du sommet du jour |
| Dette technique assumée | Raccourci pris consciemment, écrit quelque part, avec une échéance de remboursement. | `printf "dette: mock_paiement; rembourser_avant: v1.2\n" >> dette.yml` | pansement provisoire assumé aux urgences / réparation de fortune notée pour le retour au port |
| Dette technique subie | Raccourci pris sous pression, jamais écrit, découvert plus tard comme un incident. | `git log --grep="quick fix" --oneline` | improvisation non tracée en régie qui refait surface en direct / corde mal notée qui lâche en pleine ascension |

## Défense orale

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Tranche verticale | Rien n'est utilisable avant que tout soit fini, aucune validation terrain avant la fin | Ta première tranche livre-t-elle un scénario complet, ou juste une brique technique ? |
| Couche horizontale | Des semaines de travail sans aucun retour utilisateur possible avant l'assemblage final | Cette couche, seule, apporte-t-elle une valeur observable à quelqu'un ? |
| Invariant d'une feature | Tu coupes au mauvais endroit et livres quelque chose qui ment à l'utilisateur | Que se passe-t-il concrètement si cette règle est violée ? |
| Ligne de coupe | La coupe touche l'invariant : la version réduite devient dangereuse ou trompeuse | Sur quel axe as-tu réduit sans toucher l'invariant ? |
| Feature flag | Du code incomplet part en production sans filet, ou bloque un déploiement complet | Comment reviens-tu en arrière si ce comportement pose problème en production ? |
| Estimation honnête | Un chiffre unique rassure sur le moment puis explose sans qu'on sache pourquoi | Quelle est la source d'incertitude principale de cette estimation ? |
| Effet tunnel | Le dépassement est découvert au dernier moment, sans marge pour réagir | Quel point de contrôle rapproché aurait révélé cet écart plus tôt ? |
| Coût d'opportunité | Un refus paraît arbitraire, ou un oui coûte une autre priorité sans arbitrage conscient | Qu'est-ce que ce oui te fait sacrifier ailleurs, précisément ? |
| Dette technique assumée | Le raccourci se perd dans le code, personne ne sait qu'il faut le rembourser | Quelle est l'échéance de remboursement de cette dette, et où est-elle écrite ? |
| Dette technique subie | Le raccourci explose en production, traité comme une surprise alors qu'il était prévisible | Comment aurait-on pu transformer cette dette subie en dette assumée à temps ? |

Grille détaillée : voir [boss-fight.md](./boss-fight.md).

## Checklist avant de découper un MVP

- [ ] Le besoin réel et les non-objectifs du niveau 02 sont déjà écrits et validés.
- [ ] Chaque tranche envisagée livre un scénario complet et utilisable seule.
- [ ] L'invariant de chaque feature complexe est identifié avant de décider où couper.
- [ ] Chaque estimation est une fourchette avec au moins une source d'incertitude nommée.
- [ ] Des points de contrôle rapprochés sont prévus pour éviter l'effet tunnel.
- [ ] Une réponse de type "coût d'opportunité" est prête avant qu'une demande de périmètre
      supplémentaire n'arrive.

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

## Si tu rates le boss-fight

Relis d'abord le critère "identification de l'invariant" : c'est lui qui plafonne la note.
Reprends la scène en listant séparément, avant de répondre, ce qui touche l'invariant et ce
qui est un cas limite gérable. Relis ensuite l'arbre de décision ci-dessus. Attends 48 h avant
de retenter le boss-fight pour juger la scène à froid. Si l'échec se reproduit sur le même
critère, redescends au niveau 02 relire "non-objectif" : couper au bon endroit suppose déjà
de savoir ce qu'on a refusé de faire.
