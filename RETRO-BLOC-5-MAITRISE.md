# Rétrospective de bloc : Bloc MAITRISE (niveaux 13 à 15)

[Sommaire](CURRICULUM.md) | [Rythmes](ROADMAP.md) | [Style](_STYLE.md)

Une rétrospective de bloc n'est pas un bilan de motivation. C'est une opération précise : tu
rouvres un livrable écrit avant d'avoir appris ce que tu sais maintenant, et tu écris ce qui
est désormais faux, et pourquoi ce n'était pas visible à l'époque.

Durée : 60 à 90 minutes. À faire après avoir terminé le niveau 15-BONUS-VAULT, donc à la fin
du parcours, avant d'ouvrir [EPILOGUE.md](EPILOGUE.md). Ne saute pas cette étape : c'est la
seule fois du parcours où tu juges tes propres habitudes de travail, et non un livrable
technique isolé.

## Ce que tu rouvres

- ta routine d'apprentissage écrite au niveau 13 ([13-DAY-TO-LEGEND/challenge.md](13-DAY-TO-LEGEND/challenge.md)),
- ton `HYPOTHESES.md` et ton relevé de temps de résolution du niveau 14 ([14-TOOL-CAVE/challenge.md](14-TOOL-CAVE/challenge.md)),
- ta `REVUE-DE-RISQUES.md` du capstone, relue à la lumière du niveau 15 ([15-BONUS-VAULT/05-security-cost-privacy.md](15-BONUS-VAULT/05-security-cost-privacy.md)),
- le journal de bord que tu tiens depuis le niveau 00, s'il existe encore.

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

Crée le fichier `RETRO-BLOC-5-MAITRISE-<date-du-jour>.md` dans le dépôt de ton projet fil rouge.

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
| La routine d'apprentissage du niveau 13 n'a plus une seule entrée datée depuis trois semaines | Une routine qui ne survit pas à la première semaine chargée n'était pas dimensionnée pour ta vie réelle, elle était dimensionnée pour ta motivation du jour où tu l'as écrite. |
| Ton temps de résolution de bug mesuré après le niveau 14 n'est pas comparable à celui d'avant, faute d'avoir noté le premier | Tu as adopté une méthode sans jamais mesurer si elle t'a fait gagner du temps : c'est exactement le biais que le niveau 14 dénonce, appliqué à toi-même. |
| La revue sécurité/coûts/RGPD n'a été faite qu'une fois, pour le capstone, et jamais depuis sur un projet suivant | Le niveau 15 devient un exercice scolaire, pas un réflexe : un contrôle qui ne se déclenche que sur demande d'un correcteur ne protège aucun utilisateur. |

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
- Une date précise, inscrite dans ton agenda, pour la prochaine revue sécurité/coûts/RGPD sur
  un projet qui n'est pas le capstone.
- La rétro est signée, datée, et vit dans le dépôt à côté du livrable qu'elle juge.

## Si tu bloques

Rouvre [13-DAY-TO-LEGEND/challenge.md](13-DAY-TO-LEGEND/challenge.md) et relis seulement sa section de critères de réussite : la plupart
des FAUX se voient en comparant un livrable à ses propres critères, pas à ta mémoire.
