# Progression réelle, seniorité, choix de projets, réputation

## Le piège

Deux développeurs ont chacun six ans d'expérience. Le premier a passé six ans sur le même
produit de gestion de stock d'une chaîne de pharmacies, en changeant rarement de
responsabilité. Le second a changé trois fois de contexte : deux ans sur un produit de
paiement à fort trafic, deux ans sur un outil interne qu'il a dû concevoir de zéro, deux ans
en tant que référent technique d'une petite équipe. Sur le papier, "6 ans d'expérience" les
place au même niveau. Face à un problème inédit en entretien, l'écart de jugement entre les
deux est immense : pas parce que le premier est moins intelligent, mais parce que la
seniorité ne se mesure pas en années, elle se mesure en diversité de situations
correctement digérées et en niveau de responsabilité réellement exercé sur des décisions
qui comptaient.

## Ce qui se passe vraiment

### Ce que "senior" veut dire, au-delà du titre

Le titre "senior" est attribué de façon très inégale d'une entreprise à l'autre : dans
certaines, il arrive après 18 mois, dans d'autres après 8 ans. Le titre n'est donc pas un
signal fiable. Ce qui l'est, ce sont des capacités observables, indépendantes de qui te les
a accordées :

```text
Junior                    Confirmé                     Senior
───────                   ─────────                    ───────
Exécute une tâche         Découpe un problème en        Choisit QUEL problème
bien définie               tâches bien définies          mérite d'être résolu
                                                          maintenant

A besoin qu'on lui         Anticipe les cas limites      Anticipe les conséquences
signale les cas limites    évidents seul                 organisationnelles d'un
                                                          choix technique

Demande "comment           Sait comment faire, sait      Sait dire non à une demande
faire" pour une tâche      dire quand une tâche va       mal posée, et proposer une
ambiguë                     coûter plus cher que prévu    meilleure question
```

Le saut le plus dur, presque toujours sous-estimé, est celui de "confirmé" à "senior" : ce
n'est pas un saut de compétence technique pure (le confirmé code souvent aussi bien), c'est
un saut de **responsabilité sur les choix**, y compris le choix de refuser ou reformuler une
demande.

### Choisir ses projets par ce qu'ils enseignent

À compétence égale, deux offres de projet ou d'emploi ne se valent pas. Le critère de
sélection le plus utile à long terme n'est ni le salaire seul, ni la marque de l'entreprise
— c'est la réponse honnête à trois questions :

1. Ce projet me confrontera-t-il à un type de problème que je n'ai jamais résolu (échelle,
   contrainte réglementaire, coordination d'équipe, domaine métier inconnu) ?
2. Aurai-je une vraie responsabilité de décision dessus, ou serai-je un exécutant d'une
   décision déjà prise par quelqu'un d'autre ?
3. Existe-t-il, dans ce contexte, quelqu'un de plus expérimenté que moi sur au moins un axe,
   dont je pourrai observer le jugement de près ?

Un projet qui répond "non" aux trois questions peut rester un bon choix ponctuel (stabilité
financière, période personnelle chargée), mais ne doit pas devenir la norme sur plusieurs
années consécutives, sous peine de plateau silencieux (Niveau 13, leçon 01).

### La réputation technique : preuve plutôt que déclaration

Une réputation technique se construit sur des artefacts vérifiables, pas sur des
affirmations. La différence est cruciale en entretien, en revue de promotion, ou simplement
dans la confiance qu'une équipe t'accorde spontanément.

```text
Réputation déclarative (fragile)         Réputation par preuve (solide)
─────────────────────────────────         ──────────────────────────────
"Je maîtrise l'architecture"              Un ADR que tu as écrit, avec un
                                            compromis assumé et son résultat réel
"Je suis bon en debug"                    Un post-mortem d'incident que tu as
                                            mené, avec la cause racine trouvée
"Je code proprement"                      Une pull request ouverte, publique,
                                            avec des tests et une justification
"J'ai de l'expérience en systèmes         Un article ou une explication écrite
distribués"                                d'un problème réel que tu as résolu
```

