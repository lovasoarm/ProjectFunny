# Grimoire : Niveau 06, Archi-Lab

Mémo à ouvrir avant de trancher un découpage de modules ou un choix monolithe/services. Sert
à nommer le mécanisme qui casse, pas à réciter du vocabulaire d'architecture.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Couplage | A quel point un module doit connaître l'intérieur d'un autre pour fonctionner. | `import { calculTarif } from "../facturation/interne/moteur";` | commis qui doit connaître la recette secrète du chef pour dresser un plat / grimpeur qui dépend du nœud exact fait par un autre pour avancer |
| Cohésion | A quel point les éléments d'un module ont une seule raison commune de changer. | `export const ModuleReservation = { creer, annuler, deplacer };` | poste dédié uniquement aux desserts en cuisine / cordée dédiée uniquement à l'équipement, rien d'autre |
| Dépendance dirigée | Une flèche A dépend de B, qui ne doit jamais former de cycle dans le graphe global. | `// domaine/reservation.ts n'importe jamais depuis infra/*` | commande qui va toujours de la salle vers la cuisine, jamais l'inverse / ordre qui descend toujours du skipper vers l'équipage |
| Inversion de dépendance | Le domaine définit une interface, l'infra l'implémente. | `interface DepotReservation { sauver(r: Reservation): Promise<void>; }` | le chef définit la recette, le fournisseur s'adapte au cahier des charges / le skipper fixe le cap, le moteur s'adapte à la demande |
| Source de vérité | La copie d'une donnée désignée comme faisant foi en cas de désaccord. | `const stockReel = await depotStock.lireQuantite(produitId);` | le carnet de commandes du chef fait foi, pas le tableau affiché en salle / le livre de bord fait foi, pas le souvenir d'un matelot |
| Cohérence éventuelle | Un décalage temporaire et assumé entre une copie et sa source de vérité. | `// cache invalidé sous 5s, jamais utilisé pour une décision irréversible` | ardoise du jour pas encore mise à jour partout en salle / position GPS affichée avec quelques secondes de retard |
| Monolithe modulaire | Une seule unité de déploiement organisée en modules à frontières claires, sans appel réseau interne. | `// src/modules/{reservation,facturation,notif}/ dans un seul déploiement` | une seule cuisine avec des postes bien séparés, pas trois restaurants distincts / un seul bateau avec des cabines dédiées, pas trois navires |
| Critères monolithe vs services | Autonomie organisationnelle, isolation de charge, isolation de risque, maturité opérationnelle. | `// 0 critere vrai --> rester en monolithe modulaire` | ouvrir une cuisine séparée seulement si une équipe distincte la gère vraiment / affréter un second bateau seulement si la charge le justifie vraiment |

## Défense orale

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Couplage | Un changement isolé se transforme en chantier de trois jours ailleurs | Peux-tu citer le type de couplage entre deux modules précis de ton système, et pourquoi ce type-là ? |
| Cohésion | Un module fourre-tout devient impossible à décrire en une phrase, personne n'ose plus y toucher seul | Peux-tu décrire ce module en une phrase sans "et" ? |
| Dépendance dirigée | Un cycle de dépendances rend impossible de déployer, tester ou remplacer une partie sans l'autre | Peux-tu dessiner le graphe de dépendances de ton système sans trouver de cycle ? |
| Inversion de dépendance | Le domaine se retrouve à connaître HTTP, SQL ou un framework, impossible à tester sans les démarrer | Ton domaine peut-il se tester sans base de données ni serveur démarré ? |
| Source de vérité | Deux copies divergent, personne ne sait laquelle croire, une décision se prend sur la mauvaise | Pour une donnée dupliquée dans ton système, sais-tu nommer sa source de vérité ? |
| Cohérence éventuelle | Une décision irréversible se prend sur une copie périmée, sans que personne ne l'ait choisi | Quelle décision irréversible pourrait s'appuyer, par erreur, sur une copie non à jour ? |
| Monolithe modulaire | Le système se fragmente en services avant que l'équipe en ait besoin, coût réseau payé pour rien | Pourquoi choisir un monolithe modulaire plutôt que des microservices ici, avec quels critères ? |
| Critères monolithe vs services | On découpe en services par mode plutôt que par besoin, coût réseau sans bénéfice réel | Lequel des quatre critères est vrai dans ton contexte actuel, et lequel ne l'est pas ? |

Grille détaillée : voir [boss-fight.md](./boss-fight.md).

## Le gradient de couplage (du pire au meilleur)

```text
Contenu > Commun > Controle > Donnee > Message
(le pire)                              (le mieux, realiste = donnee le plus souvent)
```

## Les 4 couches et leur regle de dependance

```text
UI --> Cas d'usage --> Domaine <-- Infra (implemente une interface du Domaine)
```

Le Domaine ne connait ni HTTP, ni SQL, ni JSON, ni aucun framework.

## Checklist avant de committer un module

- [ ] Je peux decrire ce module en une phrase sans "et".
- [ ] Si je le supprime et le remplace par une autre implementation du meme contrat, je sais
      exactement combien de fichiers je dois toucher ailleurs (idealement zero).
- [ ] Aucun import ne remonte d'une couche "basse" (infra) vers une couche "haute" (domaine,
      cas d'usage, UI).
- [ ] La regle metier qu'il porte se teste sans base de donnees ni serveur demarre.
- [ ] Je sais nommer sa source de verite si ce module manipule une donnee dupliquee ailleurs.

## Les 4 criteres de decision monolithe vs services

1. Autonomie organisationnelle reelle entre equipes.
2. Isolation de charge/scalabilite radicalement differente.
3. Isolation de risque ou de conformite.
4. Maturite operationnelle de l'equipe pour absorber le cout reseau/deploiement.

Zero critere vrai --> monolithe modulaire, sans hesitation.

## Heuristique de secours

Quand tu doutes du decoupage : demande-toi "quand cette chose change, qu'est-ce que je suis
oblige de changer avec ?" Si la reponse est "beaucoup de choses sans rapport apparent",
le decoupage est mauvais, independamment de ce que dit le nom des dossiers.

## Si tu rates le boss-fight

Relis d'abord le critere qui a plafonne ta note : troisieme voie realisable, reponse aux
microservices, ou mecanisme anti-recidive. Redessine le graphe de dependances de la scene
avant de repondre a nouveau, en identifiant precisement le couplage fautif. Relis le gradient
de couplage ci-dessus. Attends 48 h avant de retenter le boss-fight pour juger la scene a
froid. Si l'echec se reproduit sur le meme critere, redescends au niveau 05 relire
"invariant" : un mauvais decoupage cache souvent un invariant mal place.
