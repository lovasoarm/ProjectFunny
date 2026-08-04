# Grimoire : Niveau 00, Prologue

Mémo de poche pour les 7 règles du joueur. Ouvre-le avant de prendre une décision sous
pression, pas pour réviser à froid : tu dois pouvoir le relire en deux minutes montre en main.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Livrer | Produire un artefact réel plutôt qu'une conception théorique. Un plan jamais confronté au réel ne compte pas comme un livrable. | `git commit -m "feat: version imparfaite mais testable en vrai"` | passe de commande en cuisine / dépose de matériel au refuge |
| Mesurer | Écrire le critère d'échec avant de commencer, jamais après avoir vu le résultat. | `echo "seuil_echec: taux_adoption < 0.2 au 2026-09-01" >> criteres.yml` | check-list avant de partir en course / feuille de route du régisseur |
| Écrire | Coucher une décision sur papier au moment où elle est prise, pas de mémoire plus tard. | `printf "## %s\nDécision : %s\n" "$(date -I)" "on garde la colonne unique" >> DECISIONS.md` | carnet de bord du navigateur / fiche de poste en régie |
| Défendre | Pouvoir justifier une décision à l'oral, en direct, sans notes ni relecture du code. | `git log -1 --format="%s%n%b" HEAD` | debrief après le service / point météo avant de partir en cordée |
| Jeter | Fixer le signal d'abandon avant de lancer le pari, pas quand il est déjà en train d'échouer. | `printf "signal_abandon: cout > 3x_estimation\n" >> pari.yml` | rebrousser chemin avant l'orage / retirer un plat de la carte |
| Documenter la décision | Inclure les options rejetées et leur raison de rejet, pas seulement l'option choisie. | `printf "## Options rejetées\n- B : rejetée, coût x3\n" >> ADR-001.md` | carte des recettes écartées en cuisine / plan de navigation alternatif refusé |
| Revenir | Noter la condition précise qui rendrait la décision obsolète. | `printf "condition_revision: si volume > 10k/jour\n" >> ADR-001.md` | seuil de rappel du guide de course / jauge de sécurité en régie |
| Modèle mental | Comprendre les forces en jeu (concurrence, coût de changement, incertitude) pour analyser une situation jamais vue. | `printf "forces: [concurrence, cout_changement, incertitude]\n"` | lecture du terrain par le chef de cordée / lecture de la houle par le navigateur |
| Exécution | Reconnaître un pattern connu et l'appliquer tel quel, sans en comprendre les limites. | `cp gabarit-adr.md ADR-004-titre.md` | recette suivie à la lettre en cuisine / geste technique répété sans varier |
| ADR | Document court qui fige une décision structurante, ses options rejetées, sa condition de péremption. | `git mv brouillon-decision.md docs/adr/ADR-004-titre.md` | fiche de sécurité affichée en régie / journal de bord du bateau |

## Défense orale

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Livrer | Tu accumules des plans jamais confrontés au réel, aucune preuve que ça marche | Pourquoi un artefact imparfait livré vaut mieux qu'un plan parfait non testé ? |
| Mesurer | Tu juges le résultat après coup, avec un critère inventé pour coller au résultat obtenu | Quel est ton seuil d'échec, et pourquoi l'as-tu fixé avant et pas après ? |
| Écrire | Une décision non écrite disparaît de la mémoire collective en six mois, et se redébat sans fin | Que se passe-t-il si la personne qui a pris la décision quitte le projet demain ? |
| Défendre | Une décision indéfendable n'était pas une décision : c'était une intuition non vérifiée | Quelle serait la première question qu'un lead sceptique te poserait sur ce choix ? |
| Jeter | Tu continues un pari perdant par habitude ou par ego, jamais par argument | Quel chiffre observable te ferait arrêter ce projet demain matin ? |
| Documenter la décision | Le prochain lecteur croit que l'option choisie était la seule envisagée, et refait le même débat | Quelles étaient les deux autres options, et pourquoi les as-tu écartées ? |
| Revenir | La décision devient un dogme qu'on n'ose plus questionner, même quand le contexte a changé | Quel événement précis devrait te faire rouvrir ce dossier ? |
| Modèle mental | Tu sais reconnaître un pattern connu mais tu es perdu face à une situation nouvelle | Explique ce qui casse si on change X, sans relire le code |
| Exécution | Tu appliques une recette hors de son contexte de validité, sans savoir pourquoi elle marchait | Dans quel contexte cette recette cesserait-elle de s'appliquer ? |
| ADR | Les décisions d'architecture se prennent en couloir et se reperdent, chacun a sa version | Qu'est-ce qui rendrait cet ADR caduc, concrètement ? |

Ce niveau n'a ni challenge.md ni boss-fight.md (voir README, section Écart au gabarit) : c'est
un sas de lecture. La checklist "fin de niveau" ci-dessous fait office d'auto-évaluation.

## Les 7 règles du joueur (rappel dense)

1. Livrer : un artefact réel bat une conception théorique.
2. Mesurer : écris le critère d'échec avant de commencer.
3. Écrire : une décision non écrite n'existe pas dans six mois.
4. Défendre : si tu ne peux pas la défendre à l'oral, ce n'est pas une décision.
5. Jeter : décide du signal d'abandon avant de lancer le pari, pas après.
6. Documenter la décision : inclus toujours les options rejetées et pourquoi.
7. Revenir : note la condition qui rendrait la décision obsolète.

## Différence exécution / modèle mental

- Exécution : reconnaître un pattern connu et l'appliquer.
- Modèle mental : comprendre les forces en jeu (concurrence, coût de changement,
  incertitude) pour analyser un problème jamais vu.
- Signal de stagnation : incapable de répondre à "qu'est-ce qui casse si on change X"
  sans relire le code.

## Checklist "fin de niveau"

- [ ] Je peux expliquer le modèle mental du niveau en 5 minutes sans support.
- [ ] J'ai un livrable concret, daté, versionné.
- [ ] J'ai une trace écrite d'au moins une décision défendable.
- [ ] J'ai confronté mon livrable au boss-fight du niveau suivant, pas juste au challenge.
- [ ] Si j'ai échoué, je sais exactement quelle leçon retravailler (pas "tout le niveau").

## Les 4 arcs (rappel)

- Arc I (00-03) Fondations de pensée : modéliser avant de coder.
- Arc II (04-08) Construction : bâtir des frontières qui résistent au changement.
- Arc III (09-12) Système en production : observer, résister aux pannes, mesurer la
  performance, sécuriser.
- Arc IV (13-15) Ingénieur en contexte : influencer une équipe, arbitrer, livrer seul
  sous contrainte réelle.

## Si tu rates le boss-fight

Ce niveau n'a pas de boss-fight noté : la sanction, c'est d'arriver mal préparé au niveau 01.
Si tu ne peux pas répondre en cinq minutes à "qu'est-ce qui casse si on change X" sur ta propre
décision, relis les 7 règles ci-dessus et le vocabulaire ADR / coûts irrécupérables. Refais
l'exercice d'écriture d'une décision réelle (la tienne, pas un exemple du cours) avant de
passer au niveau 01. Donne-toi 48 h avant de retenter l'exercice, pas moins : le recul compte
autant que la relecture. Si après une deuxième tentative tu ne sais toujours pas nommer une
option rejetée et sa raison, le blocage n'est pas ce niveau : c'est l'habitude de décider sans
écrire, à corriger avant d'avancer.
