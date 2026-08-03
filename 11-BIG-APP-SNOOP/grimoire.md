# Grimoire : Big App Snoop

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| Cartographie du terrain | Repérer la structure d'un dépôt (arborescence, dépendances, schéma DB) sans lire la logique métier | Tu lis fichier par fichier sans savoir si tu remontes vers la cause ou t'enfonces dans une branche morte | Pourquoi cette phase précède toute lecture de code et combien de temps tu lui donnes sur un budget de 3 heures |
| Flux de bout en bout | Suivre un cas d'usage réel du clic jusqu'à l'écriture en base, sans dévier | Tu comprends des fragments isolés sans jamais voir la forme réelle d'un parcours utilisateur | Pourquoi une traversée verticale unique t'apprend plus qu'une lecture exhaustive d'un dossier |
| Zone à risque | Fichier ou fonction à fort impact ou fort historique de modification (git log --stat, absence de tests) | Tu traites tout le code avec la même prudence, trop pour le cosmétique, pas assez pour le critique | Deux indices concrets, autres que ton intuition, pour repérer une zone à risque |
| Contrainte reconstruite | Raison externe (légale, contractuelle, performance, équipe) qui explique un design en apparence mauvais | Tu juges et tu réécris du code qui répond en fait à un besoin réel encore actif | Les quatre familles de contraintes et la question qui décide si chacune est encore active |
| Dette délibérée vs dette subie | Distinction entre un compromis choisi consciemment et une dégradation non maîtrisée | Tu traites toute imperfection comme une faute, tu perds la confiance de l'équipe en place | Un exemple de chacune, avec l'indice qui te permet de les distinguer dans le code |
| Rayon d'impact | Ensemble des appelants, tests et données qui dépendent de ce que tu modifies | Tu corriges une fonction partagée sans savoir qu'elle est partagée, et un service sans lien apparent casse | Les cinq branches du rayon d'impact et laquelle casse en silence plutôt que bruyamment |
| Test de caractérisation | Test qui documente le comportement actuel du code, bug inclus, avant toute modification | Tu ne sais pas mesurer l'écart exact que ton correctif introduit, tu changes à l'aveugle | En quoi il diffère d'un test qui vérifie qu'un comportement est correct |
| Patch minimal | Le plus petit changement qui corrige le problème réel sans nettoyage ni renommage mêlés | La revue de code se noie dans du bruit, le risque de régression grimpe sans bénéfice mesuré | Les trois qualités d'un patch minimal et pourquoi le nettoyage mérite sa propre PR |
| Non-régression | Preuve que ce qui marchait avant ton patch marche toujours après, distincte de la preuve que ton correctif fonctionne | Tu livres un correctif qui fonctionne sur son cas et casse un cas déjà géré ailleurs | La différence entre preuve positive et preuve négative de non-régression |
| Données déjà écrites | Lignes en base ou fichiers créés sous l'ancien comportement, non concernés automatiquement par ton patch | Un rapport ou un calcul redevient incohérent uniquement sur les enregistrements antérieurs à ton déploiement | Pourquoi ce point est l'angle mort le plus fréquent d'une modification en apparence propre |

## Comportements évalués en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'échec |
| --- | --- | --- |
| Justification par un mécanisme, pas par une impression | Méthode d'archéologie explicite (commandes, sources) utilisée avant toute conclusion sur la qualité du code | Un jugement du type "c'est mal fait" apparaît avant la section de recommandation finale |
| Compromis nommé et assumé | Hypothèses de contrainte formulées comme vérifiables, avec le moyen exact de les confirmer ou de les infirmer | Une hypothèse présentée comme une certitude définitive sans moyen de vérification proposé |
| Honnêteté sur ce que tu ne sais pas | Traitement explicite du cas où la preuve (ticket, commit) n'existe plus : contrainte traitée comme active par défaut, protégée par un test | Conclure d'une absence de preuve à une absence de besoin, ou inventer une certitude non vérifiable |

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
