# Utiliser l'IA sans perdre son cerveau

## Le piège

Tu dois refacturer la consommation d'énergie d'un immeuble collectif entre plusieurs
locataires, avec des tarifs différents selon les tranches horaires et un chauffage commun à
répartir au prorata de la surface. Tu demandes à un assistant IA de générer la fonction de
calcul. Elle sort en trois secondes, bien formatée, avec des noms de variables clairs. Tu la
colles, les tests unitaires basiques passent, tu livres. Trois semaines plus tard, un
locataire conteste sa facture : la fonction répartit le chauffage commun par nombre
d'occupants au lieu de par surface, une hypothèse plausible mais fausse pour ce cas précis,
que l'IA a choisie silencieusement faute de précision dans ta demande. Tu n'as jamais relu
la logique : seulement vérifié qu'elle "avait l'air" correcte.

## Ce qui se passe vraiment

Un assistant IA générative n'est ni un oracle ni un simple autocomplete. Le modèle mental le
plus utile est celui d'un développeur junior extraordinairement rapide, qui a lu une bonne
partie du code jamais écrit sur Terre, mais qui n'a aucune idée du contexte réel de ton
projet, ne sait jamais dire honnêtement "je ne suis pas sûr", et invente une hypothèse
plausible plutôt que de te demander une clarification.

```text
# verifie le 2026-08-04 : contenu a revoir a chaque saut de generation de modele d'IA
Développeur junior humain              Assistant IA générative
────────────────────────────           ─────────────────────────────────
Peut dire "je ne sais pas,        vs    Produit toujours une réponse,
je dois vérifier"                       même sans base solide dessous

Pose une question de clarification vs   Choisit une interprétation plausible
si l'énoncé est ambigu                  et l'exécute sans le signaler

Apprend le contexte du projet      vs   Repart de zéro à chaque session,
au fil des semaines                     sans mémoire du projet réel

Sa vitesse est limitée              vs  Sa vitesse peut masquer une absence
                                         totale de vérification derrière elle
```

Le danger n'est pas que l'IA se trompe : tout collaborateur se trompe. Le danger est que sa
vitesse et la fluidité de sa réponse créent une confiance qui n'est pas corrélée à
l'exactitude. Un humain qui hésite te donne un signal (il hésite). Une IA qui invente une
règle de répartition d'énergie inventée le fait avec la même confiance apparente qu'une
réponse correcte.

### Principe central : déplacer l'effort vers la vérification, jamais l'éliminer

```text
Sans discipline de vérification :
Effort de production   →→→→ (quasi nul, l'IA écrit)
Effort de vérification →     (quasi nul aussi, "ça a l'air bien")
= Risque non détecté élevé

Avec discipline de vérification :
Effort de production   →     (quasi nul, l'IA écrit)
Effort de vérification →→→→ (déplacé ici, volontairement)
= Gain de temps réel, risque maîtrisé
```

Le gain de temps de l'IA n'est réel que si l'effort économisé en production est réinvesti en
vérification : pas s'il est simplement supprimé du budget total.

### Prompts de décision plutôt que prompts de génération directe

La qualité d'une réponse d'IA dépend directement de la précision de la question. Un prompt
qui demande une décision force le modèle à exposer ses hypothèses au lieu de les cacher dans
du code qui a l'air fini.

```text
Prompt de génération directe (risqué)
──────────────────────────────────────
"Écris une fonction qui répartit le coût de chauffage commun entre les locataires."
→ L'IA choisit silencieusement une règle de répartition plausible.

Prompt de décision (plus sûr)
──────────────────────────────────────
"Je dois répartir un coût de chauffage commun entre locataires. Liste-moi les
règles de répartition possibles (par surface, par occupants, par relevé individuel,
mixte), avec pour chacune un cas où elle serait injuste, avant d'écrire du code."
→ L'IA doit exposer ses options et leurs limites : tu choisis en connaissance de cause,
   et tu détectes une hypothèse fausse avant qu'elle ne soit enterrée dans du code.
```

### Revue critique : la checklist minimale avant de coller du code généré

1. **Est-ce que je pourrais expliquer cette logique à un collègue sans relire le code ?**
   Si non, tu ne l'as pas encore comprise : tu ne peux pas la maintenir ni la déboguer plus
   tard.
2. **Quelle hypothèse implicite ce code fait-il sur les données d'entrée ?** (formats,
   valeurs nulles, ordres de grandeur) : l'IA choisit rarement l'hypothèse la plus prudente,
   elle choisit la plus commune dans son corpus d'entraînement.
3. **Ce code gère-t-il les cas limites de mon domaine précis, pas d'un domaine générique ?**
   Un cabinet vétérinaire, une bibliothèque d'escalade, une refacturation d'énergie ont
   chacun des règles métier spécifiques que l'IA ne connaît pas sans que tu les lui donnes.
4. **Est-ce que j'ai testé ce code sur un cas où je connais déjà la réponse correcte à la
   main ?** Un test qui "passe" sur un cas que tu n'as pas vérifié manuellement ne prouve
   rien.

## Compromis

| Option                                                   | Coût                                              | Bénéfice                                                                          | Quand choisir                                                                   |
| -------------------------------------------------------- | ------------------------------------------------- | --------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Refuser l'IA par principe                                | Perte de vitesse réelle sur les tâches mécaniques | Zéro risque de code non vérifié collé sans lecture                                | Rarement justifié en soi ; défendable seulement sur du code critique réglementé |
| Accepter les réponses de l'IA sans vérification          | Vitesse apparente maximale                        | Risque de bugs de logique métier invisibles jusqu'en production                   | Jamais, même sous deadline : le coût se paie plus tard, avec intérêts           |
| IA pour la génération, vérification humaine systématique | Effort de relecture déplacé mais réel             | Gain de vitesse net, risque maîtrisé                                              | Approche par défaut recommandée pour tout code qui touche à une règle métier    |
| IA pour l'exploration d'options, décision humaine        | Prompt plus long à construire                     | Force l'exposition des hypothèses avant qu'elles ne soient enterrées dans du code | Dès qu'une tâche a plusieurs interprétations métier possibles                   |

## Pièges classiques

- **Le "ça a l'air bien" comme seule vérification.** Symptôme : le code est lisible, bien
  nommé, mais personne n'a vérifié qu'il fait ce que le métier attend réellement.
- **Demander une fonction complète sur un domaine ambigu sans clarifier d'abord.**
  Symptôme : l'IA choisit une hypothèse plausible mais fausse, silencieusement, et le bug
  n'apparaît que sur un cas métier rare.
- **Copier du code généré sans pouvoir l'expliquer.** Symptôme : un bug apparaît trois
  semaines plus tard et personne dans l'équipe, y compris toi, ne sait comment la logique
  fonctionne réellement.
- **Utiliser l'IA comme substitut de la réflexion de conception plutôt que comme
  accélérateur.** Symptôme : l'architecture du projet devient une accumulation de morceaux
  générés indépendamment, incohérents entre eux, jamais pensés comme un tout.

## Ce que tu dois savoir défendre

- Explique pourquoi la vitesse d'une réponse d'IA n'est pas corrélée à son exactitude, et
  donne un exemple où cette confusion t'a (ou t'aurait) coûté cher.
- Formule un exemple de prompt de génération directe et transforme-le en prompt de décision
  qui force l'exposition des hypothèses.
- Cite les quatre points de la checklist de revue critique et explique lequel tu sautes le
  plus souvent sous pression, et pourquoi c'est justement le plus dangereux à sauter.
