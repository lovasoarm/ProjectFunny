# Niveau 09 : Quality Shield

[Sommaire](../CURRICULUM.md) | [Niveau precedent](../08-ROADMAP-RUN/README.md) | [Niveau suivant](../10-TEAM-QUEST/README.md)

**Ce niveau reutilise :** [07-API-DOJO](../07-API-DOJO/03-errors-and-idempotence.md) : les erreurs exploitables et l'idempotence, reutilisees pour decider quoi tester et quoi observer.

**Auto-test d'entree :**
1. Qu'est-ce qu'un jalon reel, par opposition a un jalon decoratif ?
2. Pourquoi faut-il attaquer le risque le plus cher d'un projet en premier plutot qu'en dernier ?
3. Donne un exemple de signal de derive qu'on peut reperer avant que le retard soit officiel.

**Temps de lecture :** environ 155 minutes (leçons + README, hors challenge et boss-fight).

## Ce que c'est

Ton code marche. Il tourne en démo, les tests que tu as écrits passent, tout le monde est
content. Trois mois plus tard, il tourne en production avec de vrais utilisateurs, de vrais
pics de charge, et un stagiaire qui pousse une modification à 17h58 un vendredi. Ce niveau
construit le bouclier qui te protège de toi-même et de tes coéquipiers : les tests qui
rapportent vraiment, l'observabilité qui te dit ce qui se passe sans que tu aies à deviner,
la revue de code et la CI comme garde-fous automatiques, et la gestion d'incident qui répare
sans détruire la confiance de l'équipe.

Durée estimée : 6 à 8 heures de lecture active, plus le challenge (mise en place d'un
pipeline de qualité sur un projet existant, 4 à 6 heures).

Prérequis : Niveau 07 (API Dojo) : tu dois avoir un système qui répond à des requêtes pour
que la notion de test, d'observabilité et d'incident ait un sens concret.

## Ce que tu sais faire à la sortie

- Tu sais choisir quoi tester selon le coût réel d'une panne, pas selon une règle de
  couverture arbitraire.
- Tu sais construire une observabilité qui répond à "qu'est-ce qui se passe là, maintenant"
  sans devoir te connecter en SSH et lire des logs en direct.
- Tu sais organiser une revue de code qui attrape les vrais problèmes au lieu de débattre de
  style, et une CI qui bloque ce qui doit l'être sans ralentir tout le monde.
- Tu sais mener un incident sans paniquer et écrire un postmortem qui change réellement le
  système, pas un rapport qui accuse quelqu'un.

## Structure du niveau

- [01-why-this-level.md](01-why-this-level.md) : pourquoi "ça marche chez moi" ne suffit jamais
- [02-tests-that-pay.md](02-tests-that-pay.md) : pyramide vs trophée, quoi tester selon le coût de panne
- [03-observability.md](03-observability.md) : logs structurés, métriques, traces, alertes utiles
- [04-review-and-ci.md](04-review-and-ci.md) : revue de code utile, CI, garde-fous automatiques
- [05-incidents-and-postmortem.md](05-incidents-and-postmortem.md) : gestion d'incident, postmortem sans blâme
- [challenge.md](challenge.md) : construire le bouclier qualité d'un service réel
- [boss-fight.md](boss-fight.md) : un incident en production, un dimanche, sans ton lead
- [grimoire.md](grimoire.md) : mémo dense

## Comment lire ce niveau

Dans l'ordre. `02` et `03` sont la fondation (savoir ce qui casse, savoir le voir).
`04` empêche que ça casse une deuxième fois. `05` répare quand malgré tout ça casse.

## Ce qui ne se passe pas ici

Ce niveau ne prône pas "100 % de couverture de tests" ni "logguer chaque ligne". Les deux
sont des coûts. Ce niveau t'apprend à dépenser cet effort là où il rapporte.
