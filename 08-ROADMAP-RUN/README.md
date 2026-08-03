# Niveau 08 : Roadmap Run

[Sommaire](../CURRICULUM.md) | [Niveau precedent](../07-API-DOJO/README.md) | [Niveau suivant](../09-QUALITY-SHIELD/README.md)

**Ce niveau reutilise :** [03-MVP-SPLIT](../03-MVP-SPLIT/04-estimating-honestly.md) : l'estimation honnete et l'effet tunnel, reutilises pour batir un planning risk-first.

**Auto-test d'entree :**
1. Qu'est-ce qui distingue un changement d'API retrocompatible d'un changement qui casse les clients existants ?
2. A quoi sert une cle d'idempotence, et que se passe-t-il sans elle sur un retry ?
3. Quelle est la difference entre authentification et autorisation ?

**Temps de lecture :** environ 100 minutes (leçons + README, hors challenge et boss-fight).

## Ce que c'est

Tu sais découper un problème (Niveau 02), construire un MVP (Niveau 03), poser une
architecture (Niveau 06) et exposer une API (Niveau 07). Ce niveau répond à une question
différente : comment on transforme tout ça en un plan qui tient la route sur des semaines,
avec une équipe, un budget, et des inconnues qui ne se révèlent qu'en avançant. Une roadmap
n'est pas une liste de tâches dans l'ordre où elles te viennent à l'esprit : c'est un pari
structuré sur ce qui va casser en premier.

Durée estimée : 5 à 7 heures de lecture active, plus le temps du challenge (une roadmap
réelle sur un projet de taille moyenne, environ 3 à 5 heures de travail).

Prérequis : Niveaux 00 à 03 (découpage de problème, MVP). Niveau 06 aide mais n'est pas
bloquant.

## Ce que tu sais faire à la sortie

- Tu sais découper un projet en phases qui livrent de la valeur ou de la certitude à chaque
  étape, jamais les deux mains vides.
- Tu sais identifier le risque le plus cher d'un projet et le placer en premier dans le plan,
  au lieu de le repousser parce qu'il fait peur.
- Tu sais distinguer un jalon réel (vérifiable, daté, binaire) d'un jalon décoratif ("phase 2
  terminée à 80 %").
- Tu sais repérer les signaux de dérive avant que le retard soit officiel : et tu sais quoi
  faire de ces signaux.
- Tu sais répondre en réunion à "où on en est vraiment ?" sans mentir ni paniquer.

## Structure du niveau

- [01-why-this-level.md](01-why-this-level.md) : pourquoi les plannings optimistes tuent les projets
- [02-phases-and-milestones.md](02-phases-and-milestones.md) : découper en phases livrables, jalons vérifiables
- [03-risk-first-planning.md](03-risk-first-planning.md) : attaquer le risque le plus cher en premier
- [04-tracking-reality.md](04-tracking-reality.md) : avancement réel vs déclaré, signaux de dérive
- [challenge.md](challenge.md) : construire une roadmap risk-first sur un projet réel
- [boss-fight.md](boss-fight.md) : un sponsor qui veut une date avant que tu aies un plan
- [grimoire.md](grimoire.md) : mémo dense

## Comment lire ce niveau

Dans l'ordre. `03-risk-first-planning.md` est le cœur du niveau : tout le reste organise
la manière dont tu appliques ce principe dans le temps et dont tu le communiques.

## Ce qui ne se passe pas ici

Ce niveau ne t'apprend pas un outil (Jira, Linear, Gantt). Les outils ne sauvent jamais un
mauvais plan. Ce niveau t'apprend à construire le plan que l'outil va ensuite afficher.
