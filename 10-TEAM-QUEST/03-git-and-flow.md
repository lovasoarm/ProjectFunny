# Branches, PR, releases, feature flags

## Le piège

L'équipe qui construit le système de refacturation d'énergie pour un syndic de 40 immeubles
choisit, sans y réfléchir, le flux Git utilisé sur le dernier projet de chacun de ses trois
développeurs : trois flux différents, jamais discutés ensemble. Karim crée une branche par
fonctionnalité et la garde ouverte trois semaines "le temps de bien la finir". Léa pousse
directement sur la branche principale, habituée à un ancien projet solo. Youssef ouvre des
pull requests mais personne n'a fixé de délai de revue, alors elles s'accumulent. Le jour où
il faut publier en urgence un correctif sur un bug de calcul de tantièmes, personne ne sait
ce qui, dans l'état actuel de la branche principale, est prêt pour la production et ce qui
est un chantier à moitié fini. La release qui aurait dû prendre dix minutes en prend trois
heures, à trier commit par commit ce qui peut partir et ce qui doit être exclu.

## Ce qui se passe vraiment

Git n'impose aucun flux de travail. Il offre des primitives : branches, commits, merges,
tags : et le flux qu'une équipe choisit détermine si le travail parallèle de plusieurs
personnes s'additionne proprement ou se percute. Le choix du flux n'est pas une question
d'habitude ou de préférence esthétique : c'est un compromis entre la vitesse de release
souhaitée, la taille de l'équipe, et le niveau de risque tolérable sur la branche partagée.

### Trunk-based development

Tout le monde travaille à partir d'une branche unique et toujours déployable, avec des
branches de travail très courtes (quelques heures à un jour ou deux), fusionnées le plus
vite possible.

```text
main ──●──●──●──●──●──●──●──●──●──►  toujours déployable
        \    /  \  /  \    /
         branche  branche branche
         courte   courte  courte
         (< 1-2j) (< 1-2j) (< 1-2j)
```

Ce flux impose une discipline forte : une fonctionnalité incomplète ne doit jamais bloquer
la branche principale. Ça n'est possible que combiné à des feature flags (voir plus bas),
qui permettent de fusionner du code inachevé sans l'activer pour les utilisateurs.

- **Coût** : demande une discipline de découpage très fine (petites tranches livrables) et
  des feature flags pour ne pas exposer du travail incomplet.
- **Bénéfice** : la branche principale reste toujours dans un état publiable, les conflits
  de fusion sont petits parce que les branches vivent peu de temps.
- **Quand choisir** : équipe qui publie souvent (plusieurs fois par semaine ou par jour),
  équipe habituée à découper son travail en petites tranches vérifiables.

### GitFlow

Une branche `main` qui ne reçoit que du code déjà publié, une branche `develop` qui
accumule le travail en cours, des branches de fonctionnalité qui partent de `develop`, des
branches de release qui stabilisent avant publication, des branches de correctif urgent
(`hotfix`) qui partent directement de `main`.

```text
main     ──●────────────●──────────────●──►   (uniquement du code publié)
            \            \  hotfix     /
             \            ●───────────●
develop  ──●──●──●──●──●──●──●──●──●──●──►
            \     /  \      /
             feature  feature
             (peut durer plusieurs jours)
```

- **Coût** : plus de branches à gérer, plus de cérémonie à chaque étape, latence plus
  grande entre "le code est écrit" et "le code est en production".
- **Bénéfice** : sépare clairement ce qui est stable, ce qui est en préparation, et ce qui
  est un correctif urgent : utile quand une release nécessite une validation formelle
  (réglementaire, contractuelle) avant publication.
- **Quand choisir** : releases planifiées et espacées (mensuelles, trimestrielles), contexte
  où publier une régression coûte très cher (systèmes réglementés, contrats de service
  stricts), équipe assez grande pour absorber la cérémonie sans ralentir le rythme utile.

### Feature flags

Un feature flag est une condition dans le code qui active ou désactive une fonctionnalité
sans nécessiter de nouveau déploiement. Il découple deux décisions qu'on confond souvent :
"le code est en production" et "la fonctionnalité est visible pour les utilisateurs".

```typescript
// Sans feature flag : le code fusionné = la fonctionnalité active pour tout le monde
function calculerFacture(immeuble: Immeuble): Facture {
  return calculerAvecNouvelleRepartition(immeuble); // risqué si pas encore validé
}

// Avec feature flag : le code peut être fusionné sans être activé
function calculerFacture(immeuble: Immeuble, flags: FeatureFlags): Facture {
  if (flags.nouvelleRepartitionTantiemes) {
    return calculerAvecNouvelleRepartition(immeuble);
  }
  return calculerAvecAncienneRepartition(immeuble);
}
```

Le feature flag rend possible le trunk-based development pour des fonctionnalités qui
prennent plusieurs semaines à construire : le code se fusionne par petites tranches sur la
branche principale, désactivé, testable en production sur un sous-ensemble d'utilisateurs
(par exemple un seul immeuble pilote), puis activé progressivement une fois validé.

```text
Fonctionnalité "nouvelle répartition des tantièmes", développée sur 3 semaines

Semaine 1 : code fusionné sur main, flag désactivé partout
Semaine 2 : code complété, flag activé sur l'immeuble pilote seulement
Semaine 3 : validation sur le pilote, flag activé progressivement sur les 40 immeubles
```

