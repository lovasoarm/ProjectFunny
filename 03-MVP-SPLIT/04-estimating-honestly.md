# Estimer honnêtement

## La scène

Une régie de refacturation d'énergie pour un ensemble de copropriétés demande "combien de
temps pour ajouter la répartition automatique des charges de chauffage collectif selon les
relevés de sous-compteurs ?" Un développeur répond "trois jours" — un chiffre unique, donné
en réunion, sans avoir ouvert le code. Trois semaines plus tard, la fonctionnalité n'est
toujours pas livrée : les sous-compteurs de trois fabricants différents ne renvoient pas le
même format de données, deux immeubles ont des règles de répartition dérogatoires votées en
assemblée générale, et personne n'avait anticipé qu'il fallait gérer les compteurs en panne
temporaire. Le chiffre "trois jours" n'était pas optimiste, il était fictif : il ne reposait
sur aucune inspection réelle du problème.

## Ce qui se passe vraiment

Une estimation honnête n'est pas un chiffre plus prudent, c'est un chiffre qui porte
explicitement son incertitude au lieu de la cacher. Un chiffre unique ("trois jours") laisse
croire à une précision qui n'existe pas et transfère silencieusement tout le risque
d'incertitude sur la personne qui devra livrer dans ce délai. Une fourchette honnête dit où
se trouve l'incertitude et pourquoi.

```text
Estimation malhonnête (chiffre unique) :
  "Trois jours."
  → aucune indication de ce qui est connu vs inconnu
  → tout écart devient, pour l'extérieur, un signe d'incompétence

Estimation honnête (fourchette + sources d'incertitude) :
  "Entre 2 et 8 jours.
   2 jours si les trois fabricants de sous-compteurs exposent un format
     compatible et qu'il n'y a pas de règle dérogatoire à gérer.
   8 jours si on doit écrire un adaptateur par fabricant et gérer les
     dérogations d'assemblée générale comme cas particuliers.
   Je saurai lequel des deux scénarios est le bon après une demi-journée
   d'inspection des formats de données réels."
```

La deuxième version ne demande pas plus de travail à produire — elle demande d'admettre, à
voix haute, ce qu'on ne sait pas encore. C'est souvent la partie inconfortable de l'exercice,
pas la partie technique.

## Découper avant d'estimer

Estimer une tâche large et mal définie donne systématiquement un chiffre faux, parce que
l'esprit humain sous-estime le nombre de sous-tâches cachées dans une tâche qu'il n'a pas
décomposée. Découper la tâche en sous-tâches concrètes, avant de mettre un chiffre dessus,
fait remonter les inconnues qui, sinon, restent invisibles jusqu'au jour où elles bloquent
tout.

```text
Tâche large, non découpée :
  "Répartition automatique des charges de chauffage" → estimation à l'instinct

Tâche découpée :
  ├── Lire et normaliser les formats des 3 fabricants de sous-compteurs
  │     → inconnue : formats réels, à vérifier avant d'estimer précisément
  ├── Calculer la répartition standard (au prorata des relevés)
  │     → connu : règle simple, déjà documentée
  ├── Gérer les dérogations votées en assemblée générale
  │     → inconnue : combien d'immeubles concernés, quelles règles exactement
  └── Gérer les compteurs en panne ou relevé manquant
        → inconnue : règle de fallback pas encore définie avec le métier

Chaque ligne inconnue devient soit une tâche d'investigation à part
(avant d'estimer le développement lui-même), soit une fourchette large
assumée comme telle.
```

## L'effet tunnel

L'effet tunnel se produit quand une équipe s'engage sur une estimation optimiste puis, en
cours de route, préfère continuer à foncer dans le plan initial plutôt que de remonter un
signal d'alerte — parce qu'admettre un dépassement en cours de route semble pire que d'espérer
rattraper le retard plus tard. Résultat : le dépassement, au lieu d'être signalé tôt et petit,
n'apparaît que juste avant l'échéance, en grand, quand il est trop tard pour ajuster le
périmètre ou la date sans dégâts.

```text
Sans point de contrôle :
Jour 1 ────────────────────────────────────── Jour 15 (échéance)
"tout va bien" (silence radio)             "on a un problème" (trop tard)

Avec points de contrôle réguliers :
Jour 1 ── Jour 4 ── Jour 8 ── Jour 12 ── Jour 15
       "sous-compteur      "dérogation        échéance
        fabricant C          AG plus
        pose problème,       complexe que
        +2 jours prévus"     prévu, +1 jour"
```

Le remède n'est pas d'estimer mieux du premier coup — c'est impossible avec de l'inconnu
réel — mais de prévoir des points de contrôle rapprochés où l'estimation se met à jour
publiquement, en petits ajustements, plutôt qu'en un seul choc final.

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Fourchette avec sources d'incertitude explicites | Demande d'expliciter ce qu'on ne sait pas, parfois inconfortable | Protège la crédibilité de l'équipe et permet d'anticiper les points à investiguer | Systématiquement pour toute tâche non triviale |
| Chiffre unique donné à l'instinct | Rapide et rassurant en réunion | Transfère tout le risque d'incertitude sur la personne qui livre, casse la confiance à l'écart | Jamais, sauf tâche vraiment triviale et déjà faite plusieurs fois |
| Points de contrôle rapprochés pendant l'exécution | Demande une discipline de reporting régulier | Évite l'effet tunnel, permet d'ajuster le périmètre tôt plutôt que tard | Dès que la tâche dépasse 2-3 jours ou comporte de l'inconnu |

## Pièges classiques

- Donner un chiffre unique en réunion sous pression sociale, sans avoir eu le temps
  d'inspecter le problème — le symptôme est qu'on ne peut pas dire ce qui ferait varier ce
  chiffre.
- Estimer une tâche non découpée, ce qui masque systématiquement les sous-tâches invisibles
  (formats de données réels, cas dérogatoires, comportements de panne).
- Rester silencieux quand un premier écart apparaît, en espérant le rattraper plus tard —
  c'est exactement le mécanisme de l'effet tunnel.
- Confondre "estimation optimiste" et "estimation malhonnête" : l'optimisme mesuré et assumé
  est acceptable, l'absence totale de fourchette ne l'est pas.

## Ce que tu dois savoir défendre

- Pourquoi une fourchette avec ses sources d'incertitude est plus utile qu'un chiffre unique,
  même si elle semble moins rassurante à l'oral.
- Comment découper une tâche large fait remonter des inconnues qu'une estimation globale
  laisse invisibles.
- Ce qu'est l'effet tunnel concrètement, et comment des points de contrôle rapprochés
  permettent de le prévenir plutôt que de le subir en fin de projet.
