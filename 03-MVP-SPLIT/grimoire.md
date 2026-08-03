# Grimoire : MVP Split

## Vocabulaire

- **Tranche verticale** : découpage qui traverse toute la stack et livre un scénario complet
  et utilisable, même réduit en ampleur.
- **Couche horizontale** : découpage qui prépare une partie de l'infrastructure (base de
  données, authentification générique) mais ne livre rien d'utilisable seule.
- **Invariant d'une feature** : la règle dont la violation rend la fonctionnalité dangereuse
  ou trompeuse, plutôt que juste limitée en portée.
- **Ligne de coupe** : l'endroit précis où on réduit l'ampleur d'une feature sans toucher à
  son invariant.
- **Feature flag** : interrupteur de configuration qui active ou désactive un comportement
  sans redéployer, utile pour livrer du code incomplet en sécurité.
- **Estimation honnête** : fourchette accompagnée de ses sources d'incertitude explicites,
  par opposition à un chiffre unique donné à l'instinct.
- **Effet tunnel** : dérive où une équipe continue sur un plan optimiste sans signaler les
  écarts en cours de route, jusqu'à un dépassement révélé trop tard pour être corrigé.
- **Coût d'opportunité** : ce qu'on sacrifie ailleurs si on accepte une demande de périmètre
  supplémentaire : l'argument qui rend un refus compréhensible plutôt qu'arbitraire.
- **Dette technique assumée** : raccourci pris consciemment, écrit quelque part, avec une
  échéance de remboursement.
- **Dette technique subie** : raccourci pris sous pression, jamais écrit, découvert plus tard
  comme un incident.

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
        │
        ├── Identifie l'invariant : que se passe-t-il si on le viole ?
        │       Dangereux / trompeur → ne jamais couper cette partie
        │       Juste limité → peut être réduit sans casser l'intention
        │
        ├── Cherche un axe de réduction qui ne touche pas l'invariant :
        │       nombre d'utilisateurs, volume, canal, fréquence,
        │       automatisation partielle avec validation manuelle
        │
        └── Vérifie : la version coupée reste-t-elle honnête pour
                l'utilisateur qui en bénéficie déjà ?
                Non → mauvaise coupe, recommencer
                Oui → bonne coupe, prête à livrer
```

## Trois réponses à une demande de périmètre supplémentaire

```text
OUI            → coût d'opportunité acceptable, arbitré consciemment
NON            → viole un non-objectif déjà écrit, à rappeler explicitement
PAS MAINTENANT → bonne idée, mauvais moment, avec condition de réexamen
                 écrite (sinon c'est un non déguisé)
```

## Phrases à répéter avant chaque estimation

- "Une fourchette avec ses raisons vaut mieux qu'un chiffre qui rassure sur le moment."
- "Découper avant d'estimer, jamais l'inverse."
- "Un écart signalé tôt et petit vaut mieux qu'un écart découvert tard et grand."
- "Chaque dette technique doit être écrite, sinon elle n'existe pas : jusqu'au jour où elle
  explose."
