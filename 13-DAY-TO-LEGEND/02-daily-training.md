# La routine d'entraînement quotidienne

## Le piège

Tu décides de "progresser sérieusement". Lundi, tu bloques deux heures pour lire le code
source de PostgreSQL. Mardi, réunion qui déborde, tu sautes. Mercredi, tu culpabilises, tu
essaies de rattraper avec trois heures d'un coup, tu es crevé, tu abandonnes le kata de
décision prévu. Jeudi, tu ne fais rien, en te disant "de toute façon j'ai raté ma semaine".
Au bout d'un mois, tu as fait de la "pratique délibérée" deux fois, en mode panique, sans
rien retenir. Le problème n'est pas ta discipline. C'est que tu as conçu une routine que
seule une version idéalisée de toi, sans imprévu, sans fatigue, sans réunion qui déborde,
pouvait tenir.

## Ce qui se passe vraiment

### Le principe : minimum viable, tous les jours, plutôt que maximum, parfois

Une routine d'entraînement d'ingénieur fonctionne sur le même principe qu'un programme
sportif sérieux : la régularité bat l'intensité. Vingt minutes par jour, cinq jours sur
sept, pendant douze semaines, produit infiniment plus de progrès qu'une session de trois
heures un dimanche sur deux : parce que la mémoire et le jugement se construisent par
répétition espacée, pas par accumulation brute d'heures.

```text
Routine fragile (ambitieuse)          Routine robuste (minimale, tenable)
─────────────────────────             ────────────────────────────────────
2h/jour visées                        20-30 min/jour visées
Semaine chargée → 0h faites           Semaine chargée → 20 min quand même faites
Culpabilité → abandon complet         Continuité → jamais de rupture longue
Taux de réalisation réel : ~20%       Taux de réalisation réel : ~85%
```

### Les trois piliers de la routine

La routine décrite ici tient sur trois briques, choisies pour couvrir trois mécanismes
d'apprentissage différents et complémentaires. Aucune des trois ne remplace les autres.

```text
┌─────────────────────┬───────────────────────┬────────────────────────┐
│ Lecture de code      │ Kata de décision       │ Journal                │
├─────────────────────┼───────────────────────┼────────────────────────┤
│ Expose à des idées   │ Force à trancher sous  │ Rend visible ta propre │
│ et des solutions que │ contrainte, comme en   │ progression et tes     │
│ tu n'aurais jamais   │ vrai projet, mais sans │ angles morts répétés   │
│ inventées seul       │ le risque réel          │                        │
└─────────────────────┴───────────────────────┴────────────────────────┘
```

### Pilier 1 : Lecture de code (10-15 min/jour)

Objectif : lire du code écrit par d'autres, dans un projet réel, pas un tutoriel. La lecture
de code entraîne un muscle différent de l'écriture : comprendre une intention à partir de
sa trace, sans l'avoir vécue.

Méthode concrète :

1. Choisis un projet open source dans un domaine qui t'intéresse (pas forcément ton stack
   actuelle : lire du code Rust quand tu écris du TypeScript t'apprend des idées de
   structuration que ton langage habituel cache).
2. Ne lis jamais un fichier isolé. Pars d'un comportement observable ("comment ce système
   gère un timeout réseau ?") et remonte le code jusqu'à trouver la réponse. C'est la
   méthode d'exploration du Niveau 11 (Big-App-Snoop), appliquée en continu et en petites
   doses.
3. Note une phrase par session : "ce que j'aurais fait différemment, et pourquoi eux ont
   choisi ça" : même si tu conclus qu'ils ont raison. L'important est de formuler
   explicitement le compromis, pas de juger.

### Pilier 2 : Kata de décision (10-15 min/jour, 3-4 fois/semaine)

Un kata de code classique (implémenter FizzBuzz vingt fois) muscle la vitesse d'écriture.
Un kata de **décision** muscle autre chose : la vitesse et la qualité du jugement face à une
situation ambiguë. C'est plus rare, et c'est ce qui manque le plus aux développeurs
expérimentés en syntaxe mais lents en arbitrage.

Format d'un kata de décision (15 minutes, chronométrées) :

```text
1. Tire une situation d'un pool (voir plus bas) : 1 min de lecture
2. Écris, sans recherche externe, ta décision en 5 lignes maximum : 8 min
   (contexte + décision + justification + compromis explicite + ce qui te ferait changer d'avis)
3. Compare avec une réponse de référence si tu en as une (collègue, mentor, ADR existant
   d'un projet réel) : 5 min
4. Note en une ligne l'écart principal entre ta réponse et la référence : 1 min
```

