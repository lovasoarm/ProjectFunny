# Grimoire : Niveau 14, Tool Cave

Mémo à ouvrir avant une session de debug ou une revue de code généré par IA. Sert à
vérifier la méthode, pas à réciter une liste d'outils.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Hypothèse falsifiable | Une affirmation qui prédit un résultat observable différent selon qu'elle est vraie ou fausse. | `// si vrai: log X apparait ; si faux: log X absent` | diagnostic du chef qui prédit précisément le goût si l'ingrédient est en cause / hypothèse du skipper qui prédit précisément la dérive si le courant est en cause |
| Bissection | Diviser l'espace de recherche en deux à chaque étape pour converger en O(log n). | `git bisect start && git bisect bad && git bisect good v1.2.0` | vérifier la moitié des plats du service pour isoler celui qui a raté / vérifier la moitié du parcours pour isoler où la corde a lâché |
| Log ciblé | Une trace ajoutée pour trancher une question précise, retirée ensuite. | `console.log("DEBUG cle_idempotence=", cle); // a retirer avant merge` | note prise en cuisine juste pour vérifier une cuisson, jetée après / repère marqué juste pour vérifier un cap, effacé après |
| Cause racine vs symptôme | Le symptôme casse visiblement, la cause racine produit ce symptôme en amont. | `// corriger le calcul de stock, pas juste masquer l'alerte de rupture` | traiter la fièvre sans traiter l'infection aux urgences / colmater une fuite sans réparer la coque fissurée |
| Hypothèse silencieuse d'une réponse IA | Le choix implicite qu'un modèle fait pour compléter un prompt sous-spécifié. | `// le modele a suppose email unique sans qu'on le precise` | commis qui invente une quantité non précisée sur la commande / matelot qui invente un cap non précisé par le skipper |
| Compromis nommé et assumé | La décision qui dit explicitement ce qu'elle sacrifie et pourquoi c'est acceptable. | `// on sacrifie la validation stricte cette nuit, corrige avant lundi` | chef qui annonce assumer un plat de secours faute de temps / skipper qui annonce assumer une route plus longue faute de vent |
| Honnêteté sur l'incertitude | Nommer ce que tu n'as pas vérifié, plutôt que de le présenter comme un fait. | `// NON VERIFIE : cause probable, a confirmer avec les logs de prod` | chef qui dit ne pas être sûr du fournisseur en cause / skipper qui dit ne pas être sûr de la cause de la dérive |
| Commit unitaire sans transaction globale | Un traitement par lot qui valide chaque élément séparément, sans rollback global. | `for (const item of lot) { await traiter(item); await marquerFait(item.id); }` | chaque plat du service facturé séparément, pas en un seul ticket global / chaque nœud vérifié séparément, pas en un seul geste global |

## Défense orale

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Hypothèse falsifiable | Tu tournes en rond en changeant du code au hasard, sans savoir si tu te rapproches | Donne un exemple d'hypothèse sur un bug vécu, et le résultat qui l'aurait infirmée |
| Bissection | Tu inspectes les changements un par un, en O(n), tu perds un temps proportionnel au problème | Pourquoi une recherche par bissection sur 1000 commits prend au maximum 10 étapes ? |
| Log ciblé | Tu ajoutes des logs génériques partout, tu noies le signal et tu oublies de les retirer | Quelle question précise ton dernier log de debug devait-il trancher ? |
| Cause racine vs symptôme | Tu corriges le symptôme, le même bug revient sous une autre forme la semaine suivante | Cite un bug où corriger le symptôme sans corriger la cause aurait suffi à repasser les tests |
| Hypothèse silencieuse d'une réponse IA | Tu livres un code qui répond à ta question mais pas à ton besoin réel | Comment repères-tu qu'un prompt était sous-spécifié après coup ? |
| Compromis nommé et assumé | Tu prends une décision sous pression sans dire ce qu'elle coûte, personne ne peut la challenger | Sur ta dernière décision urgente, qu'as-tu sacrifié et pourquoi c'était le bon ordre de priorité ? |
| Honnêteté sur l'incertitude | Une hypothèse non vérifiée se propage, d'autres construisent dessus sans la questionner | Sur ton dernier rapport de bug, quelle affirmation n'avait en réalité pas de preuve derrière elle ? |
| Commit unitaire sans transaction globale | Une interruption laisse un état partiel, impossible à rejouer sans risquer un doublon | Pourquoi relancer un lot à commit unitaire depuis le début double le travail déjà validé ? |

Grille détaillée : voir [boss-fight.md](./boss-fight.md).

## Méthode de debug, en une page

