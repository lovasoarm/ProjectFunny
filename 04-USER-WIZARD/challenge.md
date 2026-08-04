# Challenge : Le planning du cabinet qui ne ment jamais

## Contexte

Tu livres l'écran de planning journalier du cabinet vétérinaire, utilisé par l'accueil toute la
journée sur un poste moyen et une connexion instable.

## Livrable

Un écran de planning + un formulaire de prise de rendez-vous, avec :

1. Un parcours écrit (`flow.md` ou schéma) listant le chemin heureux et au moins six embranchements
   d'erreur, avant tout code d'écran.
2. Un composant de planning modélisé en union discriminée avec au minimum les états : idle,
   loading, empty, ready, partial, overload, error.
3. Un formulaire de réservation avec schéma de validation partagé client/serveur (même fichier
   importé des deux côtés, ou schéma dupliqué documenté comme dette si le stack l'impose).
4. Une clé d'idempotence posée sur la création de rendez-vous, avec table serveur qui rejoue la
   réponse en cas de répétition.
5. Focus clavier géré sur l'ouverture/fermeture de la modale de confirmation.
6. Un squelette de chargement dont la forme correspond au contenu réel.

## Critères de réussite mesurables

- En coupant le réseau après le clic sur "Réserver", l'UI passe par un état visible distinct de
  "loading" classique (état incertain), et ne recrée pas de doublon au retour du réseau.
- En envoyant trois fois la même requête de création (via curl, avec la même clé d'idempotence), un
  seul rendez-vous existe en base et les trois réponses sont identiques.
- Sur une liste vide (aucun rendez-vous), l'écran affiche un message explicite et une action, pas un
  espace blanc.
- Sur une liste de plus de 200 rendez-vous simulés, l'écran ne rend pas tout d'un coup et reste
  interactif (mesuré : temps entre clic et réaction visuelle inférieur à 100ms).
- En naviguant uniquement au clavier (Tab, Entrée, Échap), il est possible d'ouvrir la modale de
  réservation, la remplir, la soumettre, et la fermer, sans jamais perdre le focus visuellement.
- Le formulaire rejette côté serveur un payload qui aurait pu passer la validation client via un
  appel direct (testé avec curl en contournant l'UI).

## Ce qui n'est pas demandé

Pas de design visuel abouti, pas de responsive complet. Le jury n'évalue pas l'esthétique, il évalue
la robustesse des états et des parcours.

## Durée cible

8 h, cohérent avec la durée annoncée pour ce niveau dans CURRICULUM.md. Si tu dépasses
largement ce budget, c'est un signal à noter dans ta rétrospective de bloc, pas une raison
de bâcler la fin du livrable.
