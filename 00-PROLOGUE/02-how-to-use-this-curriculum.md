# Comment utiliser ce curriculum

## La scène

Léa suit un parcours en ligne sur les architectures distribuées. Elle regarde les vidéos à
1.5x, coche les cases, obtient le certificat. Trois mois plus tard, on lui demande de
concevoir le système de suivi des tournées de livraison d'une PME de messagerie — position
des chauffeurs, replanification en cas de retard, notification client — et elle se rend
compte qu'elle ne sait pas par où commencer. Elle a "suivi" le cours. Elle n'a rien produit
avec. Le certificat existe ; la compétence, non.

Ce curriculum est construit pour que ça ne t'arrive pas, mais seulement si tu respectes trois
règles d'usage. Les ignorer revient à transformer ce contenu en documentation qu'on lit une
fois et qu'on oublie.

## Ce qui se passe vraiment

### Règle 1 — le livrable, pas la lecture

Chaque niveau se termine par un `challenge.md` : un exercice appliqué avec un livrable
concret et des critères de réussite mesurables. Lire les leçons sans faire le challenge, c'est
comme lire un livre de musculation sans soulever de barre. Tu comprendras intellectuellement
la surcharge progressive, tes muscles n'en sauront rien.

La règle est simple et non négociable : **tu ne passes au niveau suivant qu'après avoir
produit le livrable et l'avoir confronté au `boss-fight.md`.** Pas "je pense avoir compris" —
un artefact qui existe, que tu peux montrer, critiquer, casser.

```text
   lire la leçon
        │
        ▼
   comprendre le modèle mental (tu peux l'expliquer à voix haute, sans notes)
        │
        ▼
   produire le livrable du challenge
        │
        ▼
   affronter le boss-fight (contrainte adverse réaliste)
        │
        ▼
   ┌────────────┴────────────┐
   │                         │
 réussi                   échoué
   │                         │
   ▼                         ▼
niveau suivant      retour à la leçon concernée,
                     PAS au début du niveau
```

### Règle 2 — le rythme est déterminé par la compréhension, pas par le calendrier

Il n'y a pas de "un niveau par semaine". Un niveau peut prendre trois jours ou trois semaines
selon ta base. Le signal pour avancer n'est pas "j'ai fini de lire" mais "je peux défendre
chaque réponse de la section *Ce que tu dois savoir défendre* sans relire la leçon". Si tu
bloques sur une question, c'est que le modèle mental n'est pas encore installé — retourne
lire, pas en diagonale, mais en cherchant spécifiquement la réponse à cette question.

Un rythme réaliste pour un développeur avec 1-3 ans d'expérience travaillant à côté d'un
emploi : 4 à 8 heures par semaine, un niveau toutes les 2 à 4 semaines selon sa densité.

### Règle 3 — l'auto-évaluation se fait par confrontation, pas par confiance

Tu ne peux pas juger toi-même si ton livrable est bon en te demandant "est-ce que je suis
content de moi ?". Ce n'est pas un critère. Les critères sont dans `challenge.md`
(mesurables : "le système supporte X sans Y", "le document explique Z en moins de N lignes")
et dans `boss-fight.md` (une grille d'évaluation avec des seuils explicites). Si possible,
fais relire ton livrable par quelqu'un d'autre — un pair, un mentor, ou toi-même dans une
semaine avec un œil froid. La confrontation externe révèle les angles morts qu'un
raisonnement solitaire ne révèle jamais.

### Ce que "terminer un niveau" signifie réellement

Terminer un niveau, ce n'est pas avoir lu tous les fichiers. C'est avoir :

1. Un modèle mental que tu peux expliquer à quelqu'un d'autre en moins de cinq minutes sans
   support.
2. Un livrable concret, versionné quelque part (dépôt git, document daté), que tu peux
   montrer dans six mois pour te souvenir de ce que tu as compris à ce moment-là.
3. Une trace écrite d'au moins une décision que tu as prise et que tu pourrais défendre en
   entretien technique.

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Suivre le curriculum linéairement, niveau par niveau | Plus lent, exige de la discipline | Modèle mental cumulatif solide, chaque niveau s'appuie sur le précédent | Par défaut — c'est la conception du parcours |
| Piocher les niveaux qui t'intéressent selon un besoin immédiat | Rapide pour combler un trou précis | Modèle mental fragmenté, angles morts sur les fondations | Quand tu as déjà un modèle mental solide ailleurs et un besoin ponctuel documenté |

## Pièges classiques

- Lire trois niveaux d'affilée sans faire un seul challenge — le symptôme est de ne plus
  savoir répondre aux questions du niveau précédent quand on te les repose.
- Faire le challenge en cherchant "la bonne réponse attendue" plutôt qu'en raisonnant depuis
  le contexte donné — le symptôme est un livrable qui ressemble à un exemple du cours au lieu
  de répondre au cas concret posé.
- Sauter le boss-fight parce que "le challenge suffit" — le symptôme est de se faire
  déstabiliser par la première contrainte imprévue en situation réelle.
- Considérer une note personnelle non écrite comme suffisante — le symptôme est l'incapacité
  à retrouver, six mois après, pourquoi une décision a été prise.

## Ce que tu dois savoir défendre

1. Pourquoi "avoir lu" un niveau ne veut rien dire dans ce curriculum.
2. Que fais-tu si tu échoues au boss-fight d'un niveau — où retournes-tu exactement, et
   pourquoi pas au tout début du niveau.
3. Quels sont les trois éléments concrets qui prouvent qu'un niveau est terminé.
