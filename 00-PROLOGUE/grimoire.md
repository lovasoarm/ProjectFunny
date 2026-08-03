# Grimoire : Niveau 00

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| Livrer | Produire un artefact réel plutôt qu'une conception théorique | Tu accumules des plans jamais confrontés au réel, aucune preuve que ça marche | Pourquoi un artefact imparfait livré vaut mieux qu'un plan parfait non testé ? |
| Mesurer | Écrire le critère d'échec avant de commencer | Tu juges le résultat après coup, avec un critère inventé pour coller au résultat obtenu | Quel est ton seuil d'échec, et pourquoi l'as-tu fixé avant et pas après ? |
| Écrire | Coucher une décision sur papier au moment où elle est prise | Une décision non écrite disparaît de la mémoire collective en six mois, et se redébat sans fin | Que se passe-t-il si la personne qui a pris la décision quitte le projet demain ? |
| Défendre | Pouvoir justifier une décision à l'oral, en direct, sans notes | Une décision indéfendable n'était pas une décision : c'était une intuition non vérifiée | Quelle serait la première question qu'un lead sceptique te poserait sur ce choix ? |
| Jeter | Décider du signal d'abandon avant de lancer le pari | Tu continues un pari perdant par habitude ou par ego, jamais par argument | Quel chiffre observable te ferait arrêter ce projet demain matin ? |
| Documenter la décision | Inclure systématiquement les options rejetées et leur raison de rejet | Le prochain lecteur croit que l'option choisie était la seule envisagée, et refait le même débat | Quelles étaient les deux autres options, et pourquoi les as-tu écartées ? |
| Revenir | Noter la condition qui rendrait la décision obsolète | La décision devient un dogme qu'on n'ose plus questionner, même quand le contexte a changé | Quel événement précis devrait te faire rouvrir ce dossier ? |
| Modèle mental | Comprendre les forces en jeu (concurrence, coût de changement, incertitude) pour analyser un problème jamais vu | Tu sais reconnaître un pattern connu mais tu es perdu face à une situation nouvelle | Explique ce qui casse si on change X, sans relire le code |
| Exécution | Reconnaître un pattern connu et l'appliquer tel quel | Tu appliques une recette hors de son contexte de validité, sans savoir pourquoi elle marchait | Dans quel contexte cette recette cesserait-elle de s'appliquer ? |
| ADR (Architecture Decision Record) | Document court qui fige une décision structurante, ses options rejetées et sa condition de péremption | Les décisions d'architecture se prennent en couloir et se reperdent, chacun a sa version | Qu'est-ce qui rendrait cet ADR caduc, concrètement ? |
| Coûts irrécupérables (sunk cost) | Biais qui pousse à continuer un pari perdant parce qu'on a déjà investi dedans | Tu défends une mauvaise décision uniquement parce que l'abandonner ferait perdre le travail déjà fait | Ce que tu as déjà investi change-t-il la probabilité de succès future ? |

## Comportements évalués au challenge

Ce niveau n'a ni challenge.md ni boss-fight.md (voir README, section Écart au gabarit) : c'est
un sas de lecture, pas un exercice noté. Les comportements ci-dessous reprennent la checklist
"fin de niveau" du grimoire, seul instrument d'auto-évaluation de ce niveau.

| Comportement | Preuve attendue dans ta copie | Signal d'échec |
| --- | --- | --- |
| Justification par un mécanisme | Tu expliques le modèle mental du niveau en 5 minutes sans support, en nommant les forces en jeu (concurrence, coût de changement, incertitude) | Tu redécris le contenu lu mot pour mot, sans pouvoir dire ce qui casse si on change un paramètre |
| Compromis nommé et assumé | Ton ADR ou ta décision liste les options rejetées et pourquoi, pas seulement l'option choisie | Tu présentes une décision comme évidente, sans alternative envisagée ni coût nommé |
| Honnêteté sur ce qu'on ne sait pas | Si tu échoues à un exercice ultérieur, tu sais nommer précisément quelle leçon retravailler, pas "tout le niveau" | Tu affirmes avoir compris sans pouvoir localiser la lacune exacte |

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
- [ ] J'ai confronté mon livrable au boss-fight, pas juste au challenge.
- [ ] Si j'ai échoué, je sais exactement quelle leçon retravailler (pas "tout le niveau").

## Les 4 arcs (rappel)

- Arc I (00-03) Fondations de pensée : modéliser avant de coder.
- Arc II (04-08) Construction : bâtir des frontières qui résistent au changement.
- Arc III (09-12) Système en production : observer, résister aux pannes, mesurer la
  performance, sécuriser.
- Arc IV (13-15) Ingénieur en contexte : influencer une équipe, arbitrer, livrer seul
  sous contrainte réelle.

## Vocabulaire à connaître par coeur

- ADR (Architecture Decision Record) : document court figeant une décision structurante,
  ses options rejetées, et sa condition de péremption.
- Coûts irrécupérables (sunk cost) : biais qui pousse à continuer un pari perdant parce
  qu'on a déjà investi dedans.
- Dette technique volontaire vs subie : voir Niveau 01, leçon 03.
