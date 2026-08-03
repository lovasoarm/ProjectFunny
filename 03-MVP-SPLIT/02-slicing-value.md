# Découper par valeur, pas par couche

## La scène

Une bibliothèque de club d'escalade veut numériser le prêt de son matériel (cordes,
baudriers, chaussons) actuellement géré sur un cahier. Le cahier des charges liste : gestion
des membres, catalogue de matériel avec état d'usure, emprunt, retour, relances de retard,
statistiques d'utilisation par matériel. Un développeur pressé de "faire les choses bien"
commence par modéliser une base de données complète pour tous ces besoins, puis construit
une API générique CRUD pour chaque entité, puis attaque le frontend. Après trois semaines,
rien ne fonctionne de bout en bout : la base est prête, l'API répond, mais aucun bénévole du
club n'a encore pu enregistrer un seul emprunt réel. Le président du club, qui avait promis
le nouvel outil à l'assemblée générale, doit annoncer un report.

## Ce qui se passe vraiment

Une **couche horizontale** est une strate technique complète (toute la base de données, toute
l'API, tout le design system) qui ne produit rien d'utilisable tant que les autres couches ne
sont pas terminées. Une **tranche verticale** est un scénario utilisateur complet, du geste
initial jusqu'à l'effet final observable, qui traverse toutes les couches nécessaires mais se
limite à un périmètre fonctionnel réduit.

Pour la bibliothèque du club, la première tranche verticale pourrait être : "un bénévole peut
enregistrer qu'une corde précise est empruntée par un membre précis, et voir que cette corde
n'apparaît plus comme disponible." Ça suppose une table membres minimale (juste un nom), une
table matériel minimale (juste un identifiant et un statut), un formulaire simple, une route
API, un affichage de disponibilité. Limité, mais complet et testable en vrai dès la fin de
la tranche : un bénévole peut l'utiliser au prochain créneau du club.

```text
Couches horizontales                    Tranches verticales
────────────────────                    ────────────────────
Base de données complète                Tranche 1 : emprunt simple
     │ (rien d'utilisable)              d'une corde par un membre
     ▼                                  → livrable, testable, utile seul
API générique CRUD
     │ (rien d'utilisable)              Tranche 2 : retour + relance
     ▼                                  de retard
Frontend complet                        → livrable, ajoute de la valeur
     │
     ▼                                  Tranche 3 : état d'usure +
Tout devient utilisable                 statistiques d'utilisation
   d'un coup, à la fin                  → livrable, complète le tableau
```

Le point clé : chaque tranche verticale a une valeur propre, indépendante des tranches
suivantes. Si le projet s'arrête après la tranche 1, le club dispose déjà d'un outil qui
remplace utilement le cahier pour l'usage le plus fréquent (l'emprunt).

## Comment reconnaître une "fausse" tranche verticale

Toutes les tranches ne se valent pas. Une tranche verticale mal choisie traverse les couches
mais ne produit rien d'exploitable en pratique.

- Une tranche est vraie si un utilisateur réel peut l'utiliser sans dépendre d'une tranche
  future non encore livrée.
- Une tranche est fausse si elle nécessite "juste encore un petit bout" pour devenir utile
  (exemple : afficher le catalogue de matériel sans pouvoir encore rien emprunter : ça ne
  change le quotidien de personne).
- Une tranche est vraie si elle peut être mesurée : on peut compter combien de fois elle a
  été utilisée en vrai.
- Une tranche est fausse si sa seule justification est "ça prépare le terrain pour la suite" —
  c'est une couche horizontale déguisée en tranche.

## Compromis

| Option                                                           | Coût                                                                                                                       | Bénéfice                                                                                                             | Quand choisir                                                                                   |
| ---------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| Découpage vertical strict                                        | Duplique parfois du code entre tranches (une table réutilisée est étendue plutôt que reconstruite proprement dès le début) | Valeur livrée dès la première tranche, retour terrain rapide                                                         | Presque toujours, en particulier en début de projet ou avec un budget incertain                 |
| Découpage horizontal complet avant toute livraison               | Rassure sur la propreté de l'architecture initiale                                                                         | Retarde tout retour terrain de plusieurs semaines, risque d'investir dans une architecture pour un besoin mal validé | Seulement quand le besoin est déjà validé à 100 % par ailleurs et que le risque produit est nul |
| Mélange : fondations minimales communes puis tranches verticales | Demande de la discipline pour ne pas dériver vers "encore une fondation"                                                   | Bon compromis entre propreté et vitesse de livraison                                                                 | Projets où plusieurs tranches partageront un socle évident (authentification, par exemple)      |

## Pièges classiques

- Appeler "MVP" une version qui contient encore toutes les fonctionnalités mais "en plus
  moche" : ce n'est pas un découpage par valeur, c'est juste une version bâclée du même
  périmètre complet.
- Croire qu'une fondation technique solide dès le départ fera gagner du temps plus tard,
  alors qu'elle retarde la première validation terrain, qui est souvent bien plus précieuse.
- Découper par écran plutôt que par scénario complet ("d'abord l'écran de connexion, puis
  l'écran de liste") : un écran seul, sans le flux qui le traverse, ne livre rien d'utile.
- Négliger la mesurabilité d'une tranche : si tu ne peux pas dire combien de fois elle a servi
  après livraison, tu ne sauras jamais si elle valait la peine d'être construite en premier.

## Ce que tu dois savoir défendre

- La différence concrète entre une tranche verticale et une couche horizontale, avec un
  exemple qui montre pourquoi une "fausse" tranche verticale ne compte pas.
- Pourquoi une fondation technique "propre dès le départ" peut être un mauvais calcul en
  début de projet, même si elle semble techniquement supérieure.
- Comment vérifier, avant de coder une tranche, qu'elle sera réellement mesurable une fois
  livrée.
