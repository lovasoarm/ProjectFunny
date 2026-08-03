# Boss Fight : La migration du vendredi et le tarif qui ne peut pas attendre

## La situation

Tu es seul responsable du schéma de refacturation d'énergie. Jeudi soir, le service commercial
t'annonce qu'un nouveau tarif réglementaire entre en vigueur lundi matin à 00h00, décidé par le
régulateur national, sans marge de négociation sur la date. Le schéma actuel de `tarif_energie`
n'a pas de période de validité : une seule ligne "tarif courant" est mise à jour en place à
chaque changement, comme il a toujours été fait depuis la création du logiciel. Le calcul de
facturation du trimestre en cours, déjà à moitié exécuté sur les dix premiers jours d'un cycle
de trente, dépend de cette même table. La table `tarif_energie` est lue à chaque calcul de
facture, plusieurs fois par seconde en période de pointe (fin de trimestre, dans dix jours).

Ton manager te propose la solution la plus rapide qu'il connaît : écrire un script qui, dimanche
soir à minuit, fait un `UPDATE tarif_energie SET prix_kwh = ...` en place, comme d'habitude, et
"on verra pour le vrai historique la prochaine fois qu'on aura du temps, là c'est urgent, le
régulateur ne négocie pas la date". Le service facturation, de son côté, te demande si les
factures déjà émises ce trimestre avec l'ancien tarif devront être recalculées ou pas : personne
ne le sait encore, la réponse dépendra d'un texte réglementaire encore en discussion. Tu as
jusqu'à dimanche soir.

## Les contraintes réelles

- Le changement de tarif est non négociable dans sa date : lundi 00h00, sans exception.
- Le schéma actuel ne distingue pas "tarif courant" de "tarif applicable à telle date" : un
  `UPDATE` en place efface silencieusement la trace du tarif précédent.
- Le calcul de facturation en cours d'exécution touche des factures à cheval sur les deux
  tarifs (dix premiers jours à l'ancien tarif, vingt jours restants au nouveau) : un `UPDATE`
  en place au mauvais moment ferait facturer TOUT le trimestre au nouveau tarif, y compris les
  jours déjà consommés sous l'ancien.
- Personne ne sait encore si les factures déjà émises devront être recalculées rétroactivement
  une fois le texte réglementaire définitif publié : la réponse peut arriver après lundi.

## Ce qu'on attend de toi

Produis une décision écrite (une page maximum) qui :

1. Refuse explicitement le `UPDATE` en place, avec le mécanisme précis de ce niveau qui explique
   pourquoi il produirait un résultat faux, pas seulement "risqué" : illustre avec le cas concret
   des dix jours déjà consommés sous l'ancien tarif.
2. Propose un schéma minimal livrable avant dimanche soir qui introduit une période de validité
   sur `tarif_energie`, sans réécrire tout le système de facturation en urgence.
3. Explique comment le calcul de facturation en cours doit lire le bon tarif selon la date
   réelle de consommation, pas selon la date d'exécution du calcul.
4. Répond à l'incertitude sur le recalcul rétroactif des factures déjà émises : propose une
   décision de modélisation qui reste correcte QUEL QUE SOIT ce que dira le texte réglementaire
   plus tard (indice : la leçon sur les snapshots explicites de facture s'applique directement).
5. Propose une vérification concrète, exécutable dimanche soir avant minuit, qui prouve que le
   nouveau tarif sera bien appliqué à partir de lundi 00h00 sans intervention manuelle supplémentaire.

## Grille d'évaluation

| Critère                                       | Ce qui est évalué                                                                                                                                              |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Refus argumenté de l'`UPDATE` en place        | Le refus s'appuie sur le mécanisme précis (perte d'historique, application rétroactive fausse d'un tarif), pas une prudence générique                          |
| Schéma minimal livrable en un jour            | La proposition (colonne de période, contrainte d'exclusion ou vérification applicative) est réellement réalisable dans le délai, sans refonte disproportionnée |
| Cohérence de la lecture du tarif applicable   | La solution garantit que chaque facture lit le tarif en vigueur à la date de la consommation qu'elle facture, pas à la date du calcul                          |
| Robustesse face à l'incertitude réglementaire | La décision reste correcte que le recalcul rétroactif soit finalement exigé ou non, sans pari risqué sur l'une ou l'autre issue                                |
| Vérifiabilité                                 | La vérification proposée est concrète et exécutable, pas un vœu pieux ("on testera bien")                                                                      |