Pool de situations à faire tourner (exemples, à enrichir avec des cas de ton propre
domaine) :

- Un champ optionnel devient obligatoire dans une API déjà utilisée par trois clients.
  Décide : version majeure, champ par défaut, ou double lecture temporaire ?
- Une requête qui prenait 200ms en prend 4 secondes après une migration de données. Décide :
  ordre des trois premières vérifications à faire, sans accès au profiler encore.
- Deux développeurs de ton équipe ont livré, indépendamment, deux façons différentes de
  valider un même type de formulaire. Décide : lequel garder, et comment l'annoncer sans
  braquer l'auteur du choix écarté.
- Un client demande une fonctionnalité qui contredit une règle métier que tu as posée il y a
  deux mois pour une bonne raison. Décide : quelle question tu poses avant de trancher.

Le but n'est pas de "avoir raison". C'est d'entraîner le réflexe de produire une décision
justifiée et falsifiable (qui dit ce qui la remettrait en cause) en un temps court, plutôt
que de tourner en rond ou d'attendre qu'un supérieur décide à ta place.

### Pilier 3 : Journal (5 min/jour, fin de journée)

Le journal n'est pas un carnet de pensées libres. C'est un outil de détection de patterns
sur toi-même, que ta mémoire seule ne peut pas fournir : la mémoire réécrit le passé pour le
rendre cohérent avec ce que tu crois déjà de toi.

Format minimal, trois lignes, chaque jour :

```text
1. Une décision technique ou de priorité que j'ai prise aujourd'hui, en une phrase.
2. Ce qui m'a fait hésiter avant de trancher (si rien, l'écrire aussi : c'est une info).
3. Une chose que je referais différemment si c'était à refaire demain.
```

Après quatre à six semaines, relis les entrées d'un coup, en cherchant des motifs répétés :
"j'hésite systématiquement sur les choix de nommage", "je sous-estime toujours le temps
d'une migration de données", "je documente ma décision seulement quand quelqu'un me le
demande". Ces motifs sont invisibles jour par jour et évidents en relecture groupée : c'est
le seul mécanisme fiable pour détecter ses propres angles morts récurrents.

## Compromis

| Option                                    | Coût                                                     | Bénéfice                                                       | Quand choisir                                                           |
| ----------------------------------------- | -------------------------------------------------------- | -------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Routine minimale quotidienne (20-30 min)  | Progrès lent perçu au jour le jour                       | Tenable des mois, effet composé réel sur 12 semaines           | Régime par défaut, toute l'année                                        |
| Sessions longues occasionnelles (weekend) | Fatigue, taux d'abandon élevé, pas de répétition espacée | Sensation de "gros progrès" ponctuel                           | En complément, jamais en remplacement du quotidien                      |
| Lecture seule, sans kata ni journal       | Zéro friction, facile à tenir                            | Progrès de vocabulaire, pas de jugement                        | Jamais suffisant seul : à combiner obligatoirement                      |
| Kata seul, sans lecture ni journal        | Bon entraînement de vitesse de décision                  | Risque de renforcer de mauvais réflexes sans référence externe | Seulement si tu as déjà un mentor ou une source de correction régulière |

## Pièges classiques

- **Le kata sans référence de correction.** Symptôme : tu t'entraînes à décider vite, mais
  jamais à comparer avec une décision experte : tu renforces potentiellement de mauvais
  réflexes avec de plus en plus de confiance.
- **Le journal qui devient un journal intime.** Symptôme : les entrées s'allongent, deviennent
  émotionnelles plutôt que factuelles, tu arrêtes de les relire en groupe parce qu'il y en a
  trop à parcourir. Reviens au format trois lignes strict.
- **La routine dépendante d'un contexte parfait.** Symptôme : tu ne t'entraînes que quand tu
  as "le temps", donc jamais en période chargée : exactement les périodes où l'entraînement
  aurait le plus de valeur pour tester ton jugement sous pression réelle.
- **La lecture de code passive, sans production écrite.** Symptôme : tu as l'impression
  d'apprendre en lisant, mais tu ne pourrais pas restituer une seule décision précise du code
  lu une semaine après. Sans la phrase de synthèse, la lecture ne laisse quasiment aucune
  trace.

## Ce que tu dois savoir défendre

- Pourquoi une routine de 20 minutes tenue tous les jours bat une session de trois heures
  tenue une fois par semaine, en citant le mécanisme (répétition espacée) et pas seulement
  l'intuition.
- Explique la différence entre un kata de code classique et un kata de décision, et pourquoi
  les deux entraînent des muscles différents.
- Donne un exemple de motif que tu pourrais découvrir en relisant un mois de ton propre
  journal, s'il existait déjà.
