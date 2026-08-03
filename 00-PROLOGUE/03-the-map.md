# La carte — les 16 niveaux

## La scène

Avant de partir en randonnée sur un itinéraire de plusieurs jours, tu regardes la carte
entière, pas seulement le premier sentier. Tu repères où sont les cols difficiles, où tu
pourras te ravitailler, où le dénivelé va te casser les jambes. Ce fichier est cette carte.
Tu n'as pas besoin de la mémoriser, mais tu dois savoir qu'elle existe et pourquoi l'ordre
n'est pas arbitraire.

## Ce qui se passe vraiment

Les 16 niveaux sont organisés en quatre arcs. Chaque arc construit une capacité que l'arc
suivant présuppose. Sauter un arc, c'est arriver au niveau 10 sans savoir pourquoi le
niveau 10 est nécessaire.

```text
ARC I — FONDATIONS DE PENSÉE (niveaux 00-03)
  00 Prologue        → les règles du jeu
  01 Mindset          → penser en systèmes, coût des décisions, incertitude
  02 Modélisation      → domaines métier, invariants, langage ubiquitaire
  03 Données & état    → cohérence, transactions, sources de vérité

ARC II — CONSTRUCTION (niveaux 04-08)
  04 Architecture applicative → frontières, couches, dépendances
  05 API & contrats            → versioning, compatibilité, erreurs
  06 Persistance avancée       → migrations, index, requêtes coûteuses
  07 Concurrence & parallélisme → verrous, files, idempotence
  08 Tests & vérification      → pyramide de tests, mutation testing, contrats

ARC III — SYSTÈME EN PRODUCTION (niveaux 09-12)
  09 Observabilité             → logs, métriques, traces, alerting utile
  10 Résilience & pannes       → dégradation, retries, circuit breakers
  11 Performance                → profiling, goulots d'étranglement, capacité
  12 Sécurité appliquée         → surface d'attaque, auth, secrets, audit

ARC IV — INGÉNIEUR EN CONTEXTE (niveaux 13-15)
  13 Travail en équipe technique → revues, dette, standards partagés
  14 Décision & influence         → arbitrages produit/technique, négociation
  15 Le boss final                → construire un système complet sous contrainte réelle
```

Chaque niveau débloque une capacité précise, pas un vague "tu en sais plus". Voici ce que
chaque arc rend possible concrètement.

### Arc I — Fondations de pensée

Sans cet arc, tu construis vite des systèmes qui s'effondrent dès que la réalité contredit
les hypothèses implicites que tu n'as jamais explicitées. Ce que tu débloques : la capacité
à modéliser un problème avant de coder, à évaluer le coût d'une décision avant de la prendre,
et à distinguer une donnée qui doit rester cohérente d'une donnée qui peut être approximative.

Exemple concret : à la fin de l'arc I, face à un système de gestion des adhésions d'un club
d'escalade (adhésions, accès aux murs par créneaux, assurance obligatoire), tu sais identifier
que "l'assurance valide" est un invariant qui doit bloquer l'accès en temps réel, alors que
"le nombre total d'adhérents ce mois-ci" peut être calculé de façon asynchrone sans risque.

### Arc II — Construction

Sans cet arc, tu sais quoi construire mais tu le construis dans une structure qui rend chaque
changement futur plus cher que le précédent. Ce que tu débloques : la capacité à découper un
système en composants dont les frontières résistent au changement, à concevoir des APIs qui
survivent à leurs propres évolutions, et à écrire des tests qui détectent de vraies
régressions plutôt que de la friction.

### Arc III — Système en production

Sans cet arc, ton système fonctionne en local et s'effondre silencieusement en production,
et tu l'apprends par un incident plutôt que par une mesure. Ce que tu débloques : la capacité
à voir ce qui se passe réellement dans un système que tu ne peux pas observer directement, à
concevoir pour l'échec plutôt que pour le cas nominal, et à raisonner sur la performance avec
des chiffres plutôt que des intuitions.

### Arc IV — Ingénieur en contexte

Sans cet arc, tu es un bon technicien isolé qui n'influence rien au-delà de son propre code.
Ce que tu débloques : la capacité à faire progresser une équipe entière (pas seulement toi),
à défendre un choix technique face à une pression produit légitime, et à livrer un système
complet, seul, sous une contrainte réelle et non simulée.

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Suivre les 16 niveaux dans l'ordre | Long (plusieurs mois selon rythme) | Modèle mental complet et cumulatif | Objectif : devenir un ingénieur senior généraliste solide |
| Faire uniquement l'Arc I puis III | Plus court, laisse des trous en Arc II | Débloque vite le raisonnement critique sans maîtriser la construction | Tu es déjà bon technicien mais faible en modélisation et en lecture de systèmes en prod |

## Pièges classiques

- Vouloir sauter à l'Arc III (observabilité, résilience) parce que "c'est plus excitant" sans
  avoir fait l'Arc II — le symptôme est de savoir nommer les concepts sans savoir les
  appliquer à un système qu'on vient de construire soi-même.
- Traiter l'Arc IV comme "optionnel, c'est du soft skill" — le symptôme est de rester bloqué
  au rang d'exécutant senior sans jamais influencer les décisions d'équipe.
- Refaire un niveau entier après un échec de boss-fight au lieu de cibler la leçon précise en
  cause — perte de temps, et le symptôme est la démotivation par lassitude.

## Ce que tu dois savoir défendre

1. Pourquoi l'Arc I doit précéder l'Arc II, avec un exemple de ce qui casse si on inverse.
2. Donne un exemple concret de capacité débloquée par l'Arc III que l'Arc II ne peut pas
   donner, même en la faisant très sérieusement.
3. Pourquoi l'Arc IV existe dans un curriculum technique — qu'est-ce qui casse chez un
   ingénieur purement technique sans cet arc.
