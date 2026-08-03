# Challenge — Autopsie d'un dépôt inconnu

## Consigne

Choisis un projet open source réel que tu n'as jamais ouvert, avec au minimum 15 000 lignes
de code applicatif (hors dépendances) et au moins deux ans d'historique git actif. Exemples
de familles de projets qui conviennent : un outil de gestion de tickets, un moteur de
réservation, un système de suivi logistique, un CRM léger. Évite les frameworks ou libs
pures (trop orientés API publique, pas assez "métier").

Applique la méthode en 5 phases de `02-read-a-codebase.md` sur ce dépôt, avec un budget
strict de 3 heures chronométrées, en te fixant une mission concrète avant de commencer
(exemple : "j'estime l'ajout d'une fonctionnalité X").

## Livrable

Un document d'une à deux pages contenant :

1. **La mission** que tu t'es fixée avant de commencer (une phrase).
2. **La carte du terrain** (phase 1) : schéma ASCII des grands domaines du dépôt.
3. **Le flux tracé** (phase 2) : un diagramme de bout en bout d'un cas d'usage réel, avec les
   fichiers et fonctions traversés nommés précisément.
4. **Trois zones à risque identifiées** (phase 3), avec la preuve concrète qui te fait dire
   qu'elles sont à risque (nombre de commits, absence de tests, taille de fonction...).
5. **Une contrainte reconstruite** (méthode de `03-reverse-engineer-decisions.md`) : un
   design qui t'a d'abord semblé étrange, avec ton hypothèse de contrainte et la preuve
   trouvée dans l'historique ou les tickets qui la confirme ou l'infirme.
6. **Ton estimation finale** pour la mission fixée, en fourchette, avec les sources
   d'incertitude nommées explicitement.

## Critères de réussite mesurables

- Le budget de 3 heures est respecté et documenté (heure de début, heure de fin).
- Le flux tracé en phase 2 cite au moins 4 fichiers réels avec leur chemin exact dans le
  dépôt, pas une description générique.
- Au moins une des trois zones à risque est justifiée par une donnée vérifiable (une
  commande `git log` exécutée et son résultat cité, pas une impression).
- La contrainte reconstruite en point 5 est confirmée ou infirmée par une source concrète
  (message de commit, ticket lié, commentaire de code cité mot pour mot) — pas une pure
  supposition non vérifiée.
- L'estimation finale est une fourchette (pas un chiffre unique) avec au moins deux sources
  d'incertitude distinctes nommées.

## Ce qui invalide le challenge

- Choisir un dépôt que tu connais déjà ou auquel tu as déjà contribué.
- Dépasser largement le budget de temps sans le signaler — le but est la méthode sous
  contrainte, pas l'exhaustivité.
- Une contrainte "reconstruite" sans aucune preuve citée : une hypothèse non vérifiée n'est
  pas une reconstruction, c'est une divination.
