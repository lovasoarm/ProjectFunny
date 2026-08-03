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
- La leçon sur l'IA comme outil (`14-TOOL-CAVE/03-ai-as-a-tool.md`) : c'est la plus volatile.
- Les seuils de performance cités : ils suivent le matériel.

## Marqueur de version

Chaque fichier volatil commence par une ligne :

```text
<!-- volatile: revoir avant AAAA-MM -->
```

Un fichier sans ce marqueur est réputé stable. Si tu en trouves un qui vieillit, ajoute le marqueur
au lieu de supprimer le contenu.
