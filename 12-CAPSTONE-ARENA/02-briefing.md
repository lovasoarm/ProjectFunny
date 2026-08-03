# Le brief

## Contexte

Tu reçois ce message d'un client fictif, à traiter comme s'il était réel :

> Bonjour,
>
> On dirige un réseau de trois salles d'escalade indépendantes qui viennent de s'associer
> pour partager leurs adhérents. Aujourd'hui chaque salle gère ses réservations de créneaux
> "mur", ses cours collectifs et son inventaire de matériel à la main, sur un tableur
> partagé qui déborde de partout. On aimerait un outil pour centraliser tout ça.
>
> Ce qui compte le plus pour nous : que nos adhérents puissent réserver un créneau dans
> n'importe laquelle des trois salles avec leur même compte, et qu'on sache en temps réel
> combien de personnes sont attendues pour ne pas dépasser la capacité de sécurité. On a eu
> un souci l'an dernier avec un contrôle des pompiers sur ce sujet donc c'est sensible.
>
> On aimerait aussi gérer les cours collectifs (un coach, un créneau, un nombre de places
> limité) et idéalement le prêt de matériel (baudriers, chaussons) mais si c'est trop pour
> une première version on peut vivre sans au début.
>
> Pas de budget précis à vous donner, on préfère voir ce que vous proposez et discuter après.
> On voudrait quelque chose d'utilisable dans les deux mois, sachant qu'on a une saison plus
> calme en été si jamais il fallait itérer après un premier lancement.
>
> Merci d'avance,
> Le collectif des trois salles

## Ce que ce brief contient vraiment

Lis-le une seconde fois. Il contient, mélangés sans hiérarchie explicite :

- Un besoin fonctionnel central non négociable : la réservation multi-salles avec compte
  partagé et comptage de capacité en temps réel (motivé par une contrainte de sécurité
  réelle, pas un confort).
- Un besoin secondaire explicitement dégradable : cours collectifs et prêt de matériel,
  que le client accepte de reporter.
- Une contrainte de délai floue ("dans les deux mois") sans définition de ce que signifie
  "utilisable".
- Une absence totale de contrainte budgétaire explicite, ce qui n'est pas une liberté totale
  mais un signal qu'il faudra proposer un cadrage toi-même.
- Un indice caché mais capital : "souci avec un contrôle des pompiers" signifie que le
  comptage de capacité n'est pas une fonctionnalité de confort, c'est une exigence de
  conformité que tu dois traiter avec la rigueur d'une contrainte légale, pas d'une feature
  parmi d'autres.

## Ce que tu dois produire avant de lire la suite

Avant d'ouvrir `03-deliverables.md`, écris pour toi-même, sur une feuille séparée :

1. Trois hypothèses explicites sur ce que "utilisable dans les deux mois" signifie pour ce
   client, classées par plausibilité.
2. La fonctionnalité que tu identifies comme non négociable, avec la phrase exacte du brief
   qui te le prouve.
3. Deux questions que tu poserais au client si tu avais un contact direct, et deux hypothèses
   de repli si le client ne répond pas à temps.

Ce n'est pas un exercice de style. La qualité de ton découpage dans `challenge.md` dépendra
directement de la rigueur de ce travail préalable.

## Ce que tu dois savoir défendre

- Pourquoi la mention du contrôle des pompiers change le niveau d'exigence attendu sur le
  comptage de capacité, par rapport à une fonctionnalité de confort classique.
- Pourquoi l'absence de budget explicite n'est pas une liberté sans contrainte, mais un
  signal qu'il te revient de proposer une structure de coût toi-même.
- Comment tu justifierais, face au client, de reporter les cours collectifs et le prêt de
  matériel à une deuxième phase sans donner l'impression de réduire ta prestation.
