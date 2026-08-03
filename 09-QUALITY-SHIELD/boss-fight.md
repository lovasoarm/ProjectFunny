# Boss Fight : L'incident du dimanche, sans ton lead

## Situation

Dimanche 9h. Tu es d'astreinte, seul. Une alerte se déclenche : le taux d'échec de
confirmation de livraison est passé de 1 % à 38 % depuis 8h45. Ton lead technique est
injoignable (weekend prolongé, pas de réseau). Le service de température, celui qui décide
si une tournée respecte la chaîne du froid, a été redéployé samedi soir par un script
automatique de mise à jour de dépendances : personne ne l'a explicitement demandé, c'est une
tâche planifiée qui tourne chaque samedi. Deux développeurs juniors de l'équipe, réveillés
par la même alerte, te proposent chacun une action immédiate et contradictoire : l'un veut
"redéployer la version d'hier soir tout de suite", l'autre veut "d'abord comprendre ce qui a
changé avant de toucher à quoi que ce soit". Une trentaine de tournées sont en cours sur le
terrain en ce moment même, avec des livreurs qui dépendent de l'application pour confirmer
leurs livraisons.

## Contraintes réelles à tenir en tête

- Tu ne peux pas jointer ton lead avant plusieurs heures.
- Chaque minute d'échec de confirmation bloque potentiellement des livreurs sur le terrain,
  un dimanche, avec un service client réduit pour les rassurer.
- Un rollback mal exécuté peut aggraver la situation s'il n'est pas testé, mais attendre
  une analyse complète aggrave l'impact utilisateur pendant ce temps.
- Les deux développeurs juniors attendent une décision claire de ta part, pas un débat ouvert
  pendant que l'incident continue.

## Ce qu'on attend de toi

Une réponse écrite (une page maximum) qui :

1. Tranche entre les deux propositions contradictoires, en expliquant le critère de décision
   (impact utilisateur en cours contre risque d'un rollback non validé), pas en pariant.
2. Décrit la première action concrète que tu lances dans les cinq premières minutes, et
   pourquoi elle est réversible ou à faible risque si elle s'avère être une erreur.
3. Explique comment tu coordonnes les deux développeurs juniors pour éviter qu'ils touchent
   au système en parallèle sans se concerter.
4. Prévoit ce que tu communiques, et à qui, pendant que l'incident est encore en cours (pas
   seulement après résolution).
5. Indique ce que tu mets de côté pour le postmortem du lundi plutôt que de le traiter dans
   l'urgence du dimanche (distinction entre réparer et comprendre, vue en leçon 05).

## Grille d'évaluation

| Critère                          | Insuffisant                                           | Correct                                        | Solide                                                                                     |
| -------------------------------- | ----------------------------------------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Décision sous incertitude        | Ne tranche pas, ou tranche sans critère explicite     | Tranche avec un critère simple                 | Tranche avec un critère lié à l'impact utilisateur réel, assume la part de risque          |
| Coordination de l'équipe         | Laisse les deux développeurs agir chacun de leur côté | Désigne une action, sans rôle clair            | Centralise clairement la décision, empêche les actions parallèles non coordonnées          |
| Séparation réparation/analyse    | Cherche la cause exacte avant d'agir sur l'impact     | Répare d'abord, sans le formuler explicitement | Explique clairement pourquoi il répare avant de comprendre en détail, et reporte l'analyse |
| Communication pendant l'incident | Ne communique qu'une fois résolu                      | Communique une fois, en fin d'incident         | Prévoit une communication régulière pendant l'incident, y compris "on ne sait pas encore"  |
