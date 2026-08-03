# Templates de décision : ADR, RFC, one-pager

## Le piège

L'équipe qui gère la refacturation d'énergie d'un immeuble collectif hésite entre stocker
les tarifs horaires en base ou les calculer à la volée depuis un service externe. La
discussion a lieu à l'oral en réunion, personne ne prend de notes structurées, une décision
émerge par lassitude plutôt que par conviction. Six mois plus tard, un nouveau développeur
demande "pourquoi c'est fait comme ça", personne ne se souvient exactement des raisons, et
l'équipe repart dans le même débat depuis zéro — deuxième fois pour le même coût cognitif.

## Ce qui se passe vraiment

Un format de décision n'est pas de la bureaucratie. C'est une mémoire externe qui évite de
repayer le coût d'une décision déjà prise. Le choix du bon format dépend de l'enjeu et de
l'audience, pas d'une préférence personnelle pour la paperasse ou contre elle.

```text
Enjeu réversible, une personne     →  Rien d'écrit, ou une ligne dans le message de commit
Enjeu structurant, équipe restreinte →  ADR (Architecture Decision Record)
Enjeu qui engage plusieurs équipes  →  RFC (Request for Comments)
Enjeu qui doit convaincre une direction non technique →  One-pager
```

### ADR — pour une décision d'équipe, traçable, rarement contestée après coup

```text
# ADR-0007 : Stockage des tarifs horaires en base plutôt qu'appel à la volée

## Statut
Acceptée — 2024-03-12

## Contexte
Le service externe de tarifs horaires a une latence moyenne de 400ms et une
disponibilité de 99.2%. Le calcul de facture doit rester fonctionnel même si
le service externe est indisponible ponctuellement.

## Décision
Les tarifs horaires sont synchronisés en base chaque nuit, jamais appelés à la
volée pendant le calcul de facture.

## Conséquences
+ Calcul de facture indépendant de la disponibilité du service externe.
+ Latence de calcul réduite à ~5ms au lieu de 400ms.
- Tarifs potentiellement obsolètes jusqu'à 24h en cas de changement tarifaire urgent.
- Nécessite un job de synchronisation à monitorer.
```

Une bonne ADR nomme une conséquence négative réelle, pas seulement des bénéfices — sinon ce
n'est pas une décision, c'est une publicité.

### RFC — pour un changement qui traverse plusieurs équipes ou services

Une RFC diffère d'une ADR par son intention : elle sollicite un retour avant que la décision
ne soit prise, pas après. Structure minimale : problème, contraintes connues, options
considérées avec compromis explicites, proposition, période de commentaires avec date de
clôture.

```text
Différence clé :
ADR  → documente une décision déjà prise, pour la mémoire future.
RFC  → propose une décision, ouverte à contestation, avant qu'elle ne soit prise.
```

### One-pager — pour convaincre une audience non technique

Un one-pager ne contient jamais de détail d'implémentation. Il répond à trois questions en
une page : quel problème métier concret cela résout, quel est le coût (temps, argent,
risque), quel est le résultat mesurable attendu et à quelle échéance. Aucun jargon technique
non expliqué.

## Compromis

| Format | Coût de rédaction | Bénéfice | Quand choisir |
|---|---|---|---|
| Rien d'écrit | Nul | Rapide sur l'instant | Décision réversible en une commande, une seule personne concernée |
| ADR | 15-30 minutes | Mémoire durable, évite de rejouer le débat | Décision d'architecture qui engage l'équipe plus de quelques semaines |
| RFC | Plusieurs heures, cycle de relecture | Aligne plusieurs équipes avant l'implémentation, réduit le risque de blocage a posteriori | Changement qui traverse des frontières d'équipe ou de service |
| One-pager | 30-60 minutes | Obtient un budget ou un accord d'une audience non technique | Décision qui nécessite une validation hiérarchique ou financière |

## Pièges classiques

- **Écrire une ADR après coup pour justifier une décision déjà actée ailleurs.** Symptôme :
  le document ne liste aucune conséquence négative, seulement des avantages.
- **Utiliser une RFC pour une décision déjà prise, la faire passer pour de la concertation.**
  Symptôme : les commentaires reçus ne changent jamais rien à la proposition initiale.
- **Un one-pager truffé de jargon technique.** Symptôme : l'audience non technique hoche la
  tête en réunion mais ne peut reformuler la décision avec ses propres mots ensuite.
- **Sur-documenter une décision triviale.** Symptôme : plus de temps passé à rédiger l'ADR
  qu'à implémenter la décision elle-même.

## Ce que tu dois savoir défendre

- Explique la différence d'intention entre une ADR et une RFC, pas seulement de format.
- Donne un exemple de décision technique récente que tu as prise sans la documenter, et
  explique si elle aurait mérité une ADR a posteriori.
- Explique pourquoi une ADR sans conséquence négative listée est un signal d'alarme.
