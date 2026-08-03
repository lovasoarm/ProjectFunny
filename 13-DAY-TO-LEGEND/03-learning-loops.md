# Boucles de feedback, pratique délibérée, sortir des plateaux

## Le piège

Tu tiens ta routine depuis huit semaines : lecture, katas, journal. Tu sens que tu ne
progresses plus. Les katas te semblent faciles, mais tu n'es pas meilleur pour autant sur
tes vrais tickets. Tu te dis que la méthode ne marche pas et tu abandonnes. En réalité, tu
es tombé dans le piège le plus classique de la pratique délibérée : tu t'entraînes en boucle
fermée, sans jamais confronter tes réponses à un retour extérieur fiable. Une boucle fermée
ne peut pas te faire progresser au-delà de ce que tu savais déjà — elle ne fait que te
rendre plus rapide à produire les mêmes erreurs.

## Ce qui se passe vraiment

### Anatomie d'une boucle de feedback qui marche

```text
  Action ──> Résultat observable ──> Comparaison à une référence ──> Correction
    ↑                                                                    │
    └────────────────────────────────────────────────────────────────┘

  Une boucle FERMÉE (sans référence externe) :
  Action ──> Résultat que tu juges toi-même ──> "ça a l'air bien" ──> Répétition à l'identique
  → aucune correction possible si ton propre jugement contient l'erreur.
```

La qualité de ta progression est bornée par la qualité de ta référence de comparaison, pas
par le nombre de répétitions. Cent répétitions d'un kata sans jamais comparer à une réponse
experte t'entraînent à répéter vite ton propre biais.

Sources de référence fiables, du plus fort au plus faible :

1. Un résultat de production mesuré (le bug est réapparu ou non, la performance a changé ou
   non) — la référence la plus dure, incontestable, mais lente à obtenir.
2. Une revue par quelqu'un plus expérimenté que toi sur le sujet précis (pas n'importe qui).
3. Une comparaison avec un cas documenté (ADR réel, post-mortem public, code source d'un
   projet reconnu).
4. Ta propre relecture à froid, une semaine plus tard — meilleure que rien, mais tu restes
   juge et partie.

### Pourquoi les plateaux apparaissent

Un plateau de progression a presque toujours l'une de ces trois causes, rarement un manque
de travail :

```text
Cause 1 — Zone de confort déguisée en effort
  Tu répètes des katas que tu maîtrises déjà à 90%. Sensation de travail, gain quasi nul.
  Symptôme : tu termines chaque kata sans avoir hésité une seule fois.

Cause 2 — Absence de référence de correction
  Tu t'entraînes en boucle fermée (voir plus haut). Tu deviens plus rapide, pas meilleur.
  Symptôme : ta vitesse augmente, la qualité jugée par un tiers externe reste stable.

Cause 3 — Mauvais niveau de difficulté (trop dur, pas trop facile)
  Le problème choisi dépasse tellement ton niveau actuel que tu ne peux even pas
  identifier ce qui a raté — le retour ne t'apprend rien d'exploitable.
  Symptôme : tu abandonnes le kata avant la fin, frustration sans compréhension.
```

La zone d'apprentissage efficace se situe entre les deux : juste au-delà de ce que tu sais
déjà faire confortablement, jamais dans l'inconnu total.

```text
Trop facile        Zone d'apprentissage        Trop difficile
──────────────  |  ────────────────────────  |  ──────────────
Confort, ennui  |  Inconfort gérable,          |  Frustration,
zéro gain       |  progrès mesurable           |  aucun gain exploitable
```

### Comment casser un plateau concrètement

1. Change la source de référence, pas l'exercice. Si tu t'entraînais en autocorrection,
   trouve une revue humaine — même informelle, même ponctuelle (un post sur un forum
   technique sérieux, un pair d'un meetup, un mentor payé une heure).
2. Augmente légèrement la contrainte du kata, pas sa nature. Passe de "15 minutes sans
   contrainte de format" à "15 minutes en devant citer un chiffre ou une mesure à l'appui de
   la décision" — cela force une nouvelle capacité (chiffrer) sans tout changer.
3. Change de domaine d'application temporairement. Si tes katas de décision portent
   toujours sur des APIs, fais-en trois semaines sur des décisions de modélisation de
   données. Le transfert de compétence entre domaines proches révèle souvent des lacunes
   masquées par la familiarité d'un seul domaine.
4. Accepte qu'un plateau de quelques semaines est normal et ne veut rien dire seul. Le
   signal d'alarme n'est pas "je stagne cette semaine", c'est "je stagne depuis deux mois
   malgré une routine tenue et une référence externe utilisée".

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Boucle fermée (autocorrection) | Aucun coût logistique | Progrès rapide au début, plafonne vite | Phase de démarrage, avant d'avoir accès à un mentor ou une communauté |
| Boucle ouverte avec revue humaine ponctuelle | Coût social (demander, parfois payer), délai | Correction d'angles morts invisibles seul | Dès que la boucle fermée plafonne (généralement 4-8 semaines) |
| Boucle ouverte avec production réelle mesurée | Lent, dépend du contexte pro | Référence la plus fiable qui existe | En continu, en parallèle de tout le reste, via le travail quotidien lui-même |
| Changer de domaine d'application temporairement | Sensation de repartir de zéro | Révèle des lacunes masquées par la routine, réactive la progression | Dès qu'un plateau dure plus de 4-6 semaines |

## Pièges classiques

- **Le faux plateau de la première semaine.** Symptôme : tu juges "ça ne marche pas" après
  cinq jours. La pratique délibérée montre des effets mesurables à partir de 4-6 semaines,
  pas avant — juger plus tôt, c'est juger dans le bruit.
- **La revue humaine complaisante.** Symptôme : tu demandes toujours à la même personne
  bienveillante qui te dit "c'est bien" sans jamais pointer un défaut précis. Une bonne
  référence de correction doit parfois te déplaire.
- **Le kata qui devient un rituel vide.** Symptôme : tu fais le kata machinalement, sans
  chronométrer, sans vraiment trancher — tu coches une case plutôt que de t'entraîner.
- **Confondre inconfort productif et souffrance inutile.** Symptôme : tu choisis
  systématiquement le kata le plus dur du pool pour "prouver" que tu travailles dur, tu n'en
  retires rien d'exploitable et tu finis par arrêter par épuisement.

## Ce que tu dois savoir défendre

- Explique pourquoi une boucle de feedback fermée ne peut pas te faire dépasser ton niveau
  actuel, même avec des centaines de répétitions.
- Cite les trois causes les plus fréquentes d'un plateau de progression, et pour chacune une
  action concrète pour en sortir.
- Décris ta propre "zone d'apprentissage efficace" actuelle : un exercice trop facile pour
  toi aujourd'hui, et un exercice probablement trop difficile.