- **Coût** : complexité de code supplémentaire (deux chemins à maintenir tant que le flag
  existe), risque d'oublier de supprimer un flag obsolète et d'accumuler du code mort.
- **Bénéfice** : découple le rythme de développement du rythme d'exposition aux
  utilisateurs, permet de tester en production sur un périmètre restreint avant
  généralisation, permet de désactiver instantanément une fonctionnalité qui pose problème
  sans redéployer.
- **Quand choisir** : fonctionnalité risquée ou longue à construire dans un flux
  trunk-based ; jamais pour du code qui n'a aucune raison d'être désactivé un jour : un flag
  qui ne sera jamais retiré est de la dette, pas un outil.

## Pull requests : la revue comme angle mort couvert

Une pull request n'est pas une formalité administrative avant de fusionner du code. C'est le
mécanisme qui introduit un regard extérieur avant que le code rejoigne le travail commun —
le même mécanisme qui manquait à Karim dans la leçon 01 du niveau Quality Shield. Une pull
request efficace a des propriétés précises :

```text
Pull request faible                       Pull request efficace

Titre : "fix"                             Titre : "corrige le double comptage
                                           des tantièmes sur immeubles avec
                                           parkings détachés"

Description : vide ou "voir le code"      Description : quoi, pourquoi, comment
                                           vérifié, ce qui reste hors périmètre

400 lignes changées, 12 fichiers          Une seule responsabilité claire,
touchés, 3 sujets mélangés                relisible en moins de 20 minutes

Relecteur : "LGTM" sans commentaire       Relecteur : au moins une question ou
                                           un point vérifié activement
```

Une pull request trop grosse n'est pas relue, elle est approuvée par lassitude : ce qui
annule tout le bénéfice de la revue. La règle pratique : si une pull request prend plus de
20-30 minutes à relire sérieusement, elle doit être découpée.

## Releases : ce qui part en production, et quand

Deux modèles de release coexistent avec les deux flux ci-dessus :

- **Déploiement continu** : chaque fusion sur la branche principale (trunk-based) part en
  production automatiquement, dès que les tests automatiques passent. Le risque par
  déploiement est faible parce que chaque changement est petit ; la fréquence de
  déploiement absorbe ce risque en le répartissant.
- **Release planifiée** : le code s'accumule sur une branche de préparation (GitFlow), une
  série de vérifications manuelles ou formelles précède la publication, la fréquence est
  plus basse mais chaque release engage davantage de changements à la fois.

```text
Déploiement continu                       Release planifiée

petit changement ──► prod                 accumulation de changements
   (minutes)                                       │
petit changement ──► prod                    vérification formelle
   (minutes)                                       │
petit changement ──► prod                     publication groupée
   (minutes)                                    (semaines/mois)

Risque par déploiement : faible           Risque par déploiement : plus élevé,
Fréquence : élevée                        mais plus maîtrisé si le contexte
                                           l'exige (réglementaire, contractuel)
```

## Compromis

| Option                                            | Coût                                                       | Bénéfice                                                         | Quand choisir                                                |
| ------------------------------------------------- | ---------------------------------------------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------ |
| Trunk-based + feature flags                       | Discipline de découpage fine, complexité de code des flags | Releases fréquentes, branche toujours publiable, feedback rapide | Équipe qui publie souvent, produit qui évolue vite           |
| GitFlow                                           | Plus de cérémonie, latence plus grande avant publication   | Séparation claire stable / en cours / correctif urgent           | Releases espacées, contexte réglementé ou contractuel strict |
| Pull requests systématiques, petites              | Ralentit légèrement chaque fusion individuelle             | Angle mort couvert par un regard extérieur avant intégration     | Toujours, sauf travail strictement solo                      |
| Pas de revue, push direct sur la branche partagée | Rapidité immédiate                                         | Aucun, sauf projet solo ou prototype jetable                     | Jamais en équipe sur un système qui reste en production      |

## Pièges classiques

- Choisir un flux Git par habitude personnelle sans le confronter au rythme de release réel
  de l'équipe : le symptôme est un flux GitFlow appliqué à une équipe qui voudrait publier
  chaque jour, ou l'inverse.
- Garder une branche de fonctionnalité ouverte plusieurs semaines : le symptôme est un
  conflit de fusion massif et un travail dupliqué découvert trop tard.
- Utiliser un feature flag comme excuse pour fusionner du code non testé : le symptôme est
  un flag qui, une fois activé, révèle des bugs qu'une vraie revue aurait trouvés.
- Laisser des feature flags obsolètes s'accumuler dans le code : le symptôme est une base
  de code truffée de conditions mortes que plus personne n'ose supprimer par peur de casser
  quelque chose.
- Ouvrir des pull requests énormes qui mélangent plusieurs sujets : le symptôme est une
  revue superficielle ou une approbation par lassitude plutôt que par vérification réelle.

## Ce que tu dois savoir défendre

1. Explique pourquoi le trunk-based development n'est viable qu'accompagné de feature flags
   dès que les fonctionnalités prennent plus d'un jour à construire.
2. Donne un critère concret pour décider si une pull request doit être découpée en
   plusieurs, avant même de regarder son contenu technique.
3. Pourquoi GitFlow, malgré sa cérémonie plus lourde, reste le bon choix dans certains
   contextes réglementés ou contractuels, plutôt qu'un flux plus rapide.
