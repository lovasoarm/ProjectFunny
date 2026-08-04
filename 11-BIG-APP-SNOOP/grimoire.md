# Grimoire : Big App Snoop

Ouvre ce mémo avant de toucher à du code étranger dans un gros dépôt. Il te donne la méthode
d'archéologie, pas un cours sur la lecture de code.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Cartographie du terrain | Repérer la structure d'un dépôt (arborescence, dépendances, schéma DB) sans lire la logique métier. | `find . -maxdepth 2 -type d && cat package.json | jq '.dependencies'` | course en montagne / navigation maritime |
| Flux de bout en bout | Suivre un cas d'usage réel du clic jusqu'à l'écriture en base, sans dévier. | `rg -n "createTournee" --type ts` | urgences d'hôpital / atelier de menuiserie |
| Zone à risque | Fichier ou fonction à fort impact ou fort historique de modification. | `git log --pretty=format: --name-only | sort | uniq -c | sort -rg | head -10` | régie technique de spectacle / course en montagne |
| Contrainte reconstruite | Raison externe (légale, contractuelle, performance, équipe) qui explique un design en apparence mauvais. | `git log --follow -p -- chargeSplitter.ts | head -80` | atelier de menuiserie / cuisine de restaurant en service |
| Dette délibérée vs dette subie | Distinction entre un compromis choisi consciemment et une dégradation non maîtrisée. | `git log --all --grep="TODO temporaire" -i --oneline` | navigation maritime / urgences d'hôpital |
| Rayon d'impact | Ensemble des appelants, tests et données qui dépendent de ce que tu modifies. | `rg -n "from ['\"].*slotWindow" --type ts` | régie technique de spectacle / course en montagne |
| Test de caractérisation | Test qui documente le comportement actuel du code, bug inclus, avant toute modification. | `it("caracterise le comportement actuel", () => { expect(splitHeatingCost(oldBuilding, readings)).toEqual(snapshotActuel); })` | atelier de menuiserie / urgences d'hôpital |
| Patch minimal | Le plus petit changement qui corrige le problème réel, sans nettoyage ni renommage mêlés. | `git diff --stat # verifie que le diff ne touche que la ligne du bug` | cuisine de restaurant en service / navigation maritime |
| Non-régression | Preuve que ce qui marchait avant ton patch marche toujours après. | `npm test -- --run tests/chargeSplitter.spec.ts` | urgences d'hôpital / course en montagne |
| Données déjà écrites | Lignes en base ou fichiers créés sous l'ancien comportement, non concernées automatiquement par ton patch. | `SELECT count(*) FROM allocations WHERE created_at < '2026-01-01';` | atelier de menuiserie / régie technique de spectacle |

## Défense orale

Pour la grille complète et chiffrée, va voir [./boss-fight.md](./boss-fight.md). Voici la matière
reformulée pour t'entraîner à l'oral.

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Enquêter avant de juger | Un jugement esthétique prononcé avant l'enquête te fait rater une contrainte réelle et invisible au premier coup d'oeil. | Quelle méthode d'archéologie (commandes précises) suis-tu avant de conclure sur la qualité d'un code ? |
| Formuler des hypothèses vérifiables | Présenter une supposition comme une certitude définitive empêche quiconque de la challenger plus tard. | Comment formules-tu une hypothèse de contrainte de façon à pouvoir la confirmer ou l'infirmer ? |
| Assumer l'absence de preuve | Conclure d'une absence de preuve à une absence de besoin est le piège classique de ce niveau. | Que fais-tu quand la trace d'une contrainte (ticket, commit) a disparu pour de bon ? |

## La méthode en une page

```text
1. Cartographie (30 min)     -> README, arborescence, dependances, schema DB
2. Flux de bout en bout       -> suis UN cas d'usage reel du clic jusqu'a la base
3. Zones a risque             -> fichiers chauds (git log --stat), fonctions longues,
                                  absence de tests
4. Historique cible           -> git log -p sur la zone, tickets lies, mots-cles du domaine
5. Synthese ecrite            -> carte + flux + risques + inconnues + estimation en fourchette
6. Rayon d'impact avant patch -> appelants, tests existants, donnees deja ecrites
7. Patch minimal + non-regression -> plus petit diff possible, preuve avant/apres
```

## Commandes utiles

