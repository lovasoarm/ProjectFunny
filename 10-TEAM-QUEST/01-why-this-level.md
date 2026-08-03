# Pourquoi de bons devs produisent une mauvaise équipe

## La scène

Léa et Youssef codent tous les deux le suivi de tournées d'une entreprise de livraison de
colis frigorifiques. Léa travaille depuis trois jours sur une branche `feature/gestion-conflits`
pour détecter les créneaux qui se chevauchent entre deux chauffeurs. Youssef, sur sa propre
branche, refactore la même fonction de calcul de créneaux parce qu'il la trouve mal nommée.
Ni l'un ni l'autre ne le sait, parce que personne n'a jamais dit à voix haute "on se
prévient avant de toucher un fichier partagé plus d'une heure". Le vendredi, ils fusionnent
leurs deux branches. Le conflit Git prend vingt minutes à résoudre. La vraie perte, elle,
prend trois semaines : Youssef se sent court-circuité, il commence à faire ses revues de
code de Léa plus sèchement, Léa arrête de lui montrer son travail avant qu'il soit fini pour
éviter la friction. Le code redevient propre. L'équipe, non. Deux excellents développeurs
viennent de produire, ensemble, quelque chose de pire que ce que chacun aurait produit seul.

## Ce qui se passe vraiment

Le code est un artefact technique gouverné par des règles explicites : un compilateur
refuse une syntaxe invalide, un type-checker refuse un type incompatible. Le travail
d'équipe est gouverné par des règles implicites : et une règle implicite n'existe que dans
la tête de celui qui l'a en tête. Tant que personne ne la rend explicite, chacun suit sa
propre version, découvre l'écart au moment de la friction, et interprète cet écart comme un
défaut de caractère de l'autre plutôt que comme l'absence d'un accord.

```text
Règle implicite non dite                  Règle explicite écrite

  Léa pense : "on prévient avant           Working agreement : "toute modification
  de toucher un fichier partagé"           d'un fichier partagé plus de 30 min
       │                                   se signale dans le canal d'équipe
  Youssef pense : "chacun avance            avant de commencer"
  sur sa branche, on gère les                     │
  conflits à la fusion"                     Les deux se réfèrent à la même règle,
       │                                    le désaccord porte sur le travail,
  Écart découvert au conflit Git            pas sur l'intention de l'autre
       │                                          │
  Interprété comme "il m'a doublé"          Résolution rapide, confiance intacte
  ou "elle ne me fait pas confiance"
```

Ce niveau attaque trois couches de ce problème, chacune correspondant à un moment différent
de la collaboration :

1. **Le working agreement** (leçon 02) : rend explicites les règles qui, sinon, restent
   dans la tête de chacun jusqu'à ce qu'elles se heurtent : définition de "fini", qui
   décide quoi, comment on se prévient, comment on planifie une absence.
2. **Le flux Git** (leçon 03) : donne une structure technique au travail parallèle, pour
   que deux personnes puissent avancer en même temps sur le même système sans que l'une
   écrase le travail de l'autre ni bloque une release en cours à cause d'un chantier
   inachevé.
3. **La communication sous pression** (leçon 04) : parce qu'aucun accord écrit ne couvre
   tous les cas, et que c'est précisément dans les cas non couverts : désaccord technique,
   demande intenable, décision urgente : que l'équipe se joue vraiment.

### Le coût, toujours le coût

Ce niveau refuse une idée répandue et fausse : "une équipe qui s'entend bien n'a pas besoin
d'accords écrits, ça se fait naturellement". Ça marche tant que l'équipe est petite, calme,
et sans désaccord réel. Dès qu'un désaccord technique surgit, ou qu'une deadline serrée met
la pression, l'absence d'accord explicite ne produit pas de la souplesse : elle produit de
l'ambiguïté, et l'ambiguïté sous pression se résout presque toujours par le rapport de force
plutôt que par le raisonnement. Écrire les règles à froid coûte une réunion. Les découvrir à
chaud coûte une relation.

## Compromis

| Option                                               | Coût                                                       | Bénéfice                                                                                   | Quand choisir                                                                    |
| ---------------------------------------------------- | ---------------------------------------------------------- | ------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- |
| Pas d'accords explicites, "ça se fait naturellement" | Aucun coût immédiat, semble fluide au début                | Rapidité de démarrage sur une équipe très petite et très alignée                           | Binôme temporaire, projet de quelques jours, jamais une équipe qui dure          |
| Working agreement écrit et Git flow choisi           | Coût d'une réunion de cadrage et de sa révision périodique | Les désaccords se règlent sur des règles connues de tous, pas sur des intentions supposées | Toute équipe de plus de deux personnes qui collabore plus d'une ou deux semaines |

## Pièges classiques

- Croire qu'une bonne ambiance efface le besoin de règles écrites : le symptôme apparaît au
  premier désaccord réel, quand chacun découvre que l'autre avait une règle différente en
  tête.
- Rédiger un working agreement une fois et ne plus jamais le relire : le symptôme est un
  document qui décrit une équipe qui n'existe plus, ignoré parce qu'obsolète.
- Confondre flux Git et discipline d'équipe : le symptôme est une équipe qui a un flux
  Git impeccable sur le papier mais des branches vivantes trois semaines sans être fusionnées.
- Traiter tout désaccord comme un problème de personnalité : le symptôme est qu'on change
  les gens au lieu de changer les règles, et le même conflit revient avec la personne
  suivante.

## Ce que tu dois savoir défendre

1. Pourquoi une équipe compétente individuellement peut produire un résultat collectif pire
   que la somme de ses membres, en l'absence d'accords explicites.
2. Explique le mécanisme par lequel une règle implicite non dite devient, au moment du
   conflit, une accusation contre la personne plutôt qu'un désaccord sur la règle.
3. Donne un exemple de situation où "ça se passe naturellement" fonctionne, et explique
   pourquoi elle ne se généralise pas à une équipe plus grande ou sous pression.
