# Non-objectifs : écrire ce qu'on ne fera pas

## La scène

Une association de covoiturage scolaire commande une appli de mise en relation entre parents
conducteurs et parents dont l'enfant a besoin d'un trajet. Le cahier des charges liste ce que
l'appli doit faire : créer un trajet, s'inscrire, recevoir une confirmation. Personne n'écrit
ce qu'elle ne doit pas faire. Trois mois plus tard, un parent demande "et si deux enfants de
familles différentes veulent partager un trajet avec paiement au prorata de l'essence ?" —
le développeur, sans document pour trancher, commence à coder un système de répartition de
coûts. Deux semaines plus tard, l'association réalise qu'elle ne voulait surtout pas gérer
d'argent entre parents, pour des raisons d'assurance et de responsabilité. Le code est jeté.

## Ce qui se passe vraiment

Un non-objectif n'est pas une liste de fonctionnalités reportées à plus tard. C'est une
décision explicite, écrite avant que la pression n'arrive, sur ce que le produit refuse de
faire : parce que le faire créerait un risque, une complexité ou une dérive de mission plus
coûteuse que le bénéfice apporté. Un non-objectif protège l'architecture en empêchant qu'elle
absorbe silencieusement des responsabilités qu'elle n'a jamais été conçue pour porter.

La différence entre "pas maintenant" et "non" compte énormément :

```text
"Pas maintenant" (backlog, hors scope V1) :
  → l'architecture doit rester ouverte à cette possibilité
  → exemple : notifications push, prévu pour V2, l'architecture d'événements
    doit déjà pouvoir les émettre

"Non, jamais dans ce produit" (non-objectif) :
  → l'architecture peut fermer la porte, ne doit pas en payer le coût
  → exemple : "l'appli ne gère jamais de paiement entre parents"
    → pas de modèle de compte, pas de conformité DSP2, pas de litige à arbitrer
```

Confondre les deux coûte cher dans les deux sens : traiter un vrai "non" comme un "pas
maintenant" fait payer une flexibilité architecturale inutile ; traiter un vrai "pas maintenant"
comme un "non" définitif oblige à tout refaire quand le besoin revient, ce qui arrive souvent.

## Un non-objectif a un effet mesurable ou il ne sert à rien

Un non-objectif décoratif ressemble à ceci : "Nous ne visons pas l'excellence à tout prix."
Personne ne peut s'appuyer dessus pour trancher un désaccord. Un non-objectif utile ressemble
à ceci : "Le produit ne gère jamais de transaction financière entre utilisateurs." Ce second
énoncé, quand un parent demande un partage de frais, permet de répondre en une phrase et de
fermer la discussion sans reconstruire l'argumentation à chaque fois.

Test pour vérifier qu'un non-objectif est réel :

```text
Si une personne de l'équipe propose une fonctionnalité qui viole ce non-objectif,
est-ce que le non-objectif suffit, seul, à la refuser sans débat de fond ?

  Oui → non-objectif utile, il a fait le travail qu'on attendait de lui
  Non, il faut quand même rediscuter → ce n'était pas un vrai non-objectif,
    juste une préférence formulée en négatif
```

## Où écrire les non-objectifs pour qu'ils servent

Un non-objectif enterré dans un compte-rendu de réunion de cadrage ne sert à rien six mois
plus tard, quand la personne qui doit trancher n'était pas dans la salle. Il doit vivre dans
un document que les nouvelles personnes de l'équipe lisent en arrivant, et être reformulé
chaque fois qu'une décision technique en dépend directement : en commentaire dans le code,
si besoin, à l'endroit précis où quelqu'un pourrait être tenté de le violer.

```ts
// NON-OBJECTIF (voir doc de cadrage, section 3) :
// ce service ne gère jamais de transaction financière entre utilisateurs.
// Si tu es tenté d'ajouter un champ "montant" ici, relis d'abord la décision
// de cadrage et fais valider un changement de scope explicite avant de coder.
interface TripRequest {
  fromAddress: string;
  toAddress: string;
  scheduledAt: Date;
  // pas de champ "cost" ou "amount" : voir non-objectif ci-dessus
}
```

## Compromis

| Option                                            | Coût                                                        | Bénéfice                                                    | Quand choisir                                                  |
| ------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- | -------------------------------------------------------------- |
| Écrire des non-objectifs précis et vérifiables    | Demande une vraie décision, parfois inconfortable, en amont | Tranche les débats futurs sans reconvoquer tout le monde    | Systématiquement, dès qu'un risque de dérive de mission existe |
| Écrire des non-objectifs vagues ("rester simple") | Rapide, ne froisse personne                                 | Ne sert à rien au moment où on en aurait besoin             | Jamais, sauf à vouloir se donner l'illusion d'avoir cadré      |
| Ne pas écrire de non-objectifs du tout            | Semble faire gagner du temps de cadrage                     | Chaque demande de périmètre devient un débat de fond répété | Uniquement sur un prototype jetable sans suite                 |

## Pièges classiques

- Confondre non-objectif et fonctionnalité repoussée : le symptôme est qu'on retrouve la
  même fonctionnalité "interdite" en discussion trois sprints plus tard sans que personne ne
  cite le document de cadrage pour trancher.
- Écrire des non-objectifs si généraux qu'ils ne permettent de refuser aucune demande concrète.
- Écrire les non-objectifs après avoir commencé à coder, une fois que l'architecture a déjà
  absorbé la complexité qu'ils étaient censés éviter.
- Oublier de relier le non-objectif à sa raison d'être (risque légal, responsabilité, coût de
  maintenance) : sans la raison, le non-objectif devient contestable au premier désaccord.

## Ce que tu dois savoir défendre

- La différence concrète entre un "non-objectif" et une fonctionnalité simplement reportée,
  et pourquoi les confondre coûte cher dans les deux sens.
- Comment vérifier qu'un non-objectif écrit est réellement utile plutôt que décoratif.
- Où et comment un non-objectif doit être documenté pour rester utilisable des mois plus tard,
  par des personnes qui n'étaient pas présentes à sa rédaction.
