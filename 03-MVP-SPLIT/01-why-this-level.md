# Pourquoi ce niveau existe

## Le piège

Une association de tournées de livraison à vélo pour commerçants de quartier te demande une
plateforme complète : inscription des commerçants, catalogue produit, prise de commande,
optimisation de tournée, paiement en ligne, notifications SMS, tableau de bord de suivi
en temps réel pour les livreurs. Tu regardes ça, tu estimes trois mois, tu te lances en
construisant "les fondations" d'abord : base de données complète, authentification, API
générique pour tous les modules. Après six semaines, rien n'est utilisable. Pas une seule
commande n'a encore été livrée en vrai. L'association, qui payait sur trésorerie limitée,
arrête le projet à mi-chemin. Ce qui existe : une base solide, zéro valeur livrée.

Deuxième cas, une autre association de livraison, six mois plus tard : le développeur suivant
applique le découpage vertical dès le premier jour. Le 2026-03-02, une seule tranche
fonctionne : un commerçant, une tournée, un livreur, sans authentification ni paiement, juste
un lien partagé à usage unique. Trois jours de travail. Cette tranche révèle immédiatement
que les livreurs veulent voir l'ordre des arrêts sur une carte, pas une liste : personne ne
l'avait anticipé. Le coût de ce changement de direction, découvert à J+3, est d'une demi-journée.
Découvert à J+40 dans l'approche par couches, il aurait coûté une refonte complète du frontend.

## Ce qui se passe vraiment

Le découpage d'un projet en couches techniques (d'abord la base de données, puis l'API,
puis le frontend) donne l'illusion de progresser methodiquement, mais ne produit aucune
valeur testable avant que toutes les couches soient terminées. À l'inverse, un découpage en
tranches verticales : un scénario complet, du bouton cliqué jusqu'à la ligne en base, même
limité à un seul commerçant et une seule tournée : produit une chose utilisable et testable
en quelques jours. Ce niveau existe parce que la compétence de découpage n'est pas innée :
la tentation naturelle d'un développeur est de construire par couches, parce que c'est ainsi
qu'on raisonne techniquement. Il faut apprendre à raisonner par valeur livrée à la place.

## Qui souffre en premier

Le commanditaire souffre en premier : c'est lui qui finance des semaines sans rien voir
tourner. Mais le développeur souffre juste après, quand il faut annoncer que le budget est
épuisé avant la première démonstration utilisable, une conversation nettement plus difficile
qu'un simple retard sur une tranche déjà livrée.

## À quel moment du projet ça se manifeste

