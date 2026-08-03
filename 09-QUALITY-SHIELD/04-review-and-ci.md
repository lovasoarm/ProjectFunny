# Revue de code utile, CI, garde-fous automatiques

## Le piège

Deux développeurs de l'équipe tournées de livraison ont une revue de code qui dure quarante
minutes sur une pull request de dix lignes : ils débattent de l'emplacement d'une accolade,
du nom d'une variable (`tour` contre `deliveryRun`), et de l'usage de `const` contre `let`.
La pull request est approuvée. Elle contient un bug qui envoie la notification de livraison
au mauvais client si deux tournées démarrent à la même minute : personne ne l'a vu, parce
que personne n'a lu la logique métier, occupés qu'ils étaient par le style. Trois semaines
plus tard, la même équipe passe une CI de 25 minutes à chaque pull request, avec des tests
qui échouent de façon aléatoire une fois sur cinq sans rapport avec le changement testé.
L'équipe a pris l'habitude de relancer la CI "jusqu'à ce qu'elle passe" sans regarder
pourquoi elle échoue. Les deux symptômes ont la même cause : des garde-fous qui existent
mais qui ne protègent plus rien, parce qu'ils ont dérivé loin de ce qu'ils étaient censés
attraper.

## Ce qui se passe vraiment

### La revue de code a un objectif précis, qui n'est pas le style

Une revue de code sert à faire relire une décision par quelqu'un qui n'a pas les mêmes
angles morts que l'auteur : pas à faire respecter des préférences esthétiques
interchangeables. Le style de code (indentation, nommage, organisation des imports) doit
être réglé par un outil automatique (linter, formateur), jamais débattu à l'oral en revue :
c'est un gaspillage de l'attention humaine sur un problème que la machine résout mieux et
plus vite.

```text
Ce qu'une machine règle mieux qu'un humain (linter, formateur automatique) :
  - indentation, espaces, points-virgules
  - ordre des imports
  - conventions de nommage mécaniques (camelCase vs snake_case)

Ce qu'un humain doit vérifier en revue (aucune machine ne le fait) :
  - la logique métier fait-elle ce qu'elle prétend faire, dans les cas limites ?
  - cette décision d'architecture va-t-elle coûter cher dans six mois ?
  - ce changement a-t-il un effet de bord sur une partie du système que l'auteur
    ne connaît pas bien ?
  - un attaquant ou un utilisateur malveillant peut-il détourner ce comportement ?
```

Une revue de code efficace pose des questions, elle n'impose pas des goûts :

```text
Mauvais commentaire de revue :
  "Je préfère qu'on utilise des fonctions fléchées ici."
  → préférence personnelle, sans conséquence démontrée, coûte du temps de débat.

Bon commentaire de revue :
  "Si deux tournées démarrent à la même minute exacte, cette clé de notification
   `tourStartMinute` sera identique pour les deux : est-ce voulu ? Je pense que ça
   enverrait la notif de la tournée B au chauffeur de la tournée A."
  → pointe un risque métier concret, vérifiable, avec un scénario précis.
```

### La CI est un garde-fou automatique, pas une formalité à faire passer

La CI (intégration continue) exécute automatiquement, à chaque changement, ce qu'un humain
ne devrait pas avoir à vérifier à la main : les tests, le linter, le typage, parfois un scan
de sécurité. Sa valeur dépend entièrement de sa fiabilité : une CI qui échoue au hasard sans
rapport avec le changement (test flaky) apprend à l'équipe à l'ignorer, ce qui annule
totalement son utilité : un garde-fou qu'on enjambe systématiquement n'en est plus un.

```text
Pipeline CI d'une pull request

  push ──► lint + typage (30s)   ──► échec bloquant, rapide à corriger
              │
              ▼
          tests unitaires (2min) ──► échec bloquant, cible la fonction en cause
              │
              ▼
          tests d'intégration (5min) ──► échec bloquant, cible la frontière en cause
              │
              ▼
          build + scan sécurité (3min) ──► échec bloquant, vulnérabilité connue
              │
              ▼
          merge autorisé si tout est vert
```

L'ordre compte : les vérifications les plus rapides et les moins chères d'abord (lint,
typage), pour donner un retour en quelques secondes sur les erreurs les plus fréquentes,
avant de lancer les étapes longues.

### Le test flaky : le poison silencieux de la confiance en CI

Un test flaky échoue ou réussit de façon incohérente sans changement de code, typiquement à
cause d'une dépendance au temps réel, à l'ordre d'exécution, ou à un état partagé mal
nettoyé entre tests.

