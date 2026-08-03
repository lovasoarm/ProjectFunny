# Pourquoi ce niveau existe

## La scène

Jeudi, 17h50. Le cabinet vétérinaire ferme dans dix minutes. L'accueil a trois personnes en attente
et deux appels en cours. Camille clique sur "Confirmer le rendez-vous" pour Monsieur Petit et son
chat. L'écran ne bouge pas pendant deux secondes : le wifi du cabinet est capricieux. Elle reclique.
Puis une troisième fois, en pestant. Le lendemain, la praticienne découvre trois rendez-vous
identiques à 9h00 pour le même chat, et un créneau bloqué pour rien toute la matinée.

Personne n'a "mal codé" au sens où on l'entend d'habitude. La fonction `createAppointment` marche
très bien, testée, propre. Le bug n'est pas dans la fonction. Il est dans tout ce qu'il y a autour :
que se passe-t-il entre le clic et la réponse ? Que voit Camille pendant ces deux secondes ? Que fait
le serveur si la même requête arrive trois fois ?

## Ce qui se passe vraiment quand on ignore ça

Un développeur qui code "l'écran qui marche" code en réalité 20% du produit. Les 80% restants sont
les chemins que personne ne montre en démo : la liste vide un lundi matin sans rendez-vous, l'erreur
réseau au milieu d'un scroll, le formulaire à moitié rempli quand la session expire, le tableau de
300 rendez-vous qui rend le navigateur inutilisable.

Ce niveau ne t'apprend pas de nouvelle techno. Il t'apprend à voir les états et les parcours que ton
cerveau de développeur a tendance à effacer, parce qu'ils n'apparaissent jamais dans le "happy path"
que tu testes en premier.

## Le coût réel de l'ignorer

```text
Sans ce niveau                          Avec ce niveau
───────────────                         ──────────────
"ça marche chez moi"          →         "ça marche sur 3G, en double-clic,
                                          hors-ligne, et avec 10 000 lignes"

écran = fonction du succès    →         écran = fonction de l'état
                                          (idle / loading / error / empty / partial)

formulaire = appel API        →         formulaire = validation double +
                                          clé d'idempotence + message actionnable

"le serveur va gérer ça"      →         "je sais ce que fait le serveur, et
                                          ce que je fais s'il ne répond pas"
```

## Pourquoi la véto et pas une todo-list

Une todo-list n'a pas de concurrence réelle (peu importe qui coche la case en premier), pas
d'enjeu (une tâche en double, on s'en fiche), pas de vraie donnée sensible. Un cabinet vétérinaire a
tout ça : deux personnes qui réservent le même créneau, un client qui appelle en même temps que
l'accueil clique, un dossier médical qu'on ne veut pas perdre à cause d'un rafraîchissement de page.
C'est un terrain qui te force à prendre les décisions que les vrais produits t'imposent.

## Ce que tu vas apprendre à repérer

- Un écran qui ne montre que "ça a marché" cache toujours au moins quatre autres états.
- Un bouton qui ne se désactive pas pendant l'attente est une invitation au double submit.
- Une validation qui n'existe que côté client est une validation qui n'existe pas.
- "L'utilisateur ne fera jamais ça" est une phrase qui précède presque toujours un incident.

## Ce que tu dois savoir défendre

- Pourquoi un écran qui gère bien le cas "ça marche" n'est fiable qu'à 20% ?
- Donne un exemple concret où l'absence de gestion d'état a un coût métier mesurable (pas juste
  "c'est moche").
- Pourquoi la concurrence (deux personnes qui agissent en même temps) est un problème de design
  d'interface, pas seulement un problème de base de données ?
