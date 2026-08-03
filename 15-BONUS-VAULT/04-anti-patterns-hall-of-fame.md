# Anti-patterns hall of fame

## Le piège

Chaque équipe technique croit vivre des problèmes uniques à son contexte. En réalité, la
grande majorité des dysfonctionnements techniques et organisationnels reviennent, année
après année, projet après projet, sous des noms différents mais avec la même structure
sous-jacente. Ce catalogue nomme les plus fréquents pour que tu les reconnaisses tôt, avant
qu'ils ne deviennent la norme silencieuse de ton équipe.

## Ce qui se passe vraiment

Un anti-pattern n'est pas une erreur ponctuelle. C'est une solution qui a semblé
raisonnable à court terme, s'est généralisée sans remise en question, et dont le coût
n'apparaît que longtemps après que la décision initiale a été oubliée.

```text
Décision locale raisonnable  →  Généralisation sans remise en question  →  Coût différé
     (semble efficace)              (devient la norme implicite)          (personne ne
                                                                            relie plus la
                                                                            cause à l'effet)
```

### Le God Object (l'objet qui sait tout, fait tout)

Une classe `GestionnaireCabinetVeterinaire` finit par contenir la logique de planning, de
facturation, de notification et de gestion des dossiers médicaux. Symptôme : chaque
modification, même mineure, touche un fichier de plusieurs milliers de lignes que plus
personne ne comprend en entier, et chaque revue de code sur ce fichier prend deux fois plus
de temps que sur le reste du projet.

### Le Copier-Coller Métier (duplication de règle sans source unique)

La règle "un créneau vétérinaire ne peut pas être réservé moins de deux heures à l'avance"
est codée à trois endroits différents (formulaire de prise de rendez-vous, API, job de
rappel automatique). Symptôme : la règle change une fois, dans un seul des trois endroits,
et un client peut réserver un créneau dans l'heure via l'API alors que le formulaire le
bloque — un bug invisible tant que personne ne compare les deux chemins.

### Le Faux Consensus de Réunion (décision jamais vraiment prise)

Une réunion se termine par "on est tous d'accord sur l'approche B" sans que personne n'ait
formulé explicitement ce qu'était l'approche B. Symptôme : trois personnes implémentent
trois versions différentes de "l'approche B" dans les semaines qui suivent, chacune
convaincue d'avoir suivi la décision.

### Le Test Alibi (test qui ne teste rien)

Un test unitaire vérifie que la fonction ne lève pas d'exception, sans jamais vérifier la
valeur retournée. Symptôme : la couverture de tests affichée est haute, mais un bug de
logique métier majeur passe en production sans qu'aucun test n'ait bronché.

### La Dette Technique Silencieuse (jamais nommée, jamais budgétée)

Un raccourci pris sous deadline ("on corrigera plus tard") n'est jamais écrit nulle part,
jamais estimé en coût, jamais planifié pour être remboursé. Symptôme : dix-huit mois plus
tard, personne ne se souvient pourquoi telle partie du code est fragile, et chaque nouvelle
fonctionnalité dans cette zone prend trois fois plus de temps que prévu sans qu'on sache
pourquoi précisément.

### Le Héros Systémique (une seule personne qui sait tout)

Un seul développeur comprend vraiment le système de refacturation d'énergie de l'immeuble
collectif. Toute l'équipe se repose sur lui pour chaque question un peu profonde. Symptôme :
cette personne part en vacances ou change d'équipe, et un incident simple devient une crise
de plusieurs jours faute de documentation ou de transfert de connaissance réel.

### Le Yak Shaving Involontaire (le vrai problème disparaît sous les sous-problèmes)

Pour corriger un bug de calcul de tournée, tu découvres que la librairie de dates utilisée
est obsolète, tu la mets à jour, ce qui casse trois autres modules, que tu corriges à leur
tour, en oubliant le bug initial. Symptôme : plusieurs heures ou jours passés, un diff
énorme, et le ticket d'origine toujours pas résolu.

### Le Cargo Cult Architectural (copier la forme sans le contexte)

L'équipe adopte une architecture en microservices parce qu'une grande entreprise connue
l'utilise, sans avoir le volume de trafic ni l'équipe nécessaires pour en justifier le coût
opérationnel. Symptôme : plus de complexité de déploiement et de debug qu'avec un monolithe,
sans aucun des bénéfices de scalabilité qui justifiaient le choix chez l'entreprise copiée.

## Compromis

| Anti-pattern | Coût d'ignorer | Coût de corriger tôt | Coût de corriger tard |
|---|---|---|---|
| God Object | Fichier de plus en plus risqué à toucher | Refactoring ciblé, modéré | Refactoring complet sous pression, risqué |
| Copier-Coller Métier | Bugs d'incohérence discrets | Centraliser la règle, simple | Chercher toutes les occurrences en production, lent |
| Faux Consensus | Reprises de travail multiples | Reformuler explicitement en fin de réunion, gratuit | Conflit d'équipe après coup |
| Test Alibi | Fausse confiance en la couverture | Réécrire l'assertion, rapide | Bug découvert en production, coûteux |
| Dette Technique Silencieuse | Ralentissement progressif inexpliqué | Nommer et estimer, discipline légère | Zone du code que plus personne ne veut toucher |
| Héros Systémique | Dépendance critique sur une personne | Documenter et faire des binômes, effort régulier | Crise à son départ |
| Cargo Cult Architectural | Complexité opérationnelle non justifiée | Choisir selon le contexte réel, effort de jugement | Migration inverse coûteuse |

## Pièges classiques

- **Reconnaître l'anti-pattern chez les autres, jamais chez soi.** Symptôme : tu identifies
  facilement le Héros Systémique dans une autre équipe sans remarquer que tu en es un dans
  la tienne.
- **Corriger la forme sans corriger la cause.** Symptôme : découper le God Object en
  plusieurs fichiers sans jamais clarifier les responsabilités — le God Object se
  reconstitue ailleurs, juste réparti différemment.
- **Traiter ces anti-patterns comme des fautes morales plutôt que des dérives structurelles.**
  Symptôme : blâmer une personne pour un God Object qui s'est construit sur trois ans, par
  dizaines de petites décisions locales raisonnables prises par toute l'équipe.

## Ce que tu dois savoir défendre

- Identifie, dans un projet que tu connais bien, un anti-pattern de cette liste présent
  aujourd'hui, et la décision locale raisonnable qui l'a probablement lancé.
- Explique pourquoi corriger la forme d'un anti-pattern sans corriger sa cause structurelle
  le fait souvent réapparaître ailleurs.
- Choisis l'anti-pattern de cette liste que tu risques personnellement le plus de reproduire,
  et explique le mécanisme précis qui t'y pousserait.
