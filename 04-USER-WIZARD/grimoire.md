# Grimoire : Niveau 04, User Wizard

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| États obligatoires (idle, loading, empty, ready, partial, overload, error) | Les six situations qu'un affichage de données doit gérer explicitement | L'UI ment sur l'état réel des données : succès affiché sur une erreur, ou inverse | Pour cet écran, montre-moi les six états rendus, pas seulement le cas ready |
| État partiel | Succès incomplet : ne jamais l'afficher comme un succès plein ni une erreur totale | L'utilisateur croit avoir toutes les données alors qu'il n'en a qu'une partie, décide sur une base fausse | Comment distingues-tu visuellement un succès partiel d'un succès complet ? |
| Overload | Volume trop grand pour l'UI : forcer un filtre plutôt que tout rendre | Le navigateur se fige à essayer de rendre des milliers de lignes d'un coup | Quel seuil de volume déclenche le passage en mode filtré forcé ? |
| Machine à états d'un formulaire critique (IDLE, DIRTY, ENVOI, CONFIRMÉ, INCERTAIN) | Modélisation explicite des transitions d'un formulaire, y compris le cas timeout | Un double clic ou un timeout renvoie deux fois la même action sans que personne ne le détecte | Que fait ton formulaire si la réponse serveur n'arrive jamais (timeout) ? |
| Idempotence | Une opération répétée produit le même effet qu'exécutée une seule fois | Un retry réseau ou un double clic crée deux fois la ressource (double réservation, double paiement) | Quelle est la clé d'idempotence de cette soumission, et qui la génère ? |
| Race condition | Deux actions concurrentes dont le résultat dépend de l'ordre d'arrivée | Deux utilisateurs modifient la même ressource en même temps, l'un écrase l'autre sans le savoir | Quel est le pire ordre d'arrivée possible pour ces deux actions concurrentes ? |
| UI optimiste | Appliquer un changement à l'écran avant confirmation serveur | Un rollback silencieux laisse l'utilisateur croire à un succès qui n'a jamais eu lieu | Pour cette action, l'optimisme est-il justifié (réversible, faible enjeu de conflit) ou dangereux ? |
| Layout shift | Déplacement visuel du contenu causé par une mise en page instable au chargement | L'utilisateur clique au mauvais endroit parce que le contenu a bougé sous son curseur | Ton squelette de chargement a-t-il exactement la forme du contenu final ? |
| Accessibilité clavier | Tout élément cliquable doit être atteignable et activable au clavier (Tab, Entrée, Espace) | Une partie des utilisateurs ne peut simplement pas utiliser l'interface | Peux-tu accomplir ce parcours entier sans jamais toucher la souris ? |
| Formulaire qui ne ment pas | Validation identique côté client et serveur, erreurs nommées par champ et par règle | Le client valide, le serveur accepte n'importe quoi envoyé hors UI (curl, script) | Que se passe-t-il si ce formulaire est soumis directement en curl avec un payload invalide ? |

## Comportements évalués en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'échec |
| --- | --- | --- |
| Compréhension du risque métier (justification par un mécanisme) | Illustre le risque de doublon avec l'impact concret sur un client réel | Ignore le risque de doublon ou le traite comme un détail technique |
| Qualité de l'alternative proposée (compromis nommé et assumé) | Propose un mécanisme précis (feedback instantané + confirmation réelle) réalisable dans le délai | Refuse sans alternative, ou accepte l'optimiste pur sans garde-fou |
| Communication vers le non-technique | Convainc avec un exemple concret et un chiffre, sans jargon non traduit | Utilise du jargon (race condition, idempotence) sans traduire |
| Honnêteté sur les limites (honnêteté sur ce qu'on ne sait pas) | Explique la contrainte réelle (temps de réponse backend) et propose un plan pour l'améliorer après lundi | Prétend que tout est faisable |

## Les cinq états obligatoires (+ 1) de tout affichage de données

```text
idle --> loading --> { empty | ready | partial | overload | error }
```

- **idle** : rien n'a encore été demandé.
- **loading** : requête en cours, afficher un squelette de la forme du contenu, pas un spinner nu.
- **empty** : succès, zéro résultat : dire si c'est normal et proposer une action.
- **ready** : succès, volume raisonnable.
- **partial** : succès incomplet : ne jamais l'afficher comme un succès plein ni une erreur totale.
- **overload** : volume trop grand pour l'UI : forcer un filtre plutôt que tout rendre.
- **error** : échec : nommer la cause si possible, proposer une action de récupération.

## Machine à états d'un formulaire critique

```text
IDLE -> DIRTY -> ENVOI -> CONFIRMÉ
                  ^  |
                  +--+ erreur récupérable (validation, conflit)
                  |
                  v
              INCERTAIN (timeout) -> vérifier l'état réel avant de renvoyer
```

## Checklist formulaire qui ne ment pas

- [ ] Schéma de validation identique (idéalement littéralement le même fichier) client et serveur.
- [ ] Chaque erreur nomme le champ et la règle violée, jamais "erreur de validation" seul.
- [ ] Le serveur rejette tout payload invalide, même envoyé hors de l'UI (testable au curl).
- [ ] Une clé d'idempotence unique par tentative de soumission d'un même formulaire ouvert.
- [ ] Le serveur stocke la réponse associée à la clé et la rejoue, il ne recrée jamais la ressource.
- [ ] Le bouton de soumission se désactive pendant l'envoi (confort, pas garantie).

## Optimisme UI : grille de décision rapide

```text
Action réversible + faible enjeu de conflit  -> optimiste OK (archiver, marquer lu, aimer)
Action irréversible OU forte concurrence     -> pessimiste (réserver, payer, envoyer définitif)
```

Toujours prévoir : rollback visible + message explicite en cas d'échec. Jamais de revert silencieux.

## Accessibilité : le minimum qui a le plus d'impact

- Utiliser les vrais éléments (`button`, `label`, `input`) plutôt que des `div` stylées.
- Tout ce qui est cliquable doit être atteignable et activable au clavier (Tab, Entrée, Espace).
- Focus envoyé dans une modale à l'ouverture, rendu à l'origine à la fermeture.
- Erreurs de champ reliées via `aria-describedby` + `aria-invalid`, annoncées via `role="alert"`.

## Perception de vitesse : leviers qui ne changent pas le temps réel serveur

- Squelette de la forme exacte du contenu, affiché en moins de 100ms.
- Feedback de clic instantané avant la fin de la requête réseau.
- Chargement progressif plutôt qu'attente du tout-ou-rien.
- Annoncer une attente longue plutôt que laisser un spinner muet indéfiniment.
