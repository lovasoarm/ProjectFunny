# Grimoire — Big App Snoop

## La méthode en une page

```text
1. Cartographie (30 min)     → README, arborescence, dépendances, schéma DB
2. Flux de bout en bout       → suis UN cas d'usage réel du clic jusqu'à la base
3. Zones à risque             → fichiers chauds (git log --stat), fonctions longues,
                                 absence de tests
4. Historique ciblé           → git log -p sur la zone, tickets liés, mots-clés du domaine
5. Synthèse écrite            → carte + flux + risques + inconnues + estimation en fourchette
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
```

## Les 4 familles de contraintes cachées

```text
Légale / réglementaire  → dates en dur, versions parallèles jamais fusionnées, délais courts
Contractuelle / client  → identifiants clients en dur, branches conditionnelles nommées
Performance / charge    → dénormalisation, caches, champs dupliqués, commentaires d'incident
Équipe / historique     → duplication entre zones jamais synchronisées, code "v2" abandonné
```

## Les 4 questions de reconstruction

```text
1. Quand a-t-il été écrit ?
2. Quel problème résolvait-il alors (pas aujourd'hui) ?
3. Quelle contrainte externe l'a façonné ?
4. Cette contrainte existe-t-elle encore ?
   → Active     : garder, isoler proprement
   → Disparue   : dette réelle, preuve écrite avant suppression
   → Inconnue   : traiter comme actif, protéger par un test avant de toucher
```

## Checklist avant de proposer une suppression ou une réécriture

```text
[ ] J'ai cherché le commit d'origine du code visé.
[ ] J'ai cherché les tickets ou PR liés au message de commit.
[ ] J'ai vérifié si un client ou un cas identifié en dur est encore actif.
[ ] J'ai écrit un test de caractérisation si je ne suis pas sûr du comportement actuel.
[ ] J'ai posé la question à quelqu'un de l'équipe en place, si disponible, avant de conclure.
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
45 min  zones à risque
45 min  historique ciblé
15 min  synthèse écrite
```
