# Pourquoi ce niveau existe : QUALITY-SHIELD

## 1. Pourquoi ce niveau existe

Écrire du code qui marche une fois, sur ta machine, pendant que tu le regardes, est facile.
Écrire du code qui continue de marcher six mois plus tard, modifié par quelqu'un d'autre, sous
une charge que tu n'as pas testée, est une compétence distincte. Ce niveau existe parce que la
qualité n'est pas un supplément moral ("bien coder"), c'est un mécanisme concret qui déplace le
moment où une erreur est détectée : avant ou après qu'elle ait coûté cher à quelqu'un.

## 2. Ce qui casse sans lui (deux incidents chiffrés)

Incident 1 : un cabinet vétérinaire perd l'historique de rappels vaccinaux de 340 clients
pendant une nuit, suite à une migration de base de données lancée sans sauvegarde ni test de
rollback. Trois jours de reconstruction manuelle à partir de carnets papier sont nécessaires.

Incident 2 : une plateforme de tournées de livraison déploie un correctif un vendredi soir sans
revue de code ni test automatisé. Le correctif inverse silencieusement deux champs de calcul
d'itinéraire. 45 tournées du week-end partent avec des trajets faux, provoquant environ 6
heures de retard cumulé et des appels clients en nombre.

## 3. Qui souffre en premier

L'utilisateur final souffre en premier, souvent sans le savoir : il reçoit un résultat faux
sans indication que quelque chose a cassé. L'équipe de support souffre ensuite, en absorbant
des signalements qu'elle ne sait pas encore relier à une cause technique. Le développeur
responsable souffre en dernier, au moment du post-mortem, quand le coût réel est déjà payé.

## 4. Quand ça se manifeste

Le manque de filet de sécurité ne se voit pas tant que rien ne change. Il se manifeste
précisément au moment d'une modification : un changement de dépendance, une migration, un
correctif urgent. C'est pour cela qu'il est sous-estimé en début de projet, quand le code est
encore petit et que personne n'a encore eu besoin de le modifier sous pression.

## 5. Ce que tu sais faire à la sortie

Tu sais écrire des tests qui protègent réellement un comportement critique, pas des tests qui
gonflent un pourcentage de couverture sans valeur. Tu sais instrumenter un système pour savoir
ce qu'il fait en production, pas seulement en local. Tu sais mener une revue de code utile et
conduire un post-mortem qui change un mécanisme, pas seulement une personne.

## 6. Ce qui n'est pas couvert et où

- L'architecture qui rend un système testable est posée en amont, dans
  [06-ARCHI-LAB/02-boundaries-and-coupling.md](../06-ARCHI-LAB/02-boundaries-and-coupling.md).
- La gestion d'équipe autour d'un incident (communication au client, gestion du stress) est
  couverte par [10-TEAM-QUEST/04-communication-under-pressure.md](../10-TEAM-QUEST/04-communication-under-pressure.md).
- L'audit critique d'une réponse produite par une IA est traité dans
  [14-TOOL-CAVE/05-audit-dune-reponse-ia.md](../14-TOOL-CAVE/05-audit-dune-reponse-ia.md).

## 7. Prérequis

- Savoir découper un système en modules avec des frontières claires (niveau 06).
- Avoir déjà écrit au moins un test automatisé, même simple, pour comprendre le vocabulaire de
  base (assertion, fixture, mock) avant d'aborder la stratégie de test.
- Avoir un projet réel en cours : ce niveau se pratique sur du code qui vit, pas sur un exercice
  isolé sans conséquence.

## 8. Erreurs de débutant les plus coûteuses

- Viser un pourcentage de couverture de test comme objectif en soi, ce qui produit des tests
  qui vérifient des détails d'implémentation sans jamais protéger un comportement réel.
- Ajouter des logs uniquement après un incident, jamais avant, ce qui rend le prochain incident
  aussi opaque que le précédent.
- Traiter la revue de code comme une formalité à valider vite, sans jamais poser de question
  sur le raisonnement derrière un choix.
- Rédiger un post-mortem qui blâme une personne au lieu de corriger le mécanisme qui a permis
  l'erreur (absence de test, absence de garde-fou, alerte manquante).

## 9. Le mécanisme sous-jacent

Un filet de sécurité ne réduit pas le nombre d'erreurs commises : il réduit le temps entre le
moment où une erreur est introduite et le moment où elle est détectée. Plus ce délai est court,
moins l'erreur a eu le temps de se propager dans des décisions ultérieures ou dans des données
réelles utilisées par des vrais clients.

