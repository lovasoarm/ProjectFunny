[Sommaire](../CURRICULUM.md) | [Niveau precedent](../13-DAY-TO-LEGEND/README.md) | [Niveau suivant](../15-BONUS-VAULT/README.md)

# Niveau 14 : Tool Cave

## Ce que c'est

Treize niveaux t'ont appris à penser un système. Ce niveau t'apprend à tenir tes outils :
éditeur, shell, debugger, IA, comme des extensions de ta main plutôt que comme des boîtes
noires que tu subis. La différence entre un développeur qui perd vingt minutes à chercher où
est le bug et un développeur qui le trouve en deux minutes n'est presque jamais une
différence de connaissance du langage. C'est une différence de maîtrise d'outillage.

Ce niveau ne te vend aucune stack particulière. Les raccourcis clavier, les plugins, les
IDE changent tous les deux ans. Les méthodes de debug, la logique d'un shell, et la posture
correcte face à un outil d'IA générative restent vraies bien plus longtemps. C'est ce qu'on
travaille ici.

Durée estimée : 1 à 2 semaines, avec pratique quotidienne courte plutôt que sessions longues
espacées : la maîtrise d'outil se construit par répétition, pas par lecture.

Prérequis : avoir livré au moins un projet avec un bug non trivial (Niveau 8 ou plus). Sans
un vrai bug vécu, la méthode de debug de ce niveau reste abstraite.

Ce niveau réutilise : la méthode de reproduction et d'isolation posée au
[Niveau 8](../08-ROADMAP-RUN/README.md) (si le bug n'est pas reproductible, aucune méthode
d'outillage ne compense), et la notion de compromis assumé introduite au
[Niveau 13](../13-DAY-TO-LEGEND/README.md).

Auto-test d'entrée :
1. Tu as un bug qui ne se reproduit qu'une fois sur dix. Quelle est ta première étape avant
   toute investigation ?
2. Une IA générative te renvoie une fonction de calcul sans jamais te demander de préciser
   une règle métier ambiguë que tu n'avais pas donnée. Que fait-elle à la place de demander ?
3. Un profiler te dit qu'une fonction prend 2% du temps total. Dans quelles conditions ce
   chiffre peut-il te mentir par omission ?

Temps de lecture : 35 minutes.

## Ce que tu sais faire à la sortie

- Tu configures ton éditeur et ton shell pour qu'ils travaillent pour toi, pas contre toi,
  et tu sais expliquer pourquoi chaque réglage existe, pas seulement le copier.
- Tu appliques une méthode de debug reproductible (bissection, hypothèses, logs ciblés,
  profiling) au lieu de changer du code au hasard en espérant que ça passe.
- Tu sais quand un profiler ou un outil de mesure te dira la vérité, et quand il te ment par
  omission (échantillonnage, environnement non représentatif).
- Tu utilises un assistant IA comme un collaborateur junior rapide qu'il faut vérifier, pas
  comme un oracle, et tu sais formuler les prompts qui forcent une décision plutôt qu'une
  réponse vague.
- Tu sais repérer, dans du code produit par IA ou par toi-même sous pression, les signaux
  qui doivent déclencher une relecture approfondie, et démonter par écrit l'hypothèse
  silencieuse cachée dans une réponse plausible.

## Structure du niveau

- [01-why-this-level.md](01-why-this-level.md) : pourquoi la maîtrise d'outil est un
  multiplicateur, pas un détail.
- [02-editor-and-shell.md](02-editor-and-shell.md) : éditeur et shell comme prothèses
  cognitives.
- [03-debugging-toolkit.md](03-debugging-toolkit.md) : méthode de debug, bissection,
  profilers, logs.
- [04-ai-as-a-tool.md](04-ai-as-a-tool.md) : utiliser l'IA sans perdre son cerveau.
- [05-audit-dune-reponse-ia.md](05-audit-dune-reponse-ia.md) : démonter par écrit
  l'hypothèse silencieuse d'une réponse IA plausible et fausse.
- [challenge.md](challenge.md) : exercice appliqué avec livrable mesurable.
- [boss-fight.md](boss-fight.md) : incident réel à 3h du matin, une seule tentative, aucun
  accès IA.
- [grimoire.md](grimoire.md) : mémo dense, aligné sur la grille du boss-fight.

## Comment lire ce niveau

`01` pose le diagnostic : pourquoi deux développeurs de niveau égal en algorithmique livrent
à des vitesses très différentes. `02` et `03` couvrent l'outillage classique : éditeur,
shell, debug, dans l'ordre où tu les utilises réellement un mauvais jour de bug. `04` et `05`
traitent l'outil le plus récent et le plus mal compris du métier : l'IA générative, la
discipline qu'elle exige, et l'entraînement à repérer ses hypothèses silencieuses avant
qu'elles ne deviennent un incident. Le `boss-fight` retire l'IA de l'équation et te met face
à un incident réel où seule ta méthode compte.

## Ce qui ne se passe pas ici

Ce niveau ne compare pas VSCode à Vim, ni GPT à un concurrent. Les guerres d'outils sont un
bruit qui masque la vraie question : est-ce que ta méthode de travail est reproductible et
transmissible, quel que soit l'outil que tu tiens dans la main.
