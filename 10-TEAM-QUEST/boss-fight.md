# Boss Fight : Un conflit de merge humain, pas technique

## La situation

Tu fais partie de l'équipe qui construit le système de tournées de livraison de colis
frigorifiques. Il est 17h un jeudi, la release du vendredi matin est prévue depuis deux
semaines et un client important y est attaché. Deux membres de ton équipe, Malik et Inès,
ont chacun développé une solution complète et fonctionnelle pour le même problème : gérer
les alertes de dépassement de température pendant une tournée. Ils ne le savaient pas
mutuellement jusqu'à cet après-midi, où les deux branches sont arrivées en même temps pour
la revue.

Les deux solutions marchent techniquement. Celle de Malik est plus simple, mais suppose que
le capteur envoie une donnée toutes les minutes : une hypothèse non vérifiée sur le matériel
réellement utilisé par les chauffeurs. Celle d'Inès gère les données manquantes du capteur
mais rajoute une complexité que personne d'autre dans l'équipe ne comprend encore
complètement. Aucun des deux ne veut abandonner son travail : chacun y a passé deux jours
pleins. Le ton commence à monter en fin de réunion : Malik dit qu'Inès complique tout par
principe, Inès dit que Malik ignore un vrai risque terrain. La release doit partir demain
matin. Tu es la troisième personne dans la pièce, pas leur responsable hiérarchique, mais la
seule encore capable d'entendre les deux sans être déjà arc-boutée sur une position.

## Ce qu'on attend de toi

Rédige, comme si tu intervenais réellement dans cette réunion à cet instant, ta prise de
parole complète. Elle doit :

1. Ne pas trancher toi-même en faveur d'une solution sans d'abord faire émerger le vrai
   point de désaccord vérifiable (ici : est-ce que le capteur envoie effectivement une
   donnée toutes les minutes en conditions réelles ?).
2. Proposer une méthode concrète pour vérifier ce point avant la release, plutôt que de
   trancher sur une opinion : ou, si le temps ne le permet pas avant demain matin, proposer
   une décision par défaut qui minimise le risque en attendant la vérification.
3. Traiter explicitement le coût humain de la situation (deux jours de travail de chacun,
   aucun des deux n'a tort sur le fond) sans pour autant sacrifier la qualité de la décision
   technique à la volonté de ne fâcher personne.
4. Proposer, indépendamment de la décision technique du jour, ce que l'équipe devrait
   changer dans son working agreement pour que ce doublon ne se reproduise pas.

## Grille d'évaluation

| Critère                               | Ce qui est raté                                                                            | Ce qui est réussi                                                                                                     |
| ------------------------------------- | ------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------- |
| Identification du vrai désaccord      | Tu discutes des deux solutions comme des préférences esthétiques                           | Tu identifies l'hypothèse vérifiable (fiabilité du capteur) qui devrait trancher                                      |
| Gestion de la pression de la deadline | Tu forces une décision arbitraire juste pour "avancer" avant la release                    | Tu proposes une vérification rapide ou une décision par défaut prudente si le temps manque                            |
| Gestion humaine                       | Tu ignores la frustration de Malik et Inès, ou tu la calmes sans rien résoudre sur le fond | Tu reconnais le coût du travail de chacun sans que ça t'empêche de choisir la meilleure décision technique disponible |
| Réflexe systémique                    | Tu règles seulement le conflit du jour                                                     | Tu proposes un changement concret du working agreement pour éviter le prochain doublon                                |

## Ce que ce boss fight entraîne vraiment

Ce n'est pas un exercice d'arbitrage technique, c'est un exercice de posture au milieu d'un
désaccord qui n'est pas le tien. Le vrai boss ici n'est ni Malik ni Inès : c'est la tentation
de calmer la tension immédiate en évitant la vraie question (quelle hypothèse vérifier)
et la tentation inverse de trancher vite pour tenir la deadline, au prix d'ignorer un risque
terrain réel. Un ingénieur senior ne supprime pas ce genre de tension, il la transforme en
question vérifiable.

## Seuil de validation chiffré

| Critère | Points |
| --- | --- |
| Identification du vrai désaccord | 30 |
| Gestion de la pression de la deadline | 20 |
| Gestion humaine | 25 |
| Réflexe systémique | 25 |
| **Total** | **100** |

```text
< 50   --> boss-fight non valide, la scène est a refaire apres relecture de la lecon concernee
50-69  --> valide avec reserve, identifie le critere le plus faible avant de le compter comme acquis
70-89  --> valide, le reflexe est en place
90-100 --> valide avec excellence, ce niveau de justesse est celui attendu en situation reelle
```

Seuil de passage : 70/100. En dessous, le niveau n'est pas considéré comme acquis, même si le
texte rendu est bien écrit.

**Éliminatoire :** Si "Identification du vrai désaccord" est noté en dessous de 12/30, le total est plafonné à 50/100 : traiter un désaccord technique de fond comme un simple conflit de personnes (ou l'inverse) fait échouer toute la médiation qui suit.
