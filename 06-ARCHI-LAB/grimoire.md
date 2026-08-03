# Grimoire : Archi-Lab

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| Couplage | A quel point un module doit connaitre l'interieur d'un autre pour fonctionner. Plus c'est fort, plus un changement interne se propage en cassures. | Un changement isole se transforme en chantier de trois jours ailleurs. | Peux-tu citer le type de couplage entre deux modules precis de ton systeme, et pourquoi ce type-la ? |
| Cohesion | A quel point les elements d'un module ont une raison commune d'etre ensemble (une seule raison de changer par module = forte cohesion). | Un module fourre-tout devient impossible a decrire en une phrase, et personne n'ose plus y toucher seul. | Peux-tu decrire ce module en une phrase sans "et" ? |
| Dependance dirigee | Une fleche "A depend de B" qui doit toujours pouvoir se dessiner sans jamais former de cycle dans le graphe global du systeme. | Un cycle de dependances rend impossible de deployer, tester ou remplacer une partie sans l'autre. | Peux-tu dessiner le graphe de dependances de ton systeme sans trouver de cycle ? |
| Inversion de dependance | Le domaine definit une interface, l'infra l'implemente : la fleche de dependance va de l'infra vers le domaine, pas l'inverse. | Le domaine se retrouve a connaitre HTTP, SQL ou un framework, et devient impossible a tester sans les demarrer. | Ton domaine peut-il se tester sans base de donnees ni serveur demarre ? |
| Source de verite | La copie d'une donnee designee comme faisant foi en cas de desaccord ; tout le reste est un derive reconstructible. | Deux copies divergent et personne ne sait laquelle croire, une decision se prend sur la mauvaise. | Pour une donnee dupliquee dans ton systeme, sais-tu nommer sa source de verite ? |
| Coherence eventuelle | Un decalage temporaire et assume entre une copie et sa source de verite, acceptable uniquement si aucune decision irreversible ne s'appuie dessus. | Une decision irreversible se prend sur une copie perimee, sans que personne ne l'ait choisi. | Quelle decision irreversible pourrait s'appuyer, par erreur, sur une copie non a jour ? |
| Monolithe modulaire | Une seule unite de deploiement, organisee en modules internes a frontieres claires, sans appel reseau entre les parties. | Le systeme se fragmente en services avant que l'equipe en ait besoin, et paie un cout reseau et operationnel pour rien. | Pourquoi choisir un monolithe modulaire plutot que des microservices ici, avec quels criteres ? |
| Les 4 criteres de decision monolithe vs services | Autonomie organisationnelle, isolation de charge, isolation de risque/conformite, maturite operationnelle : zero critere vrai revient a garder un monolithe modulaire. | On decoupe en services par mode plutot que par besoin, et on paie le cout reseau sans aucun benefice reel. | Lequel des quatre criteres est vrai dans ton contexte actuel, et lequel ne l'est pas ? |

## Comportements evalues en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'echec |
| --- | --- | --- |
| Refus argumente des deux extremes | La justification s'appuie sur un mecanisme concret (couplage, cout reel), pas sur une preference esthetique ou une peur du changement | Tu rejettes une option sans expliquer par quel mecanisme elle echoue |
| Troisieme voie realisable | La proposition tient dans les trois jours et cible precisement le couplage qui a cause le probleme initial, pas un refactoring generique | Tu proposes un refactoring vague ou disproportionne au delai |
| Reponse aux microservices | L'argument utilise au moins un des quatre criteres de decision du niveau, applique avec des faits du cas (une seule equipe, pas de contrainte de conformite isolee) | Tu repousses les microservices avec un simple "on n'a pas le temps" |
| Mecanisme anti-recidive | L'engagement propose est verifiable (fait partie d'une definition de "termine", d'un critere de revue de code), pas un voeu pieux | Tu promets un "nettoyage plus tard" sans mecanisme qui l'oblige |
| Ton | La decision est assumee et defendable devant le CTO et le developpeur senior en meme temps, sans chercher a plaire aux deux a la fois par du flou | Ta reponse reste evasive pour eviter de trancher clairement |

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
