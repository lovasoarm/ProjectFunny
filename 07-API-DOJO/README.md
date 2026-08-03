# Niveau 07 — API Dojo

## Ce que c'est

Tu sais écrire un endpoint qui répond `200 OK` avec le bon JSON. Ce niveau s'attaque à
autre chose : ce qui se passe quand ton API n'est plus seule dans la pièce. Dès qu'un
deuxième système appelle le tien — une appli mobile, un partenaire, un cron de nuit qui
retente trois fois — ton code n'est plus un script qui répond à une requête, c'est un
contrat public que des inconnus vont violer, mal lire, appeler en double, et attaquer.
Une API n'est pas une fonction exposée sur le réseau. C'est une promesse écrite, versionnée,
et défendue dans le temps. Ce niveau te donne les réflexes pour tenir cette promesse sans
que ton système ne s'effondre au premier appelant capricieux.

Durée estimée : 8 à 12 heures, réparties sur plusieurs jours.

Prérequis : avoir livré au moins une API HTTP qui fonctionne (Niveau 03 ou équivalent), et
avoir complété le Niveau 06 — Archi-Lab. Tu dois savoir ce qu'est une couche domaine avant
d'apprendre à protéger sa frontière publique ; sans ça, "contrat d'API" restera un mot vide.

## Ce que tu sais faire à la sortie

- Tu sais écrire un contrat d'API avant le code, et distinguer un changement rétrocompatible
  d'un changement qui casse tous tes clients existants.
- Tu sais concevoir des erreurs exploitables par du code, pas juste lisibles par un humain,
  et implémenter des retries et des clés d'idempotence sans dupliquer une opération.
- Tu sais expliquer la différence entre authentification et autorisation, et dessiner la
  frontière de confiance d'un système qui accepte des appels de plusieurs origines.
- Tu sais mettre en place pagination, rate limiting et cache HTTP sans réinventer une roue
  bancale, et chiffrer l'impact réel de chacun sur la latence perçue.
- Tu sais te défendre à l'oral sur chacun de ces choix, avec un exemple concret, pas un mot
  de vocabulaire récité.

## Fil rouge

Trois systèmes reviennent dans tout le niveau, pour que les leçons s'empilent au lieu de
se contredire :

- **Le cabinet vétérinaire** — une API de prise de rendez-vous et de dossiers patients,
  appelée par une appli mobile grand public et par un logiciel de comptabilité tiers.
- **Les tournées de livraison** — une API qui expose l'état des tournées à des chauffeurs
  via une appli terrain à connexion instable, et à des clients via un portail de suivi.
- **La refacturation d'énergie** — une API B2B exposée à des partenaires qui interrogent des
  volumes de consommation, avec des contraintes réglementaires sur qui a le droit de voir quoi.

## Structure du niveau

```text
07-API-DOJO/
├── 01-why-this-level.md            → ce qui casse sans discipline d'API
├── 02-contracts-first.md           → contrat avant code, versionnage, compatibilité
├── 03-errors-and-idempotence.md    → codes, erreurs exploitables, retries, idempotency keys
├── 04-auth-and-trust.md            → authn vs authz, tokens, scopes, frontières de confiance
├── 05-performance-and-limits.md    → pagination, rate limiting, cache HTTP, latence perçue
├── challenge.md                    → exercice appliqué et livrable noté
├── boss-fight.md                   → situation adverse réaliste + grille d'évaluation
└── grimoire.md                     → mémo dense
```

## Comment lire ce niveau

Dans l'ordre. `02` pose le socle : tant que tu ne sais pas ce qu'est un contrat stable, les
leçons suivantes n'ont pas de fondation. `03` et `04` sont les deux niveaux de défense d'une
API exposée — l'un contre les pannes et la concurrence, l'autre contre les acteurs malveillants
ou simplement mal informés. `05` referme la boucle avec les questions qui apparaissent une
fois que l'API a du succès et du trafic réel, ce qui est le meilleur problème à avoir et
aussi celui que le plus de gens ratent.

## Ce qui ne se passe pas ici

Ce niveau ne t'apprend pas un framework précis (REST vs GraphQL vs gRPC au sens outillage,
Swagger vs autre générateur). Il t'apprend les forces qui s'appliquent quel que soit le
protocole choisi : un contrat instable casse des clients en REST comme en gRPC, une clé
d'idempotence manquante double une facture peu importe le framework. Le vocabulaire d'outil
changera au fil de ta carrière ; les problèmes de ce niveau ne changeront pas.
