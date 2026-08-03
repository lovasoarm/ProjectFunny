# Monolithe modulaire vs services : critères de décision, coûts réels

## Le piège

Une plateforme de refacturation d'énergie pour des copropriétés grandit : elle est passée de
20 à 400 immeubles clients en un an. L'équipe technique, en lisant des articles de blog
d'entreprises bien plus grosses, décide de "passer aux microservices" pour "scaler" : un
service de facturation, un service de relevés de compteurs, un service de notifications, un
service d'authentification, chacun avec sa propre base de données et déployé indépendamment.
Six mois plus tard, l'équipe de cinq développeurs passe plus de temps à déboguer des appels
réseau entre services, à gérer des versions incompatibles d'API internes, et à faire tourner
localement six services avec Docker Compose pour tester une fonctionnalité qui touche trois
d'entre eux, qu'à livrer des fonctionnalités. Un simple changement de règle de calcul de TVA,
qui aurait pris deux heures dans l'ancien monolithe, prend trois jours parce qu'il faut
coordonner un déploiement synchronisé entre le service de facturation et le service de
relevés.

## Ce qui se passe vraiment

### Le choix n'est pas technique, il est organisationnel

La décision entre monolithe modulaire et services distincts ne se tranche pas d'abord par
"combien d'utilisateurs" ou "combien de données", contrairement à l'intuition la plus
répandue. Elle se tranche d'abord par une question organisationnelle : **combien d'équipes,
avec quel besoin d'autonomie de déploiement, travaillent sur ce système ?**

