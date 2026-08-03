# Boss Fight — Le refactoring sous deadline

## La situation

Tu rejoins l'équipe technique de la plateforme de refacturation d'énergie du Niveau 05,
au moment précis où la douleur devient visible : un changement de règle de calcul de TVA,
qui aurait dû prendre deux heures, vient de prendre trois jours à cause du couplage entre
le module de facturation et celui des relevés de compteurs. Le CTO, sous pression d'un
client important qui veut cette fonctionnalité "pour hier", te demande de livrer le
correctif dans le code existant, couplé, sans prendre le temps de refactorer — "on nettoiera
après le lancement". Un développeur senior de l'équipe, de son côté, insiste pour tout
réécrire en microservices avant de toucher à quoi que ce soit d'autre, en citant l'article de
blog d'une grande entreprise tech. Tu as trois jours avant la deadline client, et l'équipe
attend une décision de ta part, pas juste du code.

## Les contraintes réelles

- Le client a un contrat qui prévoit une pénalité financière si la fonctionnalité n'est pas
  livrée dans le délai annoncé.
- L'équipe compte quatre développeurs, tous sur le même projet, aucune équipe séparée.
- Le module de facturation et celui des relevés partagent actuellement une même base de
  données et un couplage de contenu direct (l'un lit les tables internes de l'autre).
- Le "nettoyage après le lancement" n'a, historiquement dans cette équipe, jamais eu lieu une
  seule fois en deux ans — chaque "après" a été absorbé par la prochaine urgence.

## Ce qu'on attend de toi

Produis une décision écrite (une page maximum) qui :

1. Refuse explicitement les deux extrêmes proposés (patch couplé "on nettoiera après", et
   réécriture complète en microservices sous trois jours) en expliquant en une phrase
   pourquoi chacun est un pari perdant, avec un coût réel à l'appui de chaque leçon du
   niveau.
2. Propose une troisième voie concrète et réalisable dans les trois jours : identifie
   précisément quel refactoring minimal (probablement l'introduction d'une interface entre
   facturation et relevés, sans toucher à la base de données ni créer de service séparé)
   permet de livrer la fonctionnalité correctement ET de réduire le couplage qui a causé le
   problème, sans tout réécrire.
3. Anticipe l'objection du développeur senior sur les microservices avec un argument tiré des
   quatre critères de décision du niveau (organisation, charge, conformité, maturité), pas un
   simple "on n'a pas le temps".
4. Propose un engagement mesurable et vérifiable pour éviter que "on nettoiera après" ne se
   reproduise une troisième fois (par exemple : le refactoring minimal fait partie de la
   définition de "terminé" de ce ticket, pas une tâche séparée reportable).

## Grille d'évaluation

| Critère | Ce qui est évalué |
|---|---|
| Refus argumenté des deux extrêmes | La justification s'appuie sur un mécanisme concret (couplage, coût réel), pas sur une préférence esthétique ou une peur du changement |
| Troisième voie réalisable | La proposition tient dans les trois jours et cible précisément le couplage qui a causé le problème initial, pas un refactoring générique |
| Réponse aux microservices | L'argument utilise au moins un des quatre critères de décision du niveau, appliqué avec des faits du cas (une seule équipe, pas de contrainte de conformité isolée) |
| Mécanisme anti-récidive | L'engagement proposé est vérifiable (fait partie d'une définition de "terminé", d'un critère de revue de code), pas un vœu pieux |
| Ton | La décision est assumée et défendable devant le CTO et le développeur senior en même temps, sans chercher à plaire aux deux à la fois par du flou |
