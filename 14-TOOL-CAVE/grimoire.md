# Grimoire : Tool Cave

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
|---|---|---|---|
| Hypothèse falsifiable | Une affirmation qui prédit un résultat observable différent selon qu'elle est vraie ou fausse | Tu tournes en rond en changeant du code au hasard, sans savoir si un changement a rapproché ou éloigné de la cause | Donne un exemple d'hypothèse sur un bug que tu as vécu, et le résultat qui l'aurait infirmée |
| Bissection | Diviser l'espace de recherche (historique git, code, données, config) en deux à chaque étape pour converger en O(log n) | Tu inspectes les changements un par un, en O(n), et tu perds un temps proportionnel à la taille du problème | Pourquoi une recherche par bissection sur 1000 commits prend au maximum 10 étapes |
| Log ciblé | Une trace ajoutée spécifiquement pour trancher une question précise, retirée une fois la réponse obtenue | Tu ajoutes des logs génériques partout, tu noies le signal utile, et tu oublies de les retirer avant prod | Quelle question précise ton dernier log de debug devait-il trancher |
| Cause racine vs symptôme | Le symptôme est ce qui casse visiblement ; la cause racine est le mécanisme qui produit ce symptôme, souvent plus en amont | Tu corriges le symptôme (un cas particulier), le même bug revient sous une autre forme la semaine suivante | Cite un bug où corriger le symptôme sans corriger la cause aurait suffi à repasser les tests |
| Hypothèse silencieuse d'une réponse IA | Le choix implicite qu'un modèle fait pour compléter un prompt sous-spécifié, jamais écrit noir sur blanc | Tu livres un code qui répond littéralement à ta question mais pas à ton besoin réel, l'écart n'apparaît qu'en production | Comment repères-tu qu'un prompt était sous-spécifié après coup, sur ta réponse IA la plus récente |
| Compromis nommé et assumé | La décision qui dit explicitement ce qu'elle sacrifie et pourquoi ce sacrifice est acceptable dans le contexte | Tu prends une décision sous pression sans dire ce qu'elle coûte, personne ne peut la challenger ni la revenir en arrière en connaissance de cause | Sur ta dernière décision urgente, qu'as-tu sacrifié et pourquoi c'était le bon ordre de priorité |
| Honnêteté sur l'incertitude | Nommer explicitement ce que tu n'as pas vérifié, plutôt que de présenter une hypothèse comme un fait | Une hypothèse non vérifiée présentée comme certaine se propage : d'autres construisent dessus sans la questionner | Sur ton dernier rapport de bug, quelle affirmation n'avait en réalité pas de preuve derrière elle |
| Commit unitaire sans transaction globale | Un traitement par lot qui valide chaque élément séparément, sans rollback global en cas d'échec en cours de route | Une interruption au milieu du lot laisse un état partiel (certains éléments traités, d'autres non), impossible à rejouer sans risquer un doublon | Pourquoi relancer un lot à commit unitaire depuis le début double le travail déjà validé |

## Comportements évalués en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'échec |
|---|---|---|
| Justification par un mecanisme | La cause racine est nommee et reliee au code et aux logs fournis, pas seulement au message d'erreur affiche | Une cause racine affirmee sans lien explicite avec une ligne de log, de code ou de git log fournie |
| Protocole de verification sans nouvelle execution | Le protocole utilise uniquement les logs, git log et requetes SQL fournis pour confirmer l'hypothese avant d'agir | Une verification qui suppose de relancer le job ou d'obtenir une donnee non fournie dans l'enonce |
| Compromis nomme et assume | La decision dit explicitement ce qui est sacrifie et pourquoi ce sacrifice est le bon ordre de priorite sous contrainte de temps | Une decision qui n'evoque aucun sacrifice, comme si elle n'avait aucun cout |
| Honnetete sur ce que tu ne sais pas | La copie liste au moins un point non verifiable cette nuit-la et prevoit sa verification ulterieure | Une affirmation presentee comme certaine alors qu'elle n'est pas etayee par les logs fournis |
| Coherence de la decision avec la contrainte "une seule tentative" | La decision traite separement les unites deja committees, celles en attente, et le cas en echec, sans action globale non maitrisee | Une relance aveugle du batch complet qui expose au double commit |

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

## Formule à retenir

```text
Vitesse de livraison utile = Comprehension du probleme x Maitrise des outils

Un facteur proche de zéro effondre le produit, quelle que soit la qualité de l'autre.
```
