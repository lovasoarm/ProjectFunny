# Source de vérité, cache, duplication, cohérence éventuelle

## Le piège

Le club d'escalade veut afficher en temps réel le nombre de places restantes sur le mur, sur
trois écrans : le site web, l'appli mobile, et un panneau d'affichage à l'accueil. Pour que ce
soit rapide, tu stockes un compteur `placesRestantes` dans une table `creneaux`, mis à jour à
chaque réservation ou annulation. Trois mois plus tard, un adhérent annule via l'appli mobile
pendant une coupure réseau de deux secondes sur le serveur qui gère le panneau d'accueil. Le
panneau affiche encore "0 place" alors qu'une place vient de se libérer. Un client au comptoir
se voit refuser une réservation que le système aurait dû accepter. Le vrai problème n'est pas
le bug réseau : c'est que personne n'avait décidé, avant d'écrire le code, qui est responsable
de dire la vérité sur le nombre de places, et ce qui doit se passer quand deux sources ne sont
pas d'accord pendant un instant.

## Ce qui se passe vraiment

### La source de vérité n'est pas un détail technique, c'est une décision métier

Dans tout système avec plus d'un endroit qui connaît une même information, il faut désigner
laquelle des copies fait foi en cas de désaccord. C'est la source de vérité. Tout le reste
(caches, compteurs dénormalisés, projections, copies pour affichage) est dérivé, et doit
pouvoir être reconstruit à partir d'elle.

```text
Source de vérité : la table `reservations` (chaque ligne = une réservation active ou annulée)

Dérivés (peuvent être faux temporairement, jamais faire foi) :
  - compteur `placesRestantes` dans `creneaux`      → dénormalisation pour la lecture rapide
  - cache Redis "places par créneau"                 → optimisation de charge
  - panneau d'affichage à l'accueil                   → copie locale rafraîchie toutes les 5s

Règle : en cas de doute, on recompte depuis `reservations`, jamais depuis un dérivé.
```

Le bug du panneau d'accueil n'est pas un bug de synchronisation réseau : c'est un bug de
conception : le compteur `placesRestantes` a été traité comme une source de vérité alors que
ce n'en était pas une. La correction ne consiste pas à "améliorer la synchronisation", mais à
documenter explicitement que ce compteur est un dérivé, avec un mécanisme de réconciliation
(recalcul périodique depuis la source de vérité) et une tolérance affichée à l'utilisateur
("mis à jour il y a quelques secondes").

### Cache : gagner de la vitesse en acceptant de mentir un peu

Un cache existe pour une seule raison : la source de vérité est trop lente ou trop coûteuse à
consulter à chaque lecture. Le prix de cette vitesse est que le cache peut être faux pendant
une fenêtre de temps. La question qui compte n'est jamais "faut-il un cache", c'est **combien
de temps le système a-t-il le droit de mentir, et qui absorbe le coût de ce mensonge**.

```text
Type de donnée              → Tolérance au mensonge         → Stratégie
────────────────────────────────────────────────────────────────────────
Nombre de vues d'une page   → plusieurs minutes, personne    → cache long, rafraîchi
                                ne le remarque                  en arrière-plan

Places restantes affichées  → quelques secondes, un client    → cache court + recalcul à
                                pourrait sinon réserver une      la confirmation finale
                                place déjà prise

Solde bancaire affiché      → zéro tolérance visible ;         → lecture directe de la
                                un écart doit être invisible     source de vérité, ou cache
                                à l'utilisateur, même bref        invalidé de façon synchrone

Statut de paiement          → zéro tolérance fonctionnelle :   → jamais de cache pour la
                                agir sur une info fausse ici     décision elle-même, seulement
                                a un coût financier direct        pour l'affichage
```

Une erreur fréquente est de choisir la stratégie de cache en fonction de la facilité
technique à la mettre en place, plutôt qu'en fonction du coût réel d'un mensonge pour cette
donnée précise. Le coût n'est pas le même pour "afficher un like en retard" et "afficher un
solde de compte en retard", même si techniquement les deux caches se codent pareil.

### Duplication : la différence entre dupliquer une donnée et dupliquer une vérité

Dupliquer une donnée pour la lecture (dénormalisation) est une technique saine, utilisée
partout, à condition qu'une seule des copies soit désignée comme faisant foi. Le problème
apparaît quand deux copies sont **toutes deux traitées comme faisant foi**, avec des chemins
d'écriture indépendants : c'est là que naissent les divergences silencieuses.

```text
Duplication saine (une source de vérité, un dérivé) :

  reservations (source) ──> écrit ──> creneaux.placesRestantes (dérivé, recalculé)
       ↑ toute écriture de réservation passe forcément par ici


Duplication dangereuse (deux écritures indépendantes, aucune source claire) :

  Formulaire web  ──> écrit directement ──> creneaux.placesRestantes
  Job de nuit     ──> écrit directement ──> creneaux.placesRestantes
                                             (recalculé différemment, à partir
                                              d'une requête légèrement différente)

  → les deux écritures peuvent se contredire, et rien dans le système ne dit laquelle
    a raison
```

