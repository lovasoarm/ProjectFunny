# Challenge : Autopsie et modification d'un dépôt inconnu

## Consigne générale

Ce challenge a deux temps, dans un ordre strict : d'abord la carte, ensuite le patch.
Contrainte non négociable : **aucune ligne de code modifiée avant que la carte soit écrite
et datée.** Si tu touches au code avant d'avoir un fichier de carte horodaté, le challenge
est invalide, même si le patch final est correct : la compétence évaluée est la méthode
d'entrée, pas le résultat.

## Choix du dépôt

Choisis un des trois dépôts réels ci-dessous, ou un dépôt équivalent en taille et en
difficulté si tu veux pratiquer sur un domaine qui te motive plus. Vérifie toi-même la
taille exacte avec `cloc` ou `git ls-files | wc -l` avant de t'engager : les ordres de
grandeur ci-dessous datent de la rédaction de ce document et bougent avec chaque release.

| Dépôt                                             | Domaine                                              | Taille (ordre de grandeur)                       | Difficulté |
| -------------------------------------------------- | ----------------------------------------------------- | --------------------------------------------------- | ---------- |
| `plausible/analytics` (github.com/plausible/analytics) | Web analytics respectueux de la vie privée, Elixir/Phoenix + un peu de TypeScript | environ 40 000 à 60 000 lignes applicatives, quelques centaines de fichiers | Facile a moyenne : stack unique, périmètre métier resserré |
| `go-gitea/gitea` (github.com/go-gitea/gitea)       | Forge Git auto-hébergée (dépôts, PR, CI légère), Go   | plusieurs centaines de milliers de lignes, plusieurs milliers de fichiers | Moyenne a difficile : nombreux sous-domaines (auth, git, issues, packages) |
| `calcom/cal.com` (github.com/calcom/cal.com)       | Plateforme de prise de rendez-vous en ligne, monorepo TypeScript (Next.js, tRPC, Prisma) | monorepo de plusieurs centaines de milliers de lignes réparties en dizaines de packages | Difficile : monorepo, beaucoup d'indirection entre packages |

Ces trois dépôts sont réels, actifs, avec un historique git de plusieurs années : ils
conviennent tous à l'exercice. N'en choisis pas un que tu as déjà ouvert ou auquel tu as déjà
contribué : le challenge évalue ta méthode d'entrée dans l'inconnu, pas ta mémoire.

## Partie 1 : cartographie chronométrée (3 heures maximum)

Fixe-toi une mission concrète avant de démarrer le chronomètre, par exemple : "je veux
comprendre comment ce projet empêche deux réservations simultanées sur le même créneau" ou
"je veux localiser où et comment ce projet calcule les permissions d'accès à un dépôt privé".

Applique les 5 phases de [02-read-a-codebase.md](02-read-a-codebase.md), chronomètre en main.
Produis un fichier `carte.md`, daté et horodaté au format suivant en première ligne :

```text
Depot : <nom exact du depot et URL>
Mission : <une phrase>
Debut : 2026-03-04 09:00
Fin   : 2026-03-04 12:00
```

Le fichier `carte.md` contient ensuite, dans l'ordre :

1. Le schéma ASCII des grands domaines du dépôt (phase 1).
2. Le flux de bout en bout tracé pour ta mission (phase 2), avec chemins de fichiers exacts.
3. Trois zones à risque (phase 3), chacune justifiée par une preuve vérifiable (résultat de
   `git log --stat`, absence de fichier de test dans le dossier concerné, taille de fonction
   mesurée).
4. Une contrainte reconstruite (méthode de
   [03-reverse-engineer-decisions.md](03-reverse-engineer-decisions.md)) : un design qui t'a
   semblé étrange, ton hypothèse, et la preuve trouvée dans l'historique ou les commentaires
   qui la confirme ou l'infirme.