```bash
# Fichiers les plus modifiés (points chauds)
git log --pretty=format: --name-only | sort | uniq -c | sort -rg | head -30

# Historique complet d'un fichier, y compris renommages
git log --follow -p -- chemin/du/fichier.ts

# Chercher un mot-clé métier dans les commits
git log --all --grep="liste d'attente" -i

# Date de création d'un fichier
git log --diff-filter=A --follow --format="%ad" -- chemin/du/fichier.ts | tail -1

# Compter les occurrences d'un terme dans le code (repérer l'ampleur d'un concept)
rg -c "creneau" --stats

# Trouver tous les appelants directs d'une fonction avant de la modifier
rg -n "slotWindow" --type ts
rg -n "from ['\"].*slotWindow" --type ts
```

## Les 4 familles de contraintes cachées

```text
Legale / reglementaire  -> dates en dur, versions paralleles jamais fusionnees, delais courts
Contractuelle / client  -> identifiants clients en dur, branches conditionnelles nommees
Performance / charge    -> denormalisation, caches, champs dupliques, commentaires d'incident
Equipe / historique     -> duplication entre zones jamais synchronisees, code "v2" abandonne
```

## Les 4 questions de reconstruction

```text
1. Quand a-t-il ete ecrit ?
2. Quel probleme resolvait-il alors (pas aujourd'hui) ?
3. Quelle contrainte externe l'a faconnee ?
4. Cette contrainte existe-t-elle encore ?
   -> Active     : garder, isoler proprement
   -> Disparue   : dette reelle, preuve ecrite avant suppression
   -> Inconnue   : traiter comme actif, proteger par un test avant de toucher
```

## Les 5 branches du rayon d'impact

```text
1. Appelants directs      -> cassent tout de suite, bruyamment
2. Appelants indirects     -> cassent via une chaine d'appels, plus dur a tracer
3. Tests existants         -> verrouillent (bien ou mal) un comportement attendu
4. Donnees deja ecrites    -> cassent en silence, decouvertes des semaines plus tard
5. Consommateurs externes  -> API publique, export, job planifie, rapport
```

## Checklist avant de proposer une suppression ou une réécriture

```text
[ ] J'ai cherché le commit d'origine du code visé.
[ ] J'ai cherché les tickets ou PR liés au message de commit.
[ ] J'ai vérifié si un client ou un cas identifié en dur est encore actif.
[ ] J'ai écrit un test de caractérisation si je ne suis pas sûr du comportement actuel.
[ ] J'ai posé la question à quelqu'un de l'équipe en place, si disponible, avant de conclure.
```

## Checklist avant de livrer une modification sur du code étranger

```text
[ ] J'ai listé tous les appelants directs trouvés par une recherche exécutée, pas devinée.
[ ] J'ai vérifié ce que chaque appelant attend, pas seulement qu'il existe.
[ ] J'ai un test de caractérisation du comportement actuel avant de le changer.
[ ] Mon patch ne mélange pas correction et nettoyage.
[ ] J'ai vérifié si des données déjà écrites deviennent incohérentes avec mon changement.
[ ] J'ai fait tourner la suite de tests de la zone touchée avant et après mon patch.
```

## Phrases à bannir en réunion tant que tu n'as pas vérifié

- "C'est clairement du code mort."
- "Ça n'a aucun sens, on peut le supprimer."
- "C'est juste mal fait."

## Phrases qui remplacent les précédentes

- "Je n'ai pas encore trouvé pourquoi ce code existe, je vérifie avant de proposer quoi que
  ce soit."
- "Ce design semble répondre à [contrainte hypothèse], je le confirme avec [action précise]."
- "Voici ce que j'ai vérifié, voici ce qui reste une hypothèse."

## Budget de temps type pour une enquête de 3 heures

```text
30 min  cartographie
45 min  flux de bout en bout
45 min  zones a risque
45 min  historique cible
15 min  synthese ecrite
```

## Si tu rates le boss-fight

Relis la leçon sur la reconstruction de contraintes et celle sur le rayon d'impact avant de
retenter. Refais l'exercice en écrivant d'abord les quatre questions de reconstruction pour
chaque branche de code suspecte, avant toute recommandation. Donne-toi 48 heures, pas plus.
Si le score reste sous 78/100, ou si le traitement de l'absence de preuve reste sous 20/25,
remonte au niveau amont sur la distinction entre hypothèse et certitude avant de revenir ici.
