# Méthode de debug : bissection, hypothèses, profilers, logs

## Le piège

La bibliothèque d'un club d'escalade gère le prêt de baudriers et de cordes avec un système
maison. Un vendredi, le rapport de disponibilité affiche parfois deux baudriers de plus que
le stock réel : mais pas toujours, et jamais de manière reproductible en local. Tu ouvres le
code, tu regardes la fonction de calcul de stock, elle te semble correcte. Tu ajoutes un
`console.log`, tu ne vois rien d'anormal sur ton poste. Tu conclus "ça doit être un problème
de cache côté client" et tu passes à autre chose. Trois semaines plus tard, le même bug
revient, plus grave. Tu n'as jamais eu de méthode : tu as eu une intuition non vérifiée que
tu as prise pour une conclusion.

## Ce qui se passe vraiment

Déboguer sans méthode, c'est chercher une aiguille dans une botte de foin en retournant le
foin au hasard. Déboguer avec méthode, c'est diviser la botte en deux, vérifier dans quelle
moitié se trouve l'aiguille, et répéter : une recherche qui converge de manière garantie,
quelle que soit la taille du foin.

```text
Recherche au hasard (sans méthode)
────────────────────────────────────
Complexité pire cas : O(n)  : proportionnelle à la taille du code suspect
Complexité moyenne  : imprévisible, dépend de la chance et de l'intuition

Bissection (avec méthode)
────────────────────────────────────
Complexité pire cas : O(log n) : pour 1000 commits suspects, ~10 étapes suffisent
Complexité garantie : converge toujours, indépendamment de la chance
```

### Étape 1 : Reproduire avant de chercher

Un bug non reproductible n'est pas un bug à corriger, c'est une hypothèse à confirmer.
Avant toute investigation dans le code, construis le scénario minimal qui déclenche le
problème de manière fiable : même si ce scénario est laid, même s'il ne ressemble pas à
l'usage réel. Un bug reproduit à volonté à 100% des essais divise le temps de correction par
cinq, parce que chaque hypothèse peut être testée immédiatement, sans attendre une
réapparition aléatoire en production.

### Étape 2 : Bissection systématique

La bissection ne s'applique pas qu'à l'historique Git. C'est un principe général : diviser
l'espace des causes possibles en deux, tester quel côté contient le problème, répéter.

```bash
# Bissection sur l'historique Git : trouver le commit qui a introduit une régression
git bisect start
git bisect bad                  # le commit actuel (HEAD) est buggé
git bisect good v1.4.0          # cette version-là était saine
# Git choisit automatiquement un commit au milieu, tu le testes, tu réponds :
git bisect good   # ou
git bisect bad
# Répète jusqu'à isoler le commit exact : log(n) étapes, jamais plus.
git bisect reset
```

La même logique s'applique sans Git : si le bug apparaît dans une fonction de 200 lignes,
commente ou isole la moitié, vérifie si le symptôme persiste, répète sur la moitié
suspecte. Chaque étape élimine la moitié de l'espace de recherche restant : c'est la seule
approche qui garantit une convergence rapide sur du code inconnu.

### Étape 3 : Hypothèses falsifiables, pas des intuitions

Une hypothèse de debug utile a une propriété précise : elle prédit un résultat observable
différent selon qu'elle est vraie ou fausse. "Ça doit être un problème de cache" n'est pas
une hypothèse falsifiable tant que tu n'as pas dit comment le vérifier. "Si c'est un
problème de cache client, alors désactiver le cache dans les DevTools doit faire disparaître
le symptôme immédiatement" est falsifiable : le test tranche.

```text
Hypothèse non falsifiable          Hypothèse falsifiable
──────────────────────────         ─────────────────────────────────────
"Ça doit venir de la base"    →    "Si ça vient de la base, la requête SQL
                                     isolée doit reproduire le doublon
                                     directement dans un client SQL, hors
                                     application"
```

### Étape 4 : Logs ciblés, pas logs partout