**Point de contrôle obligatoire** : une fois `carte.md` écrit, valide-le en committant ce
seul fichier avec un message de commit horodaté, ou en notant l'heure exacte de fin de
rédaction si tu ne commits pas dans le dépôt cible. C'est cet horodatage qui prouve que la
carte précède la modification. Sans cette preuve, la partie 2 n'est pas recevable.

## Partie 2 : une modification réelle, livrée en patch

Choisis, dans la carte que tu viens d'écrire, une des zones à risque ou une petite
amélioration légitime (correction d'un message d'erreur peu clair, ajout d'un cas de test
manquant sur un comportement déjà identifié, correction d'un bug mineur documenté dans les
issues du dépôt). Le changement doit être petit : l'objectif est la méthode de modification
sûre, pas l'ampleur de la contribution.

Applique la méthode de [05-modifier-sans-casser.md](05-modifier-sans-casser.md) et livre les
quatre éléments suivants, dans un fichier `patch.md` accompagné du diff réel :

1. **Rayon d'impact écrit avant le patch** : liste des appelants trouvés (avec chemins
   exacts), liste des tests existants qui touchent la zone, et mention explicite de toute
   donnée déjà écrite que ton changement pourrait rendre incohérente (même si la réponse est
   "aucune donnée concernée, parce que...").
2. **Le patch lui-même**, sous forme de diff (`git diff` ou fichier `.patch`), le plus petit
   possible pour l'effet recherché, sans nettoyage ni renommage mêlés au correctif.
3. **Un test de non-régression** que tu as ajouté ou identifié comme suffisant, avec la
   preuve qu'il échouait avant ton patch (si tu corriges un bug) ou qu'il continue de passer
   après (si tu ajoutes un comportement sans changer l'existant).
4. **Le résultat d'exécution de la suite de tests existante sur la zone touchée**, avant et
   après ton patch, collé tel quel (pas résumé de mémoire).

Tu n'as pas besoin d'ouvrir une vraie pull request sur le dépôt upstream pour valider ce
challenge : un fork local avec un commit propre et les livrables `carte.md` et `patch.md`
suffisent. Si tu choisis d'ouvrir une vraie PR, mentionne son lien dans `patch.md`.

## Contrainte non négociable

Aucune ligne de code modifiée avant que `carte.md` soit écrit et daté. Cette contrainte n'est
pas une formalité : elle force la séparation entre comprendre et agir, qui est exactement la
compétence que ce niveau construit. Un patch correct produit sans carte préalable ne valide
pas le challenge, même s'il fonctionne.

## Critères de réussite mesurables

- `carte.md` existe, porte une date et une heure de début et de fin, et l'écart entre les
  deux ne dépasse pas 3 heures (un léger dépassement documenté et justifié est toléré, un
  dépassement non signalé invalide le challenge).
- Le flux tracé cite au moins 4 fichiers réels avec leur chemin exact dans le dépôt.
- Au moins une des trois zones à risque est appuyée par une commande exécutée et son résultat
  cité mot pour mot, pas une impression.
- La contrainte reconstruite est confirmée ou infirmée par une preuve citée (message de
  commit, ticket, commentaire de code), pas une pure supposition.
- Le rayon d'impact de `patch.md` liste au moins un appelant réel et au moins un test
  existant, avec leurs chemins exacts, avant de montrer le diff.
- Le diff modifie le plus petit nombre de fichiers possible pour l'effet recherché : plus de
  3 fichiers touchés pour un correctif ponctuel doit être justifié explicitement.
- La suite de tests de la zone touchée est exécutée et son résultat collé, avant et après le
  patch, pas seulement après.

## Ce qui invalide le challenge

- Modifier une seule ligne de code avant que `carte.md` soit écrit et daté.
- Choisir un dépôt que tu connais déjà ou auquel tu as déjà contribué.
- Un rayon d'impact qui ne liste aucun appelant réel trouvé par une recherche exécutée.
- Un patch qui mélange correction fonctionnelle et nettoyage ou renommage.
- Une contrainte "reconstruite" sans aucune preuve citée : une hypothèse non vérifiée n'est
  pas une reconstruction, c'est une divination.
