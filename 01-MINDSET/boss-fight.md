# Boss fight — Un lead conteste ton choix en réunion

## La scène

Tu présentes ton ADR (celui du challenge, ou un choix réel sur ton projet fil rouge) en
réunion d'équipe. Tu as choisi de stocker l'historique des statuts d'une tournée de
livraison (créée, en préparation, en route, livrée, incident) sous forme d'une seule colonne
`statut_actuel` mise à jour à chaque changement, plutôt que sous forme d'une table
d'événements append-only qui garderait chaque transition.

Un lead technique, présent pour la première fois à une réunion sur ce projet, t'interrompt :

> "Je ne comprends pas pourquoi vous ne gardez pas l'historique complet dès le départ. Ça
> coûte trois fois rien à l'écriture, et le jour où le service client demande 'pourquoi cette
> livraison a mis quatre heures de plus que prévu', vous n'aurez rien à leur montrer. On a eu
> exactement ce problème sur un projet précédent, et on l'a payé cher six mois après."

Il n'est pas hostile, mais il est catégorique, et il a une expérience concrète derrière son
objection. La salle attend ta réponse. Tu ne peux pas répondre "on verra plus tard" — c'est
une réunion de décision, pas de brainstorming.

## Ce qu'on attend de toi

Rédige, comme si tu répondais en réunion puis par écrit dans l'ADR mis à jour, une réponse
qui :
- ne rejette pas l'objection sans l'avoir réellement pesée (le lead a raison sur un point
  précis : lequel ?) ;
- ne cède pas non plus immédiatement par confort social — si ta décision reste défendable
  compte tenu du contexte réel du projet, dis-le et explique pourquoi son expérience
  précédente ne s'applique pas forcément telle quelle ici ;
- s'appuie sur des faits vérifiables du projet (volume attendu, contraintes de délai,
  compétences de l'équipe) plutôt que sur une préférence esthétique pour l'une ou l'autre
  architecture ;
- propose, si pertinent, un compromis à coût mesuré plutôt qu'un choix binaire — par exemple
  une dette volontaire documentée avec un seuil de migration explicite (leçon 03).

Livre un court texte (une demi-page) qui pourrait être ajouté tel quel à la section
"Conséquences" ou "Options considérées" de ton ADR pour montrer que l'objection a été prise
en compte, pas juste survolée.

## Grille d'évaluation

| Critère | Ce qui est évalué | Échec typique |
|---|---|---|
| Reconnaissance du point valide | Le texte identifie précisément ce que le lead a raison de signaler (perte d'information si un jour un historique fin est nécessaire) | Réponse défensive qui ignore l'objection ou la caricature |
| Distinction contexte / généralité | Le texte explique pourquoi l'expérience du lead sur un autre projet ne se transpose pas automatiquement (volume différent, besoin métier différent, délai différent) | Le texte traite l'analogie comme automatiquement valide ou automatiquement invalide sans l'examiner |
| Fondation sur des faits, pas des préférences | Le texte cite au moins un élément vérifiable du projet réel (volume de tournées/jour, fréquence des demandes du service client, délai de livraison de la fonctionnalité) | Le texte reste au niveau des principes généraux ("la simplicité c'est important") sans jamais toucher au projet concret |
| Réversibilité explicite | Le texte propose un seuil ou un signal observable qui déclencherait la migration vers une table d'événements, si le choix actuel est maintenu | Le choix est présenté comme définitif et fermé, sans condition de révision |
| Absence de céder par confort social | Si la décision initiale reste justifiée compte tenu du contexte, le texte le dit clairement sans se rétracter juste parce que le lead est catégorique | Le texte change de position uniquement à cause du ton assuré de l'interlocuteur, sans nouvel argument |
| Longueur et densité | Le texte tient dans environ 200 mots, sans détour | Réponse diluée qui noie l'argument dans des formules de politesse |

## Ce que tu dois savoir défendre

1. Explique pourquoi "cette solution a causé un problème sur un autre projet" n'est ni une
   preuve qu'elle en causera un ici, ni une raison de l'ignorer.
2. Donne un seuil chiffré concret (volume de tournées, fréquence de demandes du service
   client) qui, s'il était dépassé, justifierait de migrer vers une table d'événements.
3. Explique la différence entre céder à une objection parce qu'elle est fondée et céder à une
   objection parce qu'elle est exprimée avec assurance.