```text
1. Reproduire à volonté (sinon : construire le scénario minimal, quitte à le rendre laid)
2. Formuler une hypothèse falsifiable (prédit un résultat observable différent si vraie/fausse)
3. Diviser l'espace de recherche en deux (bissection : historique, code, données, config)
4. Tester la moitié suspecte, répéter jusqu'à convergence (O(log n), jamais O(n))
5. Confirmer la cause racine avec un log ciblé ou un test isolé, pas une intuition
6. Écrire un test de non-régression AVANT de considérer le bug clos
7. Retirer les logs de debug ajoutés en cours de route
```

## Checklist de vérification avant de coller du code généré par IA

- [ ] Je peux expliquer cette logique à voix haute sans relire le code.
- [ ] J'ai identifié l'hypothèse implicite sur les données d'entrée (nulls, formats, ordres
      de grandeur).
- [ ] J'ai vérifié que les cas limites de mon domaine précis sont couverts, pas ceux d'un
      domaine générique.
- [ ] J'ai testé sur au moins un cas dont je connais la réponse correcte à la main, avant
      d'écrire le code.

## Prompt de décision : patron réutilisable

```text
"Je dois [tâche]. Liste-moi les approches possibles, avec pour chacune :
- l'hypothèse métier qu'elle suppose implicitement,
- un cas où elle serait fausse ou injuste,
- son coût de mise en œuvre.
Ne génère pas encore de code : j'ai besoin de choisir en connaissance de cause d'abord."
```

## Signaux d'alarme : code généré à relire en priorité absolue

- Le code "a l'air fini" en trois secondes sur un problème qui te prendrait normalement
  vingt minutes à réfléchir : la vitesse cache probablement une hypothèse non vérifiée.
- Aucune gestion de cas limite ou d'erreur visible dans la première lecture.
- Des noms de variables génériques (`data`, `value`, `result`) sur une logique métier
  précise : signe que le modèle a généralisé au lieu de traiter ton cas réel.
- Un commentaire qui explique "ce que fait le code" sans jamais expliquer "pourquoi ce choix
  plutôt qu'un autre" sur un point ambigu du domaine.

## Trois sources de référence pour sortir d'un doute d'outillage, du plus fort au plus faible

1. Un résultat mesuré en conditions réalistes (profiler en environnement représentatif,
   test de non-régression qui échoue puis passe).
2. Une revue par quelqu'un de plus expérimenté que toi sur le sujet précis.
3. Ta propre relecture à froid, une fois la pression du moment retombée.

## Commandes prêtes à copier

```bash
# 1. Bissection git pour isoler le commit qui a introduit une régression.
git bisect start
git bisect bad HEAD
git bisect good v1.2.0
# git testera chaque commit intermédiaire, marque-le bad/good jusqu'à convergence
```

```bash
# 2. Compter les requêtes SQL réellement exécutées par un test, pour détecter un N+1.
# suppose un log de requêtes actif en environnement de test (ex: variable d'env dédiée)
DEBUG_SQL_COUNT=1 npm test -- --grep "liste des réservations"
```

```bash
# 3. Rejouer un lot en ignorant les éléments déjà marqués comme traités (commit unitaire).
# n'agit jamais sur l'ensemble du lot d'un bloc, seulement sur ce qui reste en attente
psql -d prod -c "SELECT id FROM lot_import WHERE statut = 'en_attente' LIMIT 100;"
```

```bash
# 4. Grep ciblé sur l'historique pour retrouver quand une valeur par défaut a changé.
git log -p --follow -S"DEFAULT 'M'" -- migrations/materiel.sql
```

```bash
# 5. Isoler un test unique pour reproduire un bug sans faire tourner toute la suite.
npx vitest run src/reservation.test.ts -t "refuse un chevauchement de créneau"
```

## Formule à retenir

```text
Vitesse de livraison utile = Comprehension du probleme x Maitrise des outils

Un facteur proche de zéro effondre le produit, quelle que soit la qualité de l'autre.
```

## Si tu rates le boss-fight

Relis d'abord le critère qui a plafonné ta note : justification par un mécanisme, protocole
de vérification sans nouvelle exécution, ou cohérence avec la contrainte "une seule
tentative". Reprends les logs fournis et refais la bissection à la main avant de répondre à
nouveau. Relis la méthode de debug en une page ci-dessus. Attends 48 h avant de retenter le
boss-fight pour juger la scène à froid. Si l'échec se reproduit sur le même critère,
redescends au niveau 07 relire "idempotence" : une relance de lot mal maîtrisée cache souvent
une opération non idempotente.
