# Niveau 14 : Tool Cave

## Ce que c'est

Treize niveaux t'ont appris à penser un système. Ce niveau t'apprend à tenir tes outils —
éditeur, shell, debugger, IA : comme des extensions de ta main plutôt que comme des boîtes
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

## Ce que tu sais faire à la sortie

- Tu configures ton éditeur et ton shell pour qu'ils travaillent pour toi, pas contre toi —
  et tu sais expliquer pourquoi chaque réglage existe, pas seulement le copier.
- Tu appliques une méthode de debug reproductible (bissection, hypothèses, logs ciblés,
  profiling) au lieu de changer du code au hasard en espérant que ça passe.
- Tu sais quand un profiler ou un outil de mesure te dira la vérité, et quand il te ment par
  omission (échantillonnage, environnement non représentatif).
- Tu utilises un assistant IA comme un collaborateur junior rapide qu'il faut vérifier, pas
  comme un oracle : et tu sais formuler les prompts qui forcent une décision plutôt qu'une
  réponse vague.
- Tu sais repérer, dans du code produit par IA ou par toi-même sous pression, les signaux
  qui doivent déclencher une relecture approfondie.

## Structure du niveau

```text
14-TOOL-CAVE/
├── 01-why-this-level.md      → pourquoi la maîtrise d'outil est un multiplicateur, pas un détail
├── 02-editor-and-shell.md    → éditeur et shell comme prothèses cognitives
├── 03-debugging-toolkit.md   → méthode de debug, bissection, profilers, logs
├── 04-ai-as-a-tool.md        → utiliser l'IA sans perdre son cerveau
├── challenge.md               → exercice appliqué avec livrable mesurable
└── grimoire.md                → mémo dense
```

## Comment lire ce niveau

`01` pose le diagnostic : pourquoi deux développeurs de niveau égal en algorithmique livrent
à des vitesses très différentes. `02` et `03` couvrent l'outillage classique : éditeur,
shell, debug : dans l'ordre où tu les utilises réellement un mauvais jour de bug. `04`
traite l'outil le plus récent et le plus mal compris du métier : l'IA générative, et la
discipline qu'elle exige pour ne pas dégrader ton jugement.

## Ce qui ne se passe pas ici

Ce niveau ne compare pas VSCode à Vim, ni GPT à un concurrent. Les guerres d'outils sont un
bruit qui masque la vraie question : est-ce que ta méthode de travail est reproductible et
transmissible, quel que soit l'outil que tu tiens dans la main.