Un portfolio de preuves ne demande pas de travail supplémentaire majeur si tu documentes ce
que tu fais déjà (ADR du Niveau 01, post-mortems du Niveau 09) au lieu de le jeter après
usage. La discipline n'est pas d'en produire plus, c'est de ne pas les perdre.

### Le vrai coût de l'immobilité de confort

Rester longtemps au même poste n'est pas une erreur en soi : l'expertise verticale profonde
demande du temps et de la continuité (Niveau 13, leçon 01, compromis). L'erreur est de le
faire par défaut, sans avoir vérifié régulièrement, via une checklist honnête, que ce poste
continue de produire de la croissance et pas seulement du confort :

```text
Checklist annuelle de vérité (à se poser seul, honnêtement, une fois par an) :

[ ] Ai-je pris, cette année, au moins une décision technique dont je n'étais pas
    sûr d'avance, avec un vrai enjeu derrière ?
[ ] Existe-t-il, dans mon environnement actuel, une personne dont j'apprends encore
    quelque chose régulièrement ?
[ ] Pourrais-je citer une compétence nouvelle acquise cette année, au-delà de la
    familiarité accrue avec mon contexte actuel ?
[ ] Si je devais changer d'entreprise demain, mon "portfolio de preuves" a-t-il
    grandi cette année, ou est-il identique à l'an dernier ?

3 ou 4 "non" → signal de plateau à traiter, pas à ignorer une année de plus.
```

## Compromis

| Option                                              | Coût                                                | Bénéfice                                                             | Quand choisir                                                                                     |
| --------------------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| Rester longtemps sur un même produit/équipe         | Risque de plateau sans discipline de vérification   | Expertise verticale profonde, confiance d'équipe élevée              | Une fois qu'une base large de compétences existe déjà, et si la checklist annuelle reste positive |
| Changer régulièrement de contexte                   | Coût de ramp-up, perte de continuité relationnelle  | Large exposition, détection rapide des angles morts                  | Début à milieu de carrière, ou après un plateau confirmé                                          |
| Accepter un projet moins bien payé mais formateur   | Coût financier direct, parfois coût de statut perçu | Compétence rare acquise, meilleure négociation future                | Quand l'écart de formation est net et le coût financier absorbable                                |
| Documenter systématiquement son travail (portfolio) | Temps de rédaction, discipline continue             | Réputation vérifiable, négociation facilitée, mémoire externe fiable | Toujours, dès la première décision qui en vaut la peine                                           |

## Pièges classiques

- **Le titre pris pour la compétence.** Symptôme : tu te sens "senior" parce que ton
  entreprise t'a donné le titre, sans pouvoir citer une décision récente où tu as assumé
  seul un vrai risque.
- **Le choix de projet uniquement par le salaire.** Symptôme : tu acceptes systématiquement
  l'offre la mieux payée, tu te retrouves après quelques années avec un salaire correct et
  un éventail de compétences étroit, difficile à valoriser ailleurs.
- **La réputation qui repose uniquement sur la mémoire des autres.** Symptôme : tu comptes
  sur le bouche-à-oreille de collègues qui, eux aussi, oublient les détails avec le temps —
  sans artefact écrit, la preuve s'évapore avec les départs d'équipe.
- **L'immobilité justifiée a posteriori.** Symptôme : tu restes cinq ans au même poste "parce
  que c'est confortable" et tu rationalises ce choix en "expertise" sans jamais avoir vérifié
  honnêtement, via la checklist, si la croissance était toujours là.

## Ce que tu dois savoir défendre

- Explique la différence entre "confirmé" et "senior" en termes de responsabilité sur les
  décisions, pas seulement de compétence technique.
- Cite trois critères pour choisir un projet par ce qu'il enseigne, au-delà du salaire et de
  la marque de l'entreprise.
- Donne un exemple d'artefact que tu pourrais transformer, dès aujourd'hui, en preuve
  vérifiable de ta réputation technique.
