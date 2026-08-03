# Pourquoi la plupart des devs stagnent

## La scène

Amine a cinq ans d'expérience. Il code vite, connaît trois frameworks, résout les tickets
Jira dans les temps. Un jour, son lead lui confie la refonte du système de réservation de
créneaux d'un cabinet vétérinaire multi-sites : trois vétérinaires, des créneaux qui se
chevauchent selon les spécialités, des annulations de dernière minute qui doivent libérer
un créneau sans casser l'historique de facturation. Amine ouvre son éditeur et commence à
écrire du code le jour même. Trois semaines plus tard, la fonctionnalité "marche" en démo,
mais personne ne sait répondre à : que se passe-t-il si deux réceptionnistes réservent le
même créneau à la même seconde ? Le code n'a pas de réponse parce que la question n'a jamais
été posée. Amine ne sait pas qu'il ne sait pas.

Ce n'est pas un problème de compétence technique. Amine sait coder. Le problème est en amont :
il n'a pas de modèle mental du système avant d'écrire la première ligne.

## Ce qui se passe vraiment

Il y a deux façons de progresser en ingénierie logicielle, et elles ne se ressemblent pas :

1. **Accumuler des exécutions** : apprendre une syntaxe, un framework, un pattern, et
   l'appliquer quand on reconnaît la forme du problème. C'est rapide à démarrer, ça donne
   une impression de progrès immédiate (tu livres des tickets), et ça plafonne vite. Le
   plafond arrive exactement au moment où le problème ne ressemble à aucun pattern connu.
2. **Construire des modèles mentaux** : comprendre les forces en jeu derrière un problème —
   concurrence, cohérence, coût de changement, incertitude : de sorte qu'un problème jamais
   vu se laisse quand même analyser. C'est plus lent à démarrer (tu livres moins vite au
   début) et ça ne plafonne jamais vraiment, parce que le modèle se transfère.

La stagnation vient d'un cercle qui se referme tout seul :

```text
   exécuter sans modèle
          │
          ▼
   ça marche "en surface"
          │
          ▼
   personne ne questionne (la démo passe)
          │
          ▼
   aucune rétroaction sur la qualité du raisonnement
          │
          ▼
   le dev répète la même méthode → stagnation
          │
          └──────────────┐
                          ▼
                 (retour en haut, un an plus tard,
                  même niveau de compréhension,
                  juste plus de vocabulaire technique)
```

Ce qui casse ce cercle, ce n'est pas "plus d'expérience". Amine a de l'expérience : cinq ans.
Ce qui le casse, c'est une contrainte externe qui force la question qu'on ne se pose jamais
spontanément : _pourquoi ce choix plutôt qu'un autre, et qu'est-ce que ça coûte si j'ai tort ?_

C'est exactement ce que ce curriculum injecte à chaque niveau : pas plus de syntaxe, mais
des situations où l'exécution seule ne suffit pas, où il faut expliciter un modèle, le
défendre, et vivre avec les conséquences d'un mauvais choix pour apprendre à en reconnaître
un bon.

### Le signal qui ne trompe pas

Un dev qui exécute sans modèle mental a un symptôme reconnaissable : il ne peut pas répondre
à "qu'est-ce qui casserait si on changeait X ?" sans relire le code. Un dev avec un modèle
mental répond en quelques secondes, parce qu'il sait déjà où sont les frontières, les états
partagés, les hypothèses fragiles. Ce curriculum vise à construire ce deuxième réflexe.

## Compromis

| Option                                 | Coût                                                                                  | Bénéfice                                                                | Quand choisir                                                                        |
| -------------------------------------- | ------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Apprendre par accumulation de patterns | Rapide au démarrage, faible charge cognitive initiale                                 | Productif vite sur des problèmes déjà vus                               | Quand le domaine est stable et bien balisé (CRUD standard, intégrations connues)     |
| Apprendre par modèles mentaux          | Plus lent au démarrage, demande de la friction volontaire (écrire, défendre, mesurer) | Se transfère à des problèmes jamais vus, résiste au changement de stack | Dès que le problème a de l'incertitude, de la concurrence, ou un coût d'erreur élevé |

Ce curriculum choisit la deuxième voie délibérément, y compris quand elle semble plus lente
au début. C'est un pari : la vitesse initiale perdue se rembourse largement dès le troisième
ou quatrième problème "jamais vu".

## Pièges classiques

- Confondre "ça compile et la démo passe" avec "je comprends le système" : le symptôme est
  l'incapacité à répondre aux questions "et si" sans retourner lire le code.
- Croire que l'expérience seule construit le modèle mental : le symptôme est de refaire la
  même erreur de conception à des années d'écart, sur des stacks différentes.
- Éviter d'écrire ses décisions parce que "c'est évident" : le symptôme apparaît six mois
  plus tard, quand plus personne (soi-même inclus) ne se souvient pourquoi.
- Traiter la vitesse de livraison comme la seule métrique de compétence : le symptôme est
  une vélocité élevée sur des features qui génèrent une dette qui ralentit tout le monde
  ensuite.

## Ce que tu dois savoir défendre

1. Pourquoi quelqu'un avec cinq ans d'expérience peut avoir moins de modèle mental qu'un
   junior qui vient de finir ce prologue.
2. Quel est le symptôme observable qui distingue "exécuter sans modèle" de "raisonner avec
   un modèle" : donne un exemple concret, pas une définition.
3. Pourquoi ce curriculum accepte d'être plus lent au démarrage plutôt que de maximiser la
   vitesse de livraison de tickets dès le premier niveau.
