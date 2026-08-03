# STYLE.md : règles d'écriture du curriculum "ProjectFunny"

## Objectif

Former de vrais cerveaux d'ingénieurs : comprendre ce qu'on fait, pourquoi, ce qui casse,
et comment décider quand le projet devient réel. Pas de cours scolaire, pas de liste de notions.

## Ton

- Direct, vivant, un peu jeu vidéo (niveaux, quêtes, boss fight, grimoire), mais jamais puéril.
- Français. Phrases courtes. Zéro remplissage, zéro "il est important de noter que".
- On parle au lecteur : "tu".
- Refuser le contenu tiède : chaque page doit apprendre quelque chose de non évident.

## Structure d'une leçon (fichier `NN-slug.md`)

1. `# Titre` court et parlant.
2. **Le piège** ou **La scène** : 3-6 lignes de situation concrète et crédible (jamais un exemple
   cliché type "todo app" ou "blog" ; préférer : gestion de créneaux d'un cabinet vétérinaire,
   suivi de tournées de livraison, bibliothèque d'un club d'escalade, refacturation d'énergie...).
3. **Ce qui se passe vraiment** : le modèle mental, expliqué en profondeur.
4. Au moins un bloc ASCII (diagramme, flux, arbre de décision) dans un bloc ```text.
5. Du code réel et minimal quand ça éclaire (TypeScript / SQL / pseudo-code), commenté.
6. **Compromis** : tableau ou liste "Option A / Option B → coût, bénéfice, quand choisir".
7. **Pièges classiques** : 3-5 puces, chacune avec le symptôme observable.
8. **Ce que tu dois savoir défendre** : 3 questions auxquelles le lecteur doit pouvoir répondre à l'oral.

## Structure d'un niveau (dossier)

- `README.md` : carte du niveau, durée estimée, prérequis, ce que tu sais faire à la sortie.
- `01-why-this-level.md` : pourquoi ce niveau existe, ce qui casse sans lui.
- leçons numérotées.
- `challenge.md` : exercice appliqué, livrable, critères de réussite mesurables.
- `boss-fight.md` : situation adverse réaliste (contrainte, deadline, désaccord, incident) + grille d'évaluation.
- `grimoire.md` : mémo dense (checklists, formules, heuristiques, vocabulaire).

## Interdits

- Emojis.
- Listes de définitions sans conséquence pratique.
- "Bonnes pratiques" sans coût associé.
- Exemples génériques (todo, blog, e-commerce basique).
- Promesses vagues ("cela améliore la qualité") sans mécanisme expliqué.

## Longueur

Chaque leçon : 150 à 350 lignes de markdown dense. README de niveau : 60-120 lignes.
