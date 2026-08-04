# Challenge : Rédiger et faire vivre un working agreement

## Contexte

Choisis une équipe réelle ou simulée avec laquelle tu collabores ou vas collaborer (projet
perso à plusieurs, projet associatif, équipe de travail). L'équipe doit compter au moins
deux personnes en plus de toi, et travailler sur un système qui va évoluer sur plusieurs
semaines : pas une tâche ponctuelle de quelques jours.

## Livrable

Un document (`working-agreement.md`) contenant :

1. **Une définition de "fini" vérifiable**, spécifique à ton projet, sous forme de liste de
   conditions observables : pas une phrase générale.
2. **Une table "qui décide quoi"** couvrant au moins quatre types de décisions (choix
   d'implémentation locale, choix d'architecture partagée, priorité produit, décision
   irréversible ou coûteuse), avec pour chacun le niveau de décision et la méthode.
3. **Un engagement mesurable sur les délais de revue** de code ou de travail, formulé en
   temps observable, pas en intention ("on essaie d'être réactif" n'est pas un engagement).
4. **Une règle de signalement de retard ou d'absence**, avec le seuil précis à partir duquel
   le signalement est attendu.
5. **Une règle de coordination sur les fichiers ou modules partagés**, adaptée à la taille
   réelle de ton équipe.
6. **Un choix de flux Git justifié** (trunk-based avec feature flags, GitFlow, ou un
   compromis), avec une phrase expliquant pourquoi ce choix correspond au rythme de release
   réel de ton équipe et pas à une habitude.
7. **Une date de rédaction et une date de révision prévue** (un mois plus tard maximum).

## Validation

Le document doit être présenté et discuté avec au moins un autre membre réel de l'équipe
(ou, à défaut d'équipe réelle, avec une personne qui joue ce rôle de façon sérieuse). La
discussion doit produire au moins une modification du document par rapport à ta première
version : si aucune modification n'a lieu, c'est que le document n'a pas été vraiment
challengé, ou que la discussion n'a pas eu lieu réellement.

## Critères de réussite

- Chaque règle du working agreement peut être vérifiée par un fait observable, pas par une
  impression : relis chaque ligne et demande-toi "comment je saurais que cette règle est
  respectée ou non ?".
- La table "qui décide quoi" identifie clairement le type de décision le plus susceptible de
  créer un désaccord dans ton contexte spécifique, pas une liste générique recopiée du cours.
- Le choix de flux Git est justifié par le rythme de release réel de l'équipe (mesuré ou
  estimé), pas par la préférence personnelle du rédacteur.
- Le document tient en une à deux pages : s'il en fait dix, il ne sera jamais relu ni
  respecté.
- Une trace écrite de la discussion de validation existe (même informelle : un message, un
  compte-rendu de deux lignes) montrant ce qui a changé entre la première version et la
  version validée.

## Piège volontaire

Ne rédige pas ce document seul dans ton coin avant de le montrer à l'équipe comme un fait
accompli. Commence par demander à au moins une personne de l'équipe quelle règle implicite
elle suit déjà sans l'avoir jamais écrite : c'est souvent la première ligne la plus utile du
document, et c'est exactement le réflexe que ce niveau construit.

## Durée cible

6 h, cohérent avec la durée annoncée pour ce niveau dans CURRICULUM.md. Si tu dépasses
largement ce budget, c'est un signal à noter dans ta rétrospective de bloc, pas une raison
de bâcler la fin du livrable.
