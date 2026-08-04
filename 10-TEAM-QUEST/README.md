# Niveau 10 : Team Quest

[Sommaire](../CURRICULUM.md) | [Niveau precedent](../09-QUALITY-SHIELD/README.md) | [Niveau suivant](../11-BIG-APP-SNOOP/README.md)

**Ce niveau reutilise :** [08-ROADMAP-RUN](../08-ROADMAP-RUN/04-tracking-reality.md) : les signaux de derive, reutilises pour reperer un desaccord d'equipe avant qu'il bloque un jalon.

**Auto-test d'entree :**
1. Sur quel critere choisis-tu quoi tester : une regle de couverture ou le cout reel d'une panne ?
2. Qu'est-ce qu'un postmortem sans blame, et pourquoi cette contrainte existe-t-elle ?
3. Que doit permettre une bonne observabilite sans avoir a se connecter en SSH ?

**Temps de lecture :** environ 140 minutes (leçons + README, hors challenge et boss-fight).

## Ce que c'est

Tout ce que tu as appris jusqu'ici marche à une personne. Ce niveau ajoute la variable qui
change tout : d'autres humains, avec leur propre contexte, leurs propres désaccords, et un
Git history commun où tes erreurs deviennent visibles pour tout le monde. Une équipe qui
code bien individuellement mais qui n'a pas d'accords de travail clairs perd plus de temps
en friction qu'en bugs. Ce niveau construit les règles implicites qui doivent devenir
explicites, le flux Git qui absorbe le travail parallèle sans collision, et la communication
qui tient sous pression sans dégénérer en silence ou en conflit ouvert.

Durée estimée : 5 à 6 heures de lecture active, plus le challenge (rédiger et faire valider
un working agreement pour une équipe réelle ou simulée, 2 à 3 heures).

Prérequis : Niveau 08 (Roadmap Run) : les accords d'équipe n'ont de sens que rapportés à un
plan et des jalons partagés.

## Ce que tu sais faire à la sortie

- Tu sais rédiger un working agreement qui règle les questions avant qu'elles deviennent des
  conflits (définition de "fini", horaires de revue, qui décide quoi).
- Tu sais choisir un flux Git (trunk-based, GitFlow, feature flags) selon la taille de
  l'équipe et le rythme de release, pas par habitude.
- Tu sais désamorcer un désaccord technique sans écraser l'autre ni t'écraser toi-même.
- Tu sais dire non à une demande intenable sans bloquer la relation avec la personne qui
  demande.
- Tu sais escalader un problème au bon niveau, au bon moment, sans attendre que ça explose.

## Structure du niveau

- [01-why-this-level.md](01-why-this-level.md) : pourquoi de bons devs produisent une mauvaise équipe
- [02-working-agreements.md](02-working-agreements.md) : les règles qu'on écrit avant d'en avoir besoin
- [03-git-and-flow.md](03-git-and-flow.md) : branches, PR, releases, feature flags
- [04-communication-under-pressure.md](04-communication-under-pressure.md) : désaccords, escalade, dire non
- [challenge.md](challenge.md) : rédiger et faire vivre un working agreement
- [boss-fight.md](boss-fight.md) : un conflit de merge humain, pas technique
- [grimoire.md](grimoire.md) : mémo dense

## Comment lire ce niveau

Dans l'ordre. `02` pose le cadre, `03` l'outille techniquement, `04` te donne les réflexes
pour quand le cadre ne suffit plus : parce qu'il ne suffit jamais complètement.

## Ce qui ne se passe pas ici

Ce niveau n'enseigne pas le management au sens hiérarchique. Il s'adresse à des pairs qui
doivent collaborer, avec ou sans manager dans la pièce.

Avant de continuer : passe par [RETRO-BLOC-3-CONDUITE.md](../RETRO-BLOC-3-CONDUITE.md), la
rétrospective du bloc Conduite que tu viens de terminer.
