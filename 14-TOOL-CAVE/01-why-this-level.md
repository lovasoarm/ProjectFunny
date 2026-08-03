# Pourquoi ce niveau existe

## Le piège

Deux développeurs reçoivent le même ticket : le système de réservation de créneaux d'un
cabinet vétérinaire double parfois un rendez-vous sur le même vétérinaire à la même heure.
Le premier ouvre le fichier suspect, ajoute des `console.log` un peu partout, relance
l'appli à la main quinze fois, change une condition au hasard parce que "ça a l'air louche",
et ferme le ticket quarante minutes plus tard sans être sûr d'avoir compris le problème. Le
second pose une hypothèse précise, la falsifie avec un test qui isole exactement la
condition de course suspectée, trouve la cause en huit minutes, et écrit un test de
non-régression avant de corriger. Même langage, même framework, même ancienneté. La
différence, ce n'est pas leur compréhension du code métier. C'est leur maîtrise d'outillage
et de méthode.

## Ce qui se passe vraiment

La compétence d'un développeur n'est pas linéaire : elle a deux composantes multiplicatives,
pas additives :

```text
Vitesse de livraison utile ≈ Compréhension du problème × Maîtrise des outils

Si Maîtrise des outils tend vers 0 (recherche manuelle constante, pas de méthode de debug,
IA utilisée sans vérification), le produit s'effondre même avec une excellente
compréhension du problème.
```

Une bonne compréhension du domaine ne compense pas une mauvaise méthode d'outillage : elle
la rend seulement moins visible sur les cas faciles. Sur les cas difficiles (bug
intermittent, régression de performance discrète, code legacy mal documenté), l'écart entre
un développeur outillé et un développeur qui improvise devient un facteur de 5 à 10 en temps
passé, pas de 20%.

Ce niveau isole trois familles d'outils qui, mal maîtrisées, plafonnent silencieusement ta
vitesse et ta fiabilité sans que tu t'en rendes compte, parce que tu compares toujours ta
vitesse d'aujourd'hui à ta vitesse d'hier : jamais à ce qu'elle pourrait être :

1. **L'éditeur et le shell** : les outils que tu utilises littéralement chaque minute. Une
   friction de deux secondes répétée cinq cents fois par jour coûte plus cher qu'une
   friction de trente minutes une fois par mois : et elle est invisible parce qu'elle ne
   fait jamais assez mal pour déclencher une remise en question.
2. **La méthode de debug** : sans méthode, chaque bug est une aventure nouvelle où tu
   redécouvres les mêmes principes par tâtonnement. Avec une méthode, chaque bug devient une
   instance d'un problème déjà résolu en général.
3. **L'IA générative** : le plus récent et le plus dangereux des trois, parce qu'il ressemble
   à un raccourci gratuit alors qu'il déplace le coût ailleurs : de la production du code
   vers la vérification du code, une compétence que peu de développeurs ont explicitement
   entraînée.

## Ce que ça coûte de sauter ce niveau

```text
Sans maîtrise d'outillage                    Avec maîtrise d'outillage
────────────────────────────                 ──────────────────────────
Bug résolu par tâtonnement, cause         →  Bug résolu par hypothèse falsifiée,
réelle jamais confirmée                       cause confirmée et documentée

Friction d'éditeur ignorée, fatigue       →  Friction identifiée et éliminée,
accumulée en fin de journée                   énergie mentale préservée pour le
                                               vrai problème

Code généré par IA collé tel quel,        →  Code généré par IA relu comme du
bug découvert en production                   code d'un junior inconnu, bug
                                               attrapé en revue
```

Ce coût ne se voit pas sur un sprint. Il se voit sur une carrière : c'est la différence
entre un développeur qui plafonne à "correct" et un développeur dont chaque année de
pratique produit un vrai gain de vitesse et de fiabilité mesurable.

## Compromis

| Option                                                      | Coût                                   | Bénéfice                                      | Quand choisir                                                      |
| ----------------------------------------------------------- | -------------------------------------- | --------------------------------------------- | ------------------------------------------------------------------ |
| Ignorer l'outillage, se concentrer sur "le code qui compte" | Zéro coût d'apprentissage immédiat     | Illusion de productivité court terme          | Jamais volontairement : seulement sous deadline extrême ponctuelle |
| Investir une à deux semaines dans l'outillage maintenant    | Temps non livré à court terme          | Gain de vitesse composé sur toute la carrière | Dès que tu as un projet réel derrière toi pour ancrer la pratique  |
| Apprendre l'outillage "sur le tas", au fil des frustrations | Apprentissage lent, lacunes aléatoires | Pas de temps dédié à bloquer                  | Si aucune fenêtre dédiée n'est possible, en dernier recours        |

## Pièges classiques

- **Confondre vitesse de frappe et vitesse de livraison.** Symptôme : tu tapes très vite
  mais tu passes autant de temps à chercher où est le problème qu'avant : la frappe n'était
  jamais le goulot.
- **Optimiser l'éditeur sans jamais toucher à la méthode de debug.** Symptôme : ton setup est
  impressionnant en démo, mais un bug intermittent te fait toujours perdre une demi-journée.
- **Traiter l'IA comme un problème binaire "pour ou contre".** Symptôme : soit tu refuses
  l'outil par principe et tu perds un gain réel, soit tu l'acceptes sans vérification et tu
  livres des bugs que tu n'as pas écrits mais que tu dois quand même assumer.

## Ce que tu dois savoir défendre

- Explique pourquoi la maîtrise d'outillage a un effet multiplicatif, pas additif, sur ta
  vitesse de livraison.
- Donne un exemple personnel de friction d'outillage que tu as ignorée pendant des mois
  parce qu'elle ne faisait jamais assez mal d'un coup.
- Explique pourquoi l'IA générative déplace le coût du travail plutôt que de le supprimer,
  et vers quelle compétence précisément.
