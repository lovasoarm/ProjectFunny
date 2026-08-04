# Rétrospective de bloc : Bloc EPREUVE (niveaux 11 à 12)

[Sommaire](CURRICULUM.md) | [Rythmes](ROADMAP.md) | [Style](_STYLE.md)

Une rétrospective de bloc n'est pas un bilan de motivation. C'est une opération précise : tu
rouvres un livrable écrit avant d'avoir appris ce que tu sais maintenant, et tu écris ce qui
est désormais faux, et pourquoi ce n'était pas visible à l'époque.

Durée : 60 à 90 minutes. À faire après le boss-fight du niveau 12-CAPSTONE-ARENA, avant d'ouvrir le bloc
suivant. Ne saute pas cette étape : c'est la seule fois du parcours où tu juges ton propre
travail passé avec des yeux différents.

## Ce que tu rouvres

- ton rapport d'exploration du niveau 11 ([11-BIG-APP-SNOOP/challenge.md](11-BIG-APP-SNOOP/challenge.md)),
- ta note de cadrage du capstone ([12-CAPSTONE-ARENA/03-deliverables.md](12-CAPSTONE-ARENA/03-deliverables.md)),
- ton HYPOTHESES.md du capstone,
- ta REVUE-DE-RISQUES.md du capstone.

Tu les rouvres sans les corriger tout de suite. D'abord tu lis, ensuite tu annotes, enfin tu
décides quoi reprendre.

## Le protocole en trois passes

```text
passe 1 : lecture seule (20 min)
   |
   +-- tu surlignes chaque affirmation qui te fait tiquer
   +-- interdiction absolue de modifier le fichier
   |
passe 2 : verdict ligne par ligne (30 min)
   |
   +-- FAUX      : contredit par ce que tu sais maintenant
   +-- INCOMPLET : vrai, mais il manque la condition qui le rend vrai
   +-- TENU      : toujours valide, et tu sais dire pourquoi
   |
passe 3 : écriture de la rétro (20 à 40 min)
   \-- tu remplis le gabarit ci-dessous, tu ne réécris pas le livrable
```

## Gabarit imposé de ta rétro

Crée le fichier `RETRO-BLOC-4-EPREUVE-<date-du-jour>.md` dans le dépôt de ton projet fil rouge.

```text
Livrable rouvert :
Date d'écriture initiale :          Date de relecture :

1. Ce qui est désormais FAUX
   - affirmation exacte (citation) :
   - ce qui la rend fausse (mécanisme, pas impression) :
   - ce que j'ignorais au moment de l'écrire :
   - ce que ça aurait coûté de le découvrir en production :

2. Ce qui est INCOMPLET
   - affirmation :
   - condition manquante :

3. Ce qui est TENU
   - affirmation :
   - la preuve qui la soutient aujourd'hui :

4. La reprise
   - je corrige maintenant :
   - je ne corrige pas, et j'assume, parce que :
   - le signal chiffré qui me forcera à y revenir :

Signé :                             Date :
```

Une rétro sans aucune ligne en FAUX est suspecte. Sur ce bloc, personne n'a tout eu bon du
premier coup. Si tu n'en trouves aucune, tu relis en cherchant à te donner raison.

## Les trois faux les plus fréquents sur ce bloc

| Symptôme observable | Ce que ça révèle |
| --- | --- |
| Une hypothèse de cadrage s'est révélée fausse | Tu l'avais notée en confiance haute : sur quoi reposait cette confiance ? |
| Une conclusion du rapport d'exploration ne tient plus | Tu as jugé un code inconnu en 3 h, le code te répond maintenant. |
| Une mesure de la revue de risques n'a jamais été appliquée | Une mesure jamais appliquée est une intention, pas une mesure. |

## Analogie

Analogie : une rétrospective de bloc, c'est le débriefing d'après service en cuisine, et le
point de navigation où l'on reporte la position réelle sur la carte après une nuit de route.
Où l'analogie casse : en cuisine et en mer, l'écart se constate sur des faits déjà mesurés.
Ici, l'écart vient de toi : c'est ta grille de lecture qui a changé, pas le livrable, et rien
ne t'oblige à l'admettre à part la discipline du gabarit.

## Critères de réussite

- Au moins 3 affirmations classées FAUX ou INCOMPLET, avec leur mécanisme nommé.
- Au moins 1 correction réellement appliquée au livrable d'origine.
- Au moins 1 non-correction assumée, avec son signal chiffré de réouverture.
- La rétro est signée, datée, et vit dans le dépôt à côté du livrable qu'elle juge.

## Si tu bloques

Rouvre [11-BIG-APP-SNOOP/challenge.md](11-BIG-APP-SNOOP/challenge.md) et relis seulement sa section de critères de réussite : la plupart
des FAUX se voient en comparant un livrable à ses propres critères, pas à ta mémoire.