La loi de Conway (l'architecture d'un système finit par refléter la structure de
communication de l'organisation qui le construit) fonctionne aussi dans l'autre sens : imposer
une architecture en services distincts à une équipe de cinq personnes qui se parlent tous les
jours crée une friction permanente, parce que l'architecture impose des frontières de
communication (API, contrats réseau) là où l'équipe n'a besoin d'aucune frontière : juste de
bien organiser son code.

```text
1 équipe de 5 personnes, forte communication quotidienne
   → un monolithe modulaire (frontières internes claires, un seul déploiement)
     capture 100% du bénéfice de modularité, 0% du coût réseau

3 équipes autonomes, releases indépendantes, domaines métier bien séparés
   → des services distincts commencent à avoir du sens, parce que la frontière
     organisationnelle existe déjà et a besoin d'un miroir technique
```

### Ce qu'un monolithe modulaire est réellement

Un monolithe modulaire n'est pas "un gros fichier". C'est une seule unité de déploiement (un
seul processus, ou un petit nombre) organisée en modules internes qui respectent les règles
de couplage et de couches vues dans les leçons précédentes : frontières claires,
dépendances dirigées, une seule base de code, mais pas d'appel réseau entre les parties.

```text
Monolithe "gros fichier" (mauvais, ce n'est pas ce dont on parle) :
  app.js  →  12 000 lignes, tout mélangé, aucune frontière interne

Monolithe MODULAIRE (ce qui est recommandé par défaut) :
  src/
    facturation/      → module cohérent, frontière claire, expose une API interne
    releves/          → idem
    notifications/    → idem
    partage/          → utilitaires génuinement transverses (dates, monnaie)
  → un seul déploiement, une seule base de données (ou schémas séparés),
    zéro appel réseau entre modules, communication en appels de fonction typés
```

Le monolithe modulaire donne 80% des bénéfices annoncés des microservices (frontières
claires, responsabilités isolées, testabilité par module) pour une fraction du coût
opérationnel (pas de réseau à gérer entre les parties, pas de cohérence distribuée, un seul
pipeline de déploiement, un seul environnement à faire tourner en local).

### Les coûts réels des services distincts, non négociables

Ces coûts existent dès le premier service séparé, et ils sont souvent sous-estimés parce
qu'ils n'apparaissent pas immédiatement : ils s'accumulent avec le temps et le nombre de
services.

```text
Coût                          Ce que ça signifie concrètement
─────────────────────────────────────────────────────────────────────────
Réseau                        Chaque appel entre modules devient un appel HTTP/RPC,
                               qui peut échouer, timeout, ou répondre lentement —
                               ce qui était une garantie (un appel de fonction ne
                               "timeout" jamais) devient une incertitude à gérer.

Cohérence distribuée          Une opération qui touchait plusieurs "modules" en une
                               transaction locale devient une opération distribuée
                               (sagas, événements, compensation) : bien plus
                               complexe à raisonner et à déboguer.

Déploiement et versionnage    Chaque service a son propre cycle de version ; un
                               changement de contrat entre deux services doit gérer
                               la compatibilité ascendante (cf. Niveau 07).

Observabilité                 Un bug qui traverse 3 services nécessite du tracing
                               distribué pour être compris : un simple débogueur
                               local ne suffit plus.

Coût d'infrastructure          Chaque service tourne dans son propre processus,
                               souvent sa propre base : plus de serveurs, plus de
                               configuration, plus de surface à sécuriser.

Duplication de logique         Sans discipline forte, chaque service réimplémente
                               ses propres validations, son propre client HTTP, sa
                               propre gestion d'erreurs : la cohésion se perd.
```

Aucun de ces coûts n'est un argument contre les services distincts dans l'absolu : ce sont
des coûts réels, à mettre en balance avec le bénéfice réel obtenu, jamais acceptés par défaut
"parce que c'est ce que font les grandes entreprises".

### Critères de décision, dans l'ordre où les vérifier

```text
1. Autonomie organisationnelle réelle
   Plusieurs équipes ont-elles besoin de déployer indépendamment, sans se coordonner ?
   → Non : monolithe modulaire. Oui : envisager des services le long de cette frontière.

2. Isolation de charge ou de scalabilité radicalement différente
   Une partie du système a-t-elle des besoins de scalabilité ou de résilience
   fondamentalement différents du reste (ex: un module de calcul lourd vs le reste léger) ?
   → Non : monolithe modulaire. Oui : ce module précis peut devenir un service séparé,
     pas besoin de tout découper pour autant.

3. Isolation de risque ou de conformité
   Une partie du système a-t-elle des contraintes de sécurité/conformité qui justifient
   un périmètre technique et réseau isolé (ex: données de paiement, données médicales) ?
   → Oui : ce périmètre précis peut justifier un service séparé, indépendamment du reste.

4. Maturité opérationnelle de l'équipe
   L'équipe a-t-elle l'expérience et l'outillage (observabilité, CI/CD par service,
   gestion de la compatibilité de contrats) pour absorber le coût listé plus haut ?
   → Non : le coût dépassera le bénéfice, quelle que soit la qualité de la décision
     architecturale sur le papier.
```

Le cas de la plateforme de refacturation d'énergie coche zéro de ces quatre critères au
moment de la décision : une seule équipe, pas de besoin de scalabilité différenciée, pas de
contrainte de conformité isolée, pas d'expérience préalable en systèmes distribués. Le choix
des microservices n'a résolu aucun problème réel et a ajouté tous les coûts listés plus haut.

### La bonne séquence dans le temps

L'erreur symétrique existe aussi : rester en monolithe non modulaire (le "gros fichier")
jusqu'à ce que la douleur devienne insupportable, puis tenter une réécriture complète en
microservices sous pression. La bonne séquence, dans l'immense majorité des cas réels,
ressemble à ceci :

```text
Étape 1 : monolithe modulaire dès le départ (frontières internes claires, dès le premier jour)
Étape 2 : quand un critère de décision devient concrètement vrai (pas hypothétique),
          extraire UN module précis en service séparé : jamais tout d'un coup
Étape 3 : répéter l'étape 2 uniquement là où un critère réel l'exige

Un monolithe modulaire bien découpé se découpe en services facilement, module par module,
parce que les frontières existent déjà dans le code. Un monolithe mal modularisé ne peut
pas être découpé proprement : il doit d'abord être refactoré en interne, sinon le
"découpage en microservices" ne fait que déplacer le désordre derrière un réseau, ce qui
le rend plus cher à corriger, pas moins.
```

Analogie : Analogie : choisir entre monolithe modulaire et services, c'est décider si l'atelier de
menuiserie fabrique un meuble d'un seul bloc ou en modules livrés séparément, et décider si
une cordée avance groupée ou se scinde en équipes autonomes en course en montagne.
Où l'analogie casse : un menuisier ou une cordée peuvent changer d'avis en cours de route sans
coût organisationnel durable. Scinder un système en services crée des frontières réseau, des
équipes et des contrats qui coûtent cher à refusionner bien après la décision initiale.

## Compromis

| Option                                        | Coût                                                                                 | Bénéfice                                                                          | Quand choisir                                                                                            |
| --------------------------------------------- | ------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| Monolithe non modulaire                       | Rayon d'explosion incontrôlé, dette qui s'accumule vite                              | Vitesse de démarrage maximale                                                     | Prototype, preuve de concept, durée de vie courte assumée                                                |
| Monolithe modulaire                           | Discipline nécessaire pour maintenir les frontières internes                         | Frontières claires, un seul déploiement, coût opérationnel minimal                | Choix par défaut pour la quasi-totalité des produits, quelle que soit la taille de l'équipe au démarrage |
| Extraction ciblée d'un service précis         | Coût réseau et opérationnel pour ce module précis uniquement                         | Isolation de charge, de risque ou de conformité là où c'est réellement nécessaire | Un critère de décision concret et vérifié s'applique à un module identifié                               |
| Services distribués généralisés dès le départ | Tous les coûts listés plus haut, multipliés par le nombre de services, dès le jour 1 | Autonomie totale entre équipes, scalabilité indépendante                          | Plusieurs équipes autonomes existent déjà ET la maturité opérationnelle est en place                     |

## Pièges classiques

- **Le choix par imitation.** Symptôme : "on fait des microservices parce que Netflix/Amazon
  le font", sans qu'aucun des quatre critères de décision ne soit vérifié pour le projet en
  cours.
- **Le découpage par couche technique plutôt que par domaine métier.** Symptôme : un
  "service base de données", un "service API", un "service frontend" : ce découpage ne réduit
  aucun couplage réel, il déplace juste le monolithe derrière plusieurs réseaux.
- **La réécriture complète sous pression.** Symptôme : après des mois de douleur avec un
  monolithe non modulaire, l'équipe décide de tout réécrire en services distincts d'un coup,
  sans étape intermédiaire de modularisation interne : le taux d'échec de ce type de
  réécriture complète est élevé et documenté dans l'industrie.
- **Le monolithe modulaire jamais vraiment modulaire.** Symptôme : des dossiers nommés
  "modules" existent, mais rien n'empêche techniquement un module d'importer l'intérieur d'un
  autre : la frontière est une convention non appliquée, donc elle finit par disparaître.
- **L'extraction de service sans revoir le contrat.** Symptôme : un module extrait en service
  séparé garde exactement les mêmes appels de fonction, transformés mécaniquement en appels
  réseau : sans jamais repenser la gestion d'erreurs, de timeout, ni la granularité des
  appels, ce qui produit un système lent et fragile (voir Niveau 07 sur les erreurs et
  performances réseau).

## Ce que tu dois savoir défendre

- Explique pourquoi la décision monolithe/services est d'abord organisationnelle avant
  d'être technique, avec un exemple concret.
- Liste les coûts réels des services distincts que la plupart des articles qui les
  recommandent omettent de mentionner.
- Pour un projet que tu connais, applique les quatre critères de décision et justifie le
  choix qui en résulte.