```typescript
// Flaky : dépend de l'horloge réelle, échoue parfois si le test tourne
// juste avant minuit ou si la machine de CI est lente ce jour-là.
test("la tournée du jour est bien filtrée", () => {
  const tours = getTodaysTours(allTours, new Date());
  expect(tours.length).toBe(3);
});

// Fiable : la date est injectée, le test est déterministe quelle que soit
// l'heure réelle d'exécution.
test("la tournée du jour est bien filtrée", () => {
  const fixedNow = new Date("2024-03-15T10:00:00Z");
  const tours = getTodaysTours(allTours, fixedNow);
  expect(tours.length).toBe(3);
});
```

Règle non négociable : un test flaky détecté doit être corrigé ou désactivé explicitement
avec un ticket de suivi, jamais laissé tel quel "parce qu'il finit toujours par passer au
deuxième essai" : chaque relance ignorée érode un peu plus la confiance de l'équipe dans
tout le système de CI, y compris les échecs qui comptent vraiment.

### Garde-fous automatiques au-delà des tests

Une CI solide bloque aussi ce qu'une revue humaine oublie facilement de vérifier
systématiquement :

```text
- Scan de dépendances : bibliothèque tierce avec une vulnérabilité connue publiée.
- Vérification de schéma de migration de base de données avant qu'elle ne casse en prod.
- Contrôle de taille de bundle (une dépendance ajoutée fait doubler le poids du JS envoyé).
- Vérification que les secrets (clés API) ne sont jamais commités en clair dans le code.
```

Analogie : Analogie : la revue de code et la CI, c'est un second menuisier qui vérifie l'assemblage
avant qu'il ne parte en livraison, et la vérification du matériel par un coéquipier avant de
s'encorder en course en montagne.
Où l'analogie casse : le second menuisier ou le coéquipier vérifient un objet physique fini
sous leurs yeux. Un relecteur de code ou une CI ne voient qu'un diff texte et des tests
automatisés, ils peuvent laisser passer un comportement que rien ne rend visible avant
l'exécution en production.

## Compromis

| Option                                              | Coût                                            | Bénéfice                                                    | Quand choisir                                                                 |
| --------------------------------------------------- | ----------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------- |
| Revue de code centrée sur le style                  | Débats longs, faible valeur ajoutée             | Illusion de rigueur                                         | Jamais : remplacer par un linter automatique                                  |
| Revue de code centrée sur la logique et les risques | Demande une vraie lecture, plus lente par ligne | Attrape les bugs métier et les angles morts                 | Systématique sur toute pull request qui touche une logique non triviale       |
| CI complète à chaque push (lint, tests, sécurité)   | Temps d'exécution, coût d'infrastructure        | Empêche les régressions d'entrer dans la branche principale | Systématique sur tout projet avec plus d'un contributeur                      |
| Ignorer ou relancer un test flaky sans le corriger  | Rapide sur le moment                            | Érode la confiance dans toute la CI à moyen terme           | Jamais comme pratique répétée                                                 |
| Bloquer le merge tant que la CI n'est pas verte     | Peut ralentir une urgence ponctuelle            | Empêche une régression connue de partir en prod             | Par défaut, avec une procédure explicite et tracée pour les rares dérogations |

## Pièges classiques

- Une revue de code qui débat du style pendant que la logique métier fautive passe sans
  question : le symptôme est un bug qui aurait dû être vu, découvert seulement en
  production.
- Un test flaky ignoré et relancé systématiquement : le symptôme est que l'équipe finit par
  relancer aussi les échecs réels sans les regarder, pensant qu'ils sont "encore" flaky.
- Une CI si lente que l'équipe la contourne (merge direct, ou attente en fin de journée) —
  le symptôme est une accumulation de changements non vérifiés qui cassent ensemble.
- Un garde-fou de sécurité (scan de dépendances, détection de secrets) absent de la CI,
  découvert seulement après une fuite ou un audit externe.
- Une dérogation exceptionnelle pour merger sans CI verte qui devient une habitude non
  tracée : le symptôme est que plus personne ne sait pourquoi la CI existe encore.

## Ce que tu dois savoir défendre

1. Explique pourquoi le style de code doit être réglé par un outil automatique plutôt que
   débattu en revue humaine, et ce que cela libère comme attention pour la revue.
2. Donne un exemple de commentaire de revue utile et un exemple de commentaire inutile, en
   justifiant la différence par leur capacité à prévenir un vrai bug.
3. Explique pourquoi un test flaky ignoré est plus dangereux qu'un test qui échoue toujours
   de façon prévisible.