Un log ajouté au hasard dans une fonction suspecte pollue le signal. Un log utile répond à
une question précise posée avant de l'écrire : "à cet instant précis, quelle est la valeur
qui doit être X si mon hypothèse est vraie ?"

```typescript
// Mauvais : log générique, aucune hypothèse derrière
console.log("stock", stock);

// Utile : log qui teste une hypothèse précise (race condition sur deux requêtes concurrentes)
console.log(
  `[stock-check] req=${requestId} avant_lecture=${Date.now()} valeur_lue=${stock}`,
);
// → si deux req logguent la même valeur_lue avant qu'aucune n'ait écrit, c'est une race
//   condition confirmée, pas une supposition.
```

### Étape 5 : Profilers : mesurer avant d'optimiser, et savoir ce qu'ils cachent

Un profiler te dit où le temps est passé : mais seulement dans les conditions où tu l'as
lancé. Trois pièges de mesure reviennent sans cesse :

```text
Piège 1 : Environnement non représentatif
  Profiler en local avec une base de données vide ne révèle jamais les problèmes
  de volume qui n'apparaissent qu'à 100 000 lignes.

Piège 2 : Échantillonnage qui masque les événements rares
  Un profiler statistique (sampling) peut manquer un appel lent qui se produit une fois
  sur mille : exactement le genre de bug qu'on cherche à isoler.

Piège 3 : Optimiser la fonction la plus visible plutôt que la plus coûteuse
  Le profiler montre du temps passé dans une fonction utilitaire appelée partout ;
  ce n'est pas forcément elle le goulot : regarde le temps cumulé, pas le nombre d'appels.
```

## Compromis

| Option                                          | Coût                                               | Bénéfice                                           | Quand choisir                                                                    |
| ----------------------------------------------- | -------------------------------------------------- | -------------------------------------------------- | -------------------------------------------------------------------------------- |
| Tâtonnement intuitif                            | Rapide sur les bugs triviaux                       | Ne scale pas, aucune garantie de convergence       | Bug évident, une ligne, contexte que tu connais déjà bien                        |
| Bissection systématique                         | Discipline, quelques étapes incompressibles        | Convergence garantie même sur du code inconnu      | Bug non local, régression apparue entre deux versions                            |
| Logs ciblés par hypothèse                       | Temps de réflexion avant d'écrire le log           | Signal exploitable immédiatement, pas de bruit     | Bug intermittent, race condition, comportement dépendant du contexte d'exécution |
| Profiler en environnement de prod représentatif | Coût d'infrastructure, accès aux données réalistes | Seule mesure qui ne ment pas sur les vrais goulots | Avant toute optimisation de performance, jamais après                            |

## Pièges classiques

- **Corriger sans avoir reproduit.** Symptôme : le correctif "semble" marcher en local, le
  bug revient en production trois semaines plus tard identique.
- **Changer plusieurs choses à la fois pendant une bissection.** Symptôme : le bug disparaît
  mais tu ne sais pas lequel des trois changements l'a réellement corrigé : tu ne peux rien
  documenter ni réutiliser.
- **Logs laissés en production après le debug.** Symptôme : les logs de debug polluent les
  vrais logs applicatifs pendant des mois, jusqu'à ce que quelqu'un les retrouve par hasard.
- **Optimiser sur la base d'une intuition sans profiler.** Symptôme : le code "optimisé" est
  plus complexe, plus dur à maintenir, et le profiler montre après coup que le vrai goulot
  était ailleurs.

## Ce que tu dois savoir défendre

- Explique pourquoi la bissection garantit une convergence en O(log n) alors que le
  tâtonnement n'a aucune garantie.
- Donne un exemple d'hypothèse non falsifiable que tu as toi-même formulée un jour, et
  reformule-la en hypothèse falsifiable.
- Cite les trois pièges de mesure d'un profiler et pour chacun un exemple où il t'aurait
  fait tirer une conclusion fausse.