Ce risque est maximal au tout début d'un projet de taille moyenne à grande, au moment de
choisir l'ordre de construction. Il ne se manifeste pas immédiatement : c'est un projet qui
semble bien avancer pendant des semaines ("regarde, la base de données est propre, l'API
répond") avant que quelqu'un demande "peut-on l'utiliser aujourd'hui ?" et découvre que non.

## Ce que tu sais faire à la sortie

- Découper un périmètre en tranches verticales livrables indépendamment, chacune bout en
  bout.
- Tracer une ligne de coupe qui laisse un scénario fonctionnel même très limité, plutôt qu'une
  moitié de couche technique inutilisable seule.
- Estimer honnêtement, en assumant l'incertitude au lieu de la masquer par une date ronde.
- Dire non à une fonctionnalité ou reporter une tranche, avec un argument chiffré, pas une
  impression.

## Ce que ce niveau ne couvre pas

Ce niveau ne couvre pas comment identifier le bon périmètre à découper : c'est le rôle du
[niveau 02](../02-PROBLEM-HUNT/README.md), qui doit être traité avant. Il ne couvre pas non
plus le suivi de l'avancement réel une fois les tranches définies, traité au
[niveau 08](../08-ROADMAP-RUN/README.md).

## Ce que "petit" ne veut pas dire

Une tranche petite n'est pas une tranche pauvre. Le lien partage a usage unique de l'exemple
ci-dessus n'a ni authentification ni paiement, mais il livre le vrai coeur du besoin : un
commercant peut confier une tournee a un livreur et suivre son execution. Petit veut dire
"complet sur un perimetre reduit", jamais "incomplet sur un perimetre large".

## Prérequis réels

Avoir un périmètre et des non-objectifs déjà clarifiés, issus du
[niveau 02](../02-PROBLEM-HUNT/README.md). Découper un projet dont on ne sait pas encore ce
qu'il doit vraiment résoudre revient à couper au hasard.

## Erreurs de débutant les plus coûteuses

- Découper par couche technique en pensant que c'est "plus propre", alors que ça retarde
  systématiquement le premier retour terrain utile.
- Choisir une tranche verticale trop large en voulant "bien faire dès le début", ce qui
  revient à recréer une mini-couche complète et perd le bénéfice du découpage.
- Estimer une tranche en ne comptant que le code à écrire, en oubliant la coordination, les
  imprévus et la vérification, ce qui produit des dates systématiquement optimistes.
- Dire oui à toute nouvelle demande en cours de tranche, ce qui dissout la limite de la
  tranche et fait disparaître le bénéfice de l'avoir découpée.

## Le mécanisme sous-jacent

Le mécanisme n'est pas "faire petit". C'est faire passer un scénario complet, même minuscule,
par tous les points du système où une hypothèse peut se révéler fausse : l'interface, le
réseau, le stockage, et retour. Une couche isolée ne traverse jamais ces points en même temps,
donc elle ne peut révéler aucune fausse hypothèse avant que toutes les couches soient
assemblées, ce qui retarde l'apprentissage au moment où il coûte le plus cher à corriger.

```text
Decoupage horizontal (couches)            Decoupage vertical (tranches de valeur)
+--------------------------+              +--------+ +--------+ +--------+
| Base de donnees complete |              |Scenario| |Scenario| |Scenario|
+--------------------------+              |   1    | |   2    | |   3    |
| API generique            |              |(bout a | |(bout a | |(bout a |
+--------------------------+              | bout)  | | bout)  | | bout)  |
| Frontend complet         |              +--------+ +--------+ +--------+
+--------------------------+              livrable    livrable    livrable
rien d'utilisable avant la fin             des la semaine 1
```

## Analogie

Analogie : decouper un projet en tranches verticales, c'est comme le service d'urgences qui
traite un patient complet du triage a la sortie avant d'en prendre un deuxieme, et la cordee
de montagne qui equipe un relais complet avant d'avancer au suivant.
Où l'analogie casse : aux urgences et en cordee, il n'existe qu'un seul patient ou un seul
relais a la fois par definition. Un projet logiciel peut faire avancer plusieurs tranches en
parallele si l'equipe est assez grande, ce que l'analogie ne suggere pas.

## Contre-exemple : quand appliquer ce niveau serait une erreur

Un correctif de sécurité critique qui doit toucher toutes les couches en même temps (par
exemple remplacer un algorithme de hachage de mot de passe partout) n'a pas de sens à découper
en tranches verticales par scénario utilisateur : il doit être traité comme un changement
transverse unique, testé et déployé d'un bloc.

## Le coût de l'apprentissage

Compter 3 à 4 heures de lecture, et un exercice de découpage qui prend facilement 3 heures
supplémentaires, car tracer une bonne ligne de coupe demande plusieurs essais avant de trouver
la tranche qui reste vraiment livrable seule.

## Comment savoir que tu maîtrises

Le signal observable : tu peux annoncer une date de première démonstration utilisable en
moins d'une semaine sur un projet de taille moyenne, et tu la tiens, parce que la première
tranche ne dépend d'aucune autre.

## Ce que l'IA fait à ta place, et ce qu'elle ne fait pas

Une IA peut t'aider à lister les tranches possibles et à estimer chacune séparément. Elle ne
peut pas décider, à ta place, quelle tranche a le plus de valeur pour ce projet précis,
parce que cette décision dépend du contexte réel du commanditaire, de sa trésorerie, de ses
priorités politiques internes : des informations que l'IA n'a pas et que tu dois apporter.

## Comment ce niveau est réutilisé plus tard

Le découpage en tranches devient l'unité de travail suivie au
[niveau 08](../08-ROADMAP-RUN/README.md). La discipline de dire non à une fonctionnalité hors
tranche prépare directement la gestion de règles métier ajoutées trop tôt, traitée au
[niveau 06](../06-ARCHI-LAB/README.md).

## Ce que tu dois savoir défendre

- Pourquoi un découpage par couches techniques retarde systématiquement le retour terrain,
  même si chaque couche est bien construite.
- Donne un exemple, différent de celui de ce fichier, où découper par couches semblait
  raisonnable mais a fait échouer un projet.
- Ce qui distingue une "fondation nécessaire" d'un prétexte pour retarder la livraison de
  valeur.
