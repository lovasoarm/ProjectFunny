# ANNEXE : pérennité : pourquoi ce curriculum ne périme pas

Les outils changent tous les trois ans. Les modes d'échec des projets, non. Ce curriculum est
construit sur les seconds.

## Ce qui périme et ce qui reste

```text
PERIME VITE                          RESTE 20 ANS
-------------------------------      ---------------------------------
la syntaxe d'un framework            le couplage et la cohésion
l'ORM du moment                      le coût d'un changement de schéma
le format de config du CI            l'idée de garde-fou automatique
le fournisseur de cloud              la latence, la panne partielle, le coût
la mode micro-services               le critère de découpage d'un système
l'outil de ticket                    le fait qu'un travail non visible n'est pas suivi
```

Chaque leçon est écrite pour que l'exemple soit remplaçable et le mécanisme, non.

## Pérennité niveau par niveau

```text
05 DATA-SPELLS
  intemporel : normalisation, invariants métier, migration expand/contract
  périssable : la version exacte de Postgres citée en exemple (16) ; revoir si elle
               atteint sa fin de support

07 API-DOJO
  intemporel : idempotence, contrats, versionnage, politique d'erreurs
  périssable : la mécanique HTTP précise si un nouveau standard venait remplacer REST
               comme norme dominante

09 QUALITY-SHIELD
  intemporel : stratégie de tests par coût, observabilité comme signal avant l'utilisateur
  périssable : les formats de logs et de traces cités en exemple

11 BIG-APP-SNOOP
  intemporel : la méthode de cartographie en cinq phases
  périssable : les trois dépôts réels cités (plausible/analytics, go-gitea/gitea,
               calcom/cal.com), leur taille exacte et leur structure interne, qui évoluent
               à chaque release

14 TOOL-CAVE
  intemporel : la méthode de reproduction déterministe, le format HYPOTHESES.md
  périssable : 04-ai-as-a-tool.md dans son intégralité, à revoir à chaque saut de
               génération de modèle d'IA
```

## Comment maintenir ce dépôt

1. **Les leçons ne citent un outil que comme illustration.** Si une leçon devient fausse parce
   qu'un outil a changé de version, c'est un défaut de la leçon : réécris l'exemple, pas le principe.
2. **Chaque exemple de code doit tenir sans dépendance exotique.** TypeScript et SQL standard.
3. **Règle des cinq ans.** Avant d'ajouter un contenu, demande : sera-t-il encore vrai dans cinq
   ans ? Si non, il va dans `15-BONUS-VAULT/` avec une date de péremption écrite en tête de fichier.
4. **Revue annuelle.** Une fois par an, relis les études de cas du niveau 11 : ce sont elles qui
   vieillissent le plus vite, car elles décrivent des contextes techniques.

## Ce qui doit être révisé quand l'écosystème bouge

- Les exemples d'observabilité (formats de logs, conventions de traces).
- La leçon sur l'IA comme outil (`14-TOOL-CAVE/04-ai-as-a-tool.md`) : c'est la plus volatile.
- Les seuils de performance cités : ils suivent le matériel.

## Marqueur de version

Chaque exemple de code volatil (version d'outil, seuil de performance) porte, dans le
commentaire du bloc de code lui-même, la mention :

```text
# verifie le AAAA-MM-JJ
```

au format déjà en usage dans `05-DATA-SPELLS/grimoire.md` et `09-QUALITY-SHIELD/grimoire.md`.
Un exemple de code sans cette mention est réputé stable. Si tu en trouves un qui vieillit,
ajoute la mention au lieu de supprimer le contenu.
