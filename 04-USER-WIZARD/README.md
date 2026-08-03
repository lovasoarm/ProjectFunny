# Niveau 04 : User Wizard

[Sommaire](../CURRICULUM.md) | [Niveau precedent](../03-MVP-SPLIT/README.md) | [Niveau suivant](../05-DATA-SPELLS/README.md)

**Ce niveau reutilise :** [03-MVP-SPLIT](../03-MVP-SPLIT/02-slicing-value.md) : la tranche verticale, reutilisee ici pour livrer un parcours utilisateur complet plutot qu'un ecran isole.

**Auto-test d'entree :**
1. Qu'est-ce qu'une tranche verticale, par opposition a une couche horizontale ?
2. Pourquoi une estimation qui cache son incertitude derriere un chiffre unique est-elle malhonnete ?
3. Donne un exemple concret ou dire non a un ajout de perimetre protege la livraison.

**Temps de lecture :** environ 165 minutes (leçons + README, hors challenge et boss-fight).

## Ce niveau en une phrase

Construire des interfaces qui disent la vérité à l'utilisateur : ce qui se passe, ce qui a marché,
ce qui a raté, et ce qu'il peut faire maintenant.

## Pourquoi ce nom

"User Wizard" pas au sens assistant-en-5-étapes, au sens sorcier : celui qui manipule l'état d'un
écran sans jamais perdre le fil de ce que voit vraiment la personne en face. Un formulaire, une liste,
un bouton "enregistrer" : chacun cache dix états que le prototype ignore et que la prod te fait payer.

## Ce que tu sais déjà en arrivant ici

- Tu sais écrire un composant qui affiche des données.
- Tu as déjà branché un appel réseau et affiché le résultat "quand ça marche".
- Tu n'as jamais eu à gérer sérieusement : double-clic, perte de connexion, réponse partielle,
  liste de 40 000 lignes, ou un serveur qui répond deux fois à la même requête.

## Ce que tu sauras faire à la sortie

- Dessiner un parcours utilisateur avant l'écran, avec tous ses embranchements d'erreur.
- Lister et traiter les cinq états obligatoires de tout affichage de données : vide, chargement,
  erreur, partiel, trop plein.
- Écrire un formulaire dont la validation client et la validation serveur ne se contredisent jamais,
  et dont le double submit ne peut pas créer deux fois la même ressource.
- Choisir entre UI optimiste et UI pessimiste en connaissant le prix de chaque choix.
- Défendre des décisions d'accessibilité et de perception de vitesse avec des arguments mesurables,
  pas des impressions.

## Terrain de jeu

Toutes les leçons de ce niveau utilisent le même produit fil rouge : un logiciel de gestion de
créneaux pour un cabinet vétérinaire (prise de rendez-vous, dossiers animaux, plannings des
praticiens). Un domaine avec de la concurrence réelle (deux personnes qui réservent le même créneau),
des données sensibles (dossier médical), et des utilisateurs pressés (la salariée à l'accueil avec
trois personnes qui attendent).

## Plan du niveau

- [01-why-this-level.md](01-why-this-level.md) : ce qui casse en prod quand personne n'a pensé aux états et aux flux.
- [02-flows-before-screens.md](02-flows-before-screens.md) : dessiner le parcours et ses erreurs avant de dessiner l'écran.
- [03-states-and-empty-cases.md](03-states-and-empty-cases.md) : les cinq états obligatoires de tout affichage.
- [04-forms-that-dont-lie.md](04-forms-that-dont-lie.md) : validation double, messages utiles, idempotence.
- [05-accessibility-and-speed.md](05-accessibility-and-speed.md) : a11y utile, perception de latence, UI optimiste et ses pièges.
- [challenge.md](challenge.md) : exercice appliqué et critères de réussite mesurables.
- [boss-fight.md](boss-fight.md) : situation adverse réaliste, avec grille d'évaluation.
- [grimoire.md](grimoire.md) : mémo dense à garder sous la main.

## Durée estimée

Une semaine de travail effectif si tu codes le fil rouge en parallèle des leçons. Compte deux jours
de plus si c'est ton premier vrai formulaire avec gestion d'erreurs serveur.

## Prérequis

Niveaux 00 à 03 (ou équivalent) : bases HTTP, état de composant, notion de requête asynchrone.
Tu dois savoir ce qu'est une race condition, même vaguement, avant d'attaquer la leçon 2.

## Comment progresser

Lis une leçon, code la partie correspondante du cabinet vétérinaire, casse-la volontairement
(coupe le réseau, double-clique, envoie deux requêtes en parallèle), observe ce qui se passe
vraiment, corrige. Le challenge et le boss fight vérifient que tu as fait ce travail, pas que
tu as lu les mots.
