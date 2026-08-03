# Pourquoi ce niveau existe

## Le piège

Tu rejoins une équipe qui gère le logiciel de gestion des tournées d'une entreprise de
livraison de colis frais. 400 000 lignes, sept ans d'historique, trois refontes partielles
jamais terminées. Ton ticket du premier jour : "le calcul de créneau de livraison affiche
parfois un horaire impossible, corrige ça". Tu ouvres le dépôt. Tu tapes "creneau" dans la
recherche globale : 340 résultats répartis dans 60 fichiers. Tu passes ta journée à cliquer
de fichier en fichier sans savoir si tu remontes vers la cause ou si tu t'enfonces dans une
branche morte. À 17h tu n'as toujours pas trouvé le bug, et tu ne sais même pas dire dans
quel service il vit.

Ce n'est pas un manque de compétence technique. C'est un manque de méthode d'entrée.

## Ce qui casse sans ce niveau

- **Tu lis linéairement un système qui n'est pas linéaire.** Le code s'exécute dans un ordre,
  mais sa logique de conception suit un autre ordre — celui des responsabilités métier. Lire
  fichier par fichier dans l'ordre alphabétique ou dans l'ordre d'appel te noie, parce que tu
  accumules des détails avant d'avoir la carte qui leur donne un sens.
- **Tu juges un design avant de comprendre ses contraintes.** Tu vois une fonction de 400
  lignes et tu penses "mauvais code". Six mois plus tard tu apprends qu'elle existe parce
  qu'un audit de conformité imposait qu'un seul point de code traite toute la logique de
  calcul de prix, traçable en une seule revue. Le jugement rapide te fait perdre la confiance
  de l'équipe en place et te fait rater la vraie leçon.
- **Tu ne sais pas où est le risque.** Dans un système inconnu, certains fichiers sont
  cosmétiques et d'autres sont le cœur battant qui, s'il casse, arrête l'entreprise. Sans
  méthode, tu traites les deux avec la même prudence — trop pour l'un, pas assez pour l'autre.
- **Tu proposes des réécritures qui répètent des erreurs déjà commises et corrigées.** Le
  code que tu trouves "mal fait" a souvent déjà été "bien fait" dans une version antérieure,
  puis changé pour une raison que le commit d'origine explique et que tu n'as pas lue.

## Ce qui se passe vraiment

Un gros code base est un artefact archéologique. Il porte les traces de décisions produit,
de contraintes techniques disparues, de rapports de force d'équipe, de deadlines qui ont
forcé des compromis. Lire ce code sans reconstruire ce contexte, c'est lire les ruines d'une
ville sans savoir qu'elle a survécu à trois guerres et un tremblement de terre — tu vois des
murs bizarrement placés et tu conclus que les architectes étaient mauvais.

```text
Code étranger = signal + bruit + histoire figée

Signal   → logique métier actuelle, ce qui doit marcher aujourd'hui
Bruit    → code mort, expérimentations abandonnées, copier-coller jamais nettoyé
Histoire → contraintes disparues (deadline, migration, contrat client) toujours visibles
           dans la forme du code, même quand la raison a disparu de toute mémoire humaine
```

Ce niveau t'apprend à séparer ces trois couches vite, avec une méthode répétable, pour
qu'entrer dans un système inconnu devienne un exercice maîtrisé au lieu d'une noyade.

## Pourquoi cette compétence est rare

La plupart des formations optimisent pour "écrire depuis zéro". C'est confortable à enseigner
et à évaluer. Mais le marché du travail ne t'offre presque jamais une feuille blanche. Un
développeur qui sait entrer vite dans l'inconnu et en ressortir avec un diagnostic juste est
plus précieux, plus vite, qu'un développeur qui écrit un code neuf impeccable mais met trois
semaines à comprendre où le brancher.

## Ce que tu dois savoir défendre

- Pourquoi juger la qualité d'un code avant d'avoir compris ses contraintes est une erreur
  de méthode, pas une preuve de sens critique.
- Pourquoi un gros code base doit se lire par couches de responsabilité et non par ordre
  d'exécution ou ordre alphabétique.
- Donne un exemple (vécu ou plausible) de code qui semble mauvais mais qui répond en fait à
  une contrainte externe légitime.
