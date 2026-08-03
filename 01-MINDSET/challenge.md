# Challenge : Note de conception + ADR sur ton projet fil rouge

## Contexte

Tu as choisi un projet fil rouge au Niveau 00. Ce projet n'a probablement encore aucune
ligne de code, et c'est très bien : ce challenge se joue entièrement à l'écrit, avant toute
implémentation. L'objectif n'est pas de trancher la meilleure architecture possible, mais de
prouver que tu sais raisonner et écrire une décision qui résiste à la contradiction.

Si ton projet fil rouge te semble trop simple pour contenir une vraie décision difficile,
c'est un signal : cherche la décision qui deviendra difficile dans six mois, pas celle qui
est facile aujourd'hui. Exemple : "comment on identifie un utilisateur" semble trivial au
jour 1, mais devient une décision lourde dès qu'il faut gérer les comptes partagés, les
invités, ou la suppression de compte.

## Ce qu'on attend de toi

Choisis, sur ton projet fil rouge, une décision technique ou produit qui remplit ces trois
conditions :

- elle a au moins deux options sérieusement défendables (pas une option évidente et un
  épouvantail) ;
- elle est coûteuse à défaire une fois prise (règle du Niveau 01, leçon 03) ;
- elle repose sur au moins une hypothèse non vérifiée (leçon 04).

Livre deux documents distincts, dans un dossier `decisions/` de ton projet :

### 1. Une note de conception (`design-doc-XX.md`)

Doit contenir, dans cet ordre :

- **Problème** : ce qui est observé ou anticipé, pour qui, avec quel impact si rien n'est
  fait.
- **Contraintes** : ce qui est non négociable (délai, budget, compétences de l'équipe,
  contraintes techniques déjà en place).
- **Non-objectifs** : au moins deux choses que cette note ne cherche pas à résoudre.
- **Hypothèses** : au moins une hypothèse formulée avec un seuil chiffré (leçon 04), et le
  moyen le moins cher de la vérifier avant de s'engager complètement.
- **Critères de succès** : comment tu sauras, dans trois mois, que la décision était la
  bonne.

### 2. Un ADR (`ADR-XX-titre.md`)

Doit contenir, dans cet ordre :

- **Statut** et date.
- **Contexte** : peut reprendre le problème de la note de conception, en plus court.
- **Options considérées** : au minimum trois options réelles, chacune avec son coût et son
  bénéfice : pas de version caricaturée pour faire gagner ton option préférée d'avance.
- **Décision** : l'option choisie, en une phrase.
- **Justification** : pourquoi celle-là précisément, en distinguant explicitement les faits
  vérifiables des jugements assumés (leçon 05).
- **Conséquences** : ce que tu acceptes de perdre, et la condition observable qui devrait te
  faire revenir sur cette décision.

## Critères de réussite mesurables

Ton livrable est validé si, et seulement si :

1. La note de conception contient au moins une hypothèse avec un seuil chiffré et une
   expérience concrète, réalisable en moins d'une journée, pour la tester : pas "on
   demandera aux utilisateurs" sans préciser comment ni à combien.
2. L'ADR présente au moins trois options, et pour chacune un coût _et_ un bénéfice réels —
   si une option n'a aucun bénéfice listé, elle est probablement un épouvantail à retirer.
3. La section "Conséquences" de l'ADR contient une condition de révision observable et
   vérifiable (un seuil, un événement, une métrique) : pas "on réévaluera si besoin".
4. Un tiers qui n'a jamais discuté du projet avec toi peut lire les deux documents et
   reconstituer, sans te poser de question, pourquoi tu as choisi cette option plutôt que les
   deux autres.
5. Aucun des deux documents ne dépasse une page A4 imprimée (environ 500 mots) : la
   contrainte de longueur force à couper le remplissage et garder l'essentiel.

## Comment savoir si tu as raté

Si tu peux répondre "peu importe, on aurait fait pareil de toute façon" à la question "qu'est-ce
qui aurait dû se passer pour que tu choisisses une autre option", ton ADR ne documente pas une
vraie décision : il documente une évidence. Recommence avec une décision qui avait réellement
deux issues possibles.
