# Boss Fight : Le code moche avait une raison

## La situation

Tu rejoins l'équipe qui maintient le logiciel de refacturation d'énergie d'un syndic
d'immeubles (répartition des charges de chauffage collectif entre copropriétaires selon des
relevés de compteurs). Le dépôt fait 120 000 lignes, huit ans d'historique, quatre
générations de développeurs. En parcourant `chargeSplitter.ts`, tu tombes sur ceci :

```ts
function splitHeatingCost(building: Building, readings: Reading[]): Allocation[] {
  // NE PAS SIMPLIFIER. Voir ticket SUPPORT-4471.
  if (building.constructionYear < 1975) {
    return splitByLegacyCoefficient(building, readings);
  }
  if (building.hasIndividualMeters === false) {
    return splitBySurfaceOnly(building, readings);
  }
  return splitByConsumption(building, readings);
}
```

Trois branches, un commentaire agressif sans explication, un numéro de ticket que tu n'as
pas accès à l'outil de support pour consulter (l'équipe a changé d'outil de ticketing deux
fois depuis). Ton premier réflexe est de fusionner les deux premières branches, qui te
semblent redondantes. Ne le fais pas avant d'avoir lu la suite de cette page.

## Ta mission

Avant de juger, avant de proposer quoi que ce soit, tu dois mener une enquête d'archéologie
de code et écrire un rapport qui répond, dans l'ordre, aux quatre questions suivantes,
chacune appuyée sur une preuve concrète que tu décrirais aller chercher (commit, commentaire,
structure de données, norme du secteur) :

1. **Quand chacune des trois branches a-t-elle été introduite ?** Décris comment tu le
   déterminerais (quelle commande, sur quel fichier) et ce que la chronologie relative des
   trois branches (laquelle est arrivée en premier) te dit déjà avant même de lire le contenu
   des commits.
2. **Quelle contrainte disparue ou toujours active explique le seuil "1975" ?** Ce nombre
   n'est presque certainement pas arbitraire dans le domaine de l'énergie et du bâtiment en
   France. Formule une hypothèse vérifiable et le moyen de la confirmer sans deviner au
   hasard.
3. **Que signifie concrètement `hasIndividualMeters === false`, et quelle obligation légale
   ou contractuelle peut forcer un mode de répartition différent quand il n'y a pas de
   compteur individuel ?** Encore une fois : formule une hypothèse et un moyen de la vérifier,
   pas une affirmation en l'air.
4. **Le ticket SUPPORT-4471 est-il perdu pour de bon, et si oui, qu'est-ce que ça change à
   ta décision ?** Décris ce que tu ferais si aucune trace de ce ticket n'existe plus nulle
   part : comment tu traites une contrainte "inconnaissable" au sens de
   [03-reverse-engineer-decisions.md](03-reverse-engineer-decisions.md).

Ton rapport final doit se terminer par une recommandation explicite : tu gardes le code tel
quel, tu le gardes mais tu le documentes, ou tu proposes un changement précis avec le test de
caractérisation qui le protège. Chaque option est valable si elle est justifiée par ton
enquête, aucune n'est valable si elle repose sur un jugement esthétique du code.

## Ce que tu dois montrer, concrètement

- Une méthode d'archéologie explicite : quelles commandes, dans quel ordre, pour reconstruire
  la date et le contexte de chaque branche du code.
- Une hypothèse de contrainte pour le seuil "1975" et pour l'absence de compteurs
  individuels, chacune formulée comme une affirmation vérifiable, pas comme une certitude.
- Un traitement explicite du cas où la preuve n'existe plus (ticket disparu) : tu ne dois
  jamais conclure "donc ce n'est plus nécessaire" à partir d'une absence de preuve.
- Une recommandation finale cohérente avec ce que ton enquête a réellement trouvé, pas avec
  ce que tu aurais préféré trouver.

## Grille d'évaluation

| Critère                                                  | Points | Ce qui est vérifié                                                                                                                                     |
| ---------------------------------------------------------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Méthode d'archéologie avant jugement                     | 20     | Tu décris une séquence d'enquête concrète (commandes, sources) avant toute conclusion sur la qualité du code, dans l'ordre chronologie puis contexte     |
| Hypothèses de contrainte formulées comme vérifiables      | 25     | Chaque hypothèse (seuil 1975, absence de compteurs) est énoncée avec le moyen exact de la confirmer ou de l'infirmer, pas comme une affirmation définitive |
| Traitement correct de l'absence de preuve                 | 25     | Face au ticket disparu, tu traites la contrainte comme active par défaut et proposes un test de caractérisation, sans jamais conclure d'une absence de preuve à une absence de besoin |
| Recommandation finale cohérente avec l'enquête            | 20     | Ta décision finale (garder, documenter, ou changer avec filet) découle logiquement de ce que ton enquête a réellement établi, pas d'un jugement esthétique initial |
| Absence de jugement de qualité prématuré                  | 10     | Aucune phrase du type "c'est mal fait" ou "il faut réécrire" n'apparaît avant la section de recommandation finale                                          |

Score minimal pour valider le niveau : 78/100, avec au moins 20/25 sur le critère de
traitement de l'absence de preuve : c'est le cœur du boss-fight, la tentation de conclure vite
face à une contrainte qu'on ne peut plus vérifier.

## Ce qui fait perdre automatiquement, quel que soit le score

- Proposer de fusionner ou simplifier le code avant la section de recommandation finale.
- Affirmer que le seuil "1975" ou l'absence de ticket "prouve" que la contrainte a disparu :
  une absence de preuve n'est jamais une preuve d'absence.
- Ne mentionner aucune méthode concrète pour dater les branches (uniquement des suppositions
  sur le contenu, sans jamais évoquer l'historique git ou une autre source vérifiable).