La règle pratique : si une donnée peut être écrite depuis plus d'un endroit du code, ce
n'est presque jamais un problème en soi : mais si elle peut être écrite depuis plus d'une
**source de calcul différente**, c'est un problème quasi certain à moyen terme.

### Cohérence éventuelle : accepter le décalage au lieu de le nier

Dans un système distribué (plusieurs services, plusieurs bases, du cache, plusieurs
utilisateurs simultanés), il est souvent impossible et même indésirable de garantir que
"tout le monde voit la même chose au même instant" : le coût en latence et en complexité
serait disproportionné par rapport au problème réel. La cohérence éventuelle est le choix
assumé d'accepter un décalage temporaire, borné et documenté, en échange de performance et de
simplicité, à condition que ce décalage ne puisse jamais provoquer une décision irréversible
incorrecte.

```text
Acceptable en cohérence éventuelle :
  - le compteur "j'aime" met 3 secondes à se propager entre deux onglets ouverts
  - le tableau de bord de stats se rafraîchit toutes les minutes

Inacceptable en cohérence éventuelle sans garde-fou supplémentaire :
  - deux personnes réservent la dernière place au même instant sur deux appareils
    → il FAUT un point de vérification synchrone au moment de la confirmation finale,
      même si l'affichage en amont était en cohérence éventuelle
```

Le garde-fou classique est de séparer clairement "l'affichage" (peut être en cohérence
éventuelle, sert à informer) de "la décision engageante" (doit vérifier la source de vérité
au moment exact de l'action, avec une contrainte d'unicité en base si besoin : par exemple une
contrainte SQL empêchant deux réservations sur la même place).

Analogie : Analogie : la source de vérité et le cache, c'est le dossier patient de référence aux
urgences que chaque service consulte plutôt que de refaire l'anamnèse, et le carnet de route
que le chef de cordée tient à jour pendant une course en montagne.
Où l'analogie casse : un dossier patient ou un carnet de route se corrige d'un trait de stylo
et tout le monde relit le même papier immédiatement. Un cache technique peut continuer de
mentir à des clients différents pendant plusieurs secondes après que la vérité a changé, sans
qu'aucun d'eux ne le sache.

## Compromis

| Option                                                           | Coût                                                                       | Bénéfice                                                                     | Quand choisir                                                        |
| ---------------------------------------------------------------- | -------------------------------------------------------------------------- | ---------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| Une seule source de vérité, tout le reste recalculé à la demande | Lectures plus lentes, plus de charge sur la source                         | Zéro risque de divergence                                                    | Donnée peu lue, ou système à faible charge                           |
| Source de vérité + dérivés en cache avec TTL court               | Complexité de gestion du cache, fenêtre de mensonge à documenter           | Lectures rapides, charge réduite sur la source                               | Donnée lue très souvent, tolérance au décalage clairement acceptable |
| Vérification synchrone au moment de l'action engageante          | Un aller-retour supplémentaire vers la source de vérité au moment critique | Empêche les incohérences qui coûtent cher (double réservation, double débit) | Toute action irréversible ou financière                              |
| Duplication avec deux chemins d'écriture indépendants            | Divergences silencieuses inévitables à moyen terme                         | Aucun bénéfice réel, seulement une fausse impression de simplicité           | Jamais : signal qu'il faut refactorer vers une source unique         |

## Pièges classiques

- **Le compteur dénormalisé sans mécanisme de réconciliation.** Symptôme : le compteur dérive
  petit à petit de la réalité, personne ne s'en aperçoit jusqu'à un écart flagrant, et aucun
  job ne sait le recalculer proprement depuis la source.
- **Le cache invalidé "à la main" au cas par cas.** Symptôme : chaque nouvel endroit du code
  qui modifie la donnée doit se souvenir d'invalider le cache : un développeur l'oublie tôt
  ou tard, et le cache devient menteur en permanence sur certains chemins.
- **La cohérence éventuelle appliquée à une décision engageante.** Symptôme : deux
  utilisateurs réservent la même ressource unique en même temps parce que la vérification de
  disponibilité s'est appuyée sur une valeur en cache plutôt que sur une contrainte au niveau
  de la source de vérité.
- **La confusion entre "affiché" et "vrai".** Symptôme : une équipe débat pendant une heure de
  pourquoi deux écrans affichent des chiffres différents, sans jamais se demander laquelle des
  deux sources fait réellement foi : la question n'a jamais été tranchée à la conception.
- **La duplication qui commence "temporaire" et devient permanente.** Symptôme : un champ
  copié d'une table à une autre "pour aller vite", sans jamais documenter qui doit le
  maintenir à jour : six mois plus tard, plus personne ne sait lequel des deux est correct.

## Ce que tu dois savoir défendre

- Pour une donnée de ton projet actuel, désigne sa source de vérité et énumère tous ses
  dérivés (caches, compteurs, copies d'affichage).
- Explique pourquoi la cohérence éventuelle est acceptable pour un compteur de vues mais pas
  pour la dernière place disponible d'un événement.
- Décris un mécanisme concret de réconciliation entre un dérivé et sa source de vérité.
