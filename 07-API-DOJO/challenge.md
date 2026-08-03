# Challenge : Blinder une API existante

## Contexte

Reprends une API que tu as déjà écrite (ou crée-en une minimale, 4-5 endpoints suffisent) sur
le thème de ton choix parmi le fil rouge : prise de rendez-vous vétérinaire, suivi de
tournées de livraison, ou consultation de consommations d'énergie. Elle doit exposer au
minimum : un endpoint de liste (avec potentiellement beaucoup d'éléments), un endpoint de
création d'une ressource à effet réel (rendez-vous, confirmation de livraison, ou demande de
relevé), et un accès qui doit être restreint selon l'identité de l'appelant.

## Livrable

1. Un fichier `CONTRACT.md` ou un schéma OpenAPI qui documente, pour chaque endpoint : la
   forme exacte de la réponse, les codes d'erreur possibles avec leur `code` machine, et ce
   qui est garanti ne jamais changer dans la version actuelle.
2. L'endpoint de création accepte un header `Idempotency-Key` et prouve, par un test
   automatisé, qu'envoyer deux fois la même requête avec la même clé ne crée qu'une seule
   ressource.
3. L'endpoint de liste est paginé (curseur ou offset, au choix, mais justifié par écrit dans
   `CONTRACT.md` selon le critère de volume de ce niveau).
4. Un mécanisme d'autorisation qui distingue au moins deux identités différentes avec des
   périmètres différents (par exemple : un client qui ne voit que ses propres rendez-vous,
   un vétérinaire qui voit tous les rendez-vous de sa clinique), prouvé par un test qui vérifie
   qu'une identité A ne peut pas lire les données réservées à une identité B.
5. Un paragraphe qui explique la stratégie de versionnage choisie et donne un exemple concret
   de changement futur qui, selon cette stratégie, nécessiterait une nouvelle version.

## Critères de réussite mesurables

- Le test de double soumission avec `Idempotency-Key` identique passe et prouve, en
  interrogeant la base ou le store de données, qu'une seule ressource existe après les deux
  appels.
- Le test d'autorisation croisée échoue clairement (403 ou 404 selon la politique choisie et
  justifiée) quand l'identité A tente d'accéder à une ressource de l'identité B.
- L'endpoint de liste refuse une taille de page au-delà d'un maximum documenté (teste-le en
  demandant explicitement une taille excessive et vérifie la réponse).
- Chaque code d'erreur retourné par l'API a un `code` stable documenté dans `CONTRACT.md`,
  vérifiable en recoupant le code source et la documentation (`grep` sur les deux).
- Le paragraphe de versionnage cite un exemple concret et discriminant (pas "on versionnera
  si besoin"), qui montre que tu sais reconnaître un changement rétrocompatible d'un
  changement qui ne l'est pas.

## Ce que ce challenge ne demande pas

Il ne demande pas d'implémenter un rate limiting distribué en production ni un cache HTTP
complet avec invalidation : ces mécanismes se démontrent en boss fight sur un cas de
décision, pas nécessairement en code complet ici. Il demande de prouver, par des tests qui
passent et échouent aux bons endroits, que tu maîtrises le socle : contrat écrit, idempotence
réelle, autorisation vérifiée, pagination bornée. Une API "sécurisée" sur le papier sans test
qui le prouve ne vaut rien de plus qu'une API qui n'a jamais pensé à la question.