```text
Sans filet : erreur introduite --> propagation --> plainte client --> detection
Avec filet : erreur introduite --> test ou alerte --> detection immediate
La difference de cout entre les deux chemins n'est pas lineaire : plus l'erreur
a voyage loin avant detection, plus elle a contamine de decisions dependantes.
```

Analogie : un filet de sécurité logiciel, c'est le triage systématique aux urgences d'un hôpital
qui détecte un problème grave avant qu'il devienne critique, et la vérification d'un régisseur
avant chaque lever de rideau qui contrôle que chaque élément technique répond.
Où l'analogie casse : un triage ou une vérification de régie s'effectuent sur un système qui
attend, à l'arrêt. Un système logiciel continue de recevoir du trafic réel pendant qu'on le
teste ou qu'on le corrige : le filet doit fonctionner sans jamais interrompre ce qui tourne.

## 10. Contre-exemple : quand appliquer ce niveau serait une erreur

Un script jetable, écrit pour migrer une fois 200 lignes d'un vieux tableur vers une base de
données, n'a pas besoin d'une suite de tests ni d'observabilité complète : il sera exécuté une
fois, vérifié à la main sur le résultat, puis jeté. La règle de discernement : la rigueur de ce
niveau se justifie proportionnellement à la durée de vie du code et au nombre de personnes qui
en dépendront. Un code à usage unique et vérifiable à l'oeil n'a pas besoin du même filet qu'un
service de production utilisé chaque jour par des tiers.

## 11. Le coût d'apprentissage

Compter 10 à 15 heures pour construire une première stratégie de test cohérente sur un projet
réel, plus 3 à 4 heures pour mettre en place une observabilité minimale utile (logs
structurés, une alerte). Le coût principal n'est pas d'apprendre la syntaxe des outils de test,
mais d'apprendre à choisir quoi tester et quoi laisser sans test, ce qui demande de la pratique
répétée sur des cas réels, pas seulement de la lecture.

## 12. Le signal observable de maîtrise

Tu maîtrises ce niveau quand, face à un bug en production, ton premier réflexe est de te
demander "quel test aurait détecté ça avant, et pourquoi n'existait-il pas", plutôt que de
corriger le symptôme et de passer à autre chose sans ajouter de garde-fou.

Un signal complementaire : tu sais reformuler un compromis test/vitesse en une
phrase claire ('on ne teste pas ce cas rare, on accepte le risque, voici pourquoi')
plutot que de laisser ce choix implicite et non assume.

Ce reflexe se verifie aussi a l'oral : tu dois pouvoir citer, pour ton projet actuel, au moins un chemin de code volontairement laisse sans test, et la raison de ce choix.

## 13. Ce que l'IA fait et ne fait pas à ta place

Une IA peut générer rapidement des cas de test à partir d'une fonction donnée, ce qui aide à
couvrir des cas limites qu'on oublierait spontanément. Elle ne peut pas savoir, sans qu'on le
lui dise, quel comportement est réellement critique dans ton contexte métier : un test généré
automatiquement peut très bien vérifier un détail sans importance et ignorer la règle de
sécurité qui, elle, doit absolument être protégée. Voir aussi
[14-TOOL-CAVE/05-audit-dune-reponse-ia.md](../14-TOOL-CAVE/05-audit-dune-reponse-ia.md) pour
la méthode d'audit d'une suggestion produite par une IA.

## 14. Réutilisation dans les niveaux aval

- [11-BIG-APP-SNOOP](../11-BIG-APP-SNOOP/README.md) réutilise la lecture des tests existants
  comme méthode pour comprendre les intentions d'une base de code inconnue.
- [12-CAPSTONE-ARENA](../12-CAPSTONE-ARENA/README.md) exige un filet de sécurité minimal comme
  critère de passage du projet final.
- [13-DAY-TO-LEGEND](../13-DAY-TO-LEGEND/README.md) réutilise le réflexe de post-mortem comme
  boucle d'apprentissage personnelle.

## 15. Trois questions à défendre à l'oral

1. Explique pourquoi viser un pourcentage de couverture de test comme objectif en soi peut
   produire une fausse impression de sécurité.
2. Donne un exemple où l'absence de log a rendu un incident plus long à diagnostiquer qu'il
   n'aurait dû l'être.
3. Pourquoi un post-mortem qui blâme une personne plutôt qu'un mécanisme ne réduit pas le
   risque que l'incident se reproduise.

Point final de verification : relis la section 2 avant un boss-fight, les deux incidents
chiffres sont les preuves que la grille attend en premier.
