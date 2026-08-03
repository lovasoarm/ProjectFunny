# Grimoire : Niveau 04, User Wizard

## Les cinq états obligatoires (+ 1) de tout affichage de données

```
idle → loading → { empty | ready | partial | overload | error }
```

- **idle** : rien n'a encore été demandé.
- **loading** : requête en cours, afficher un squelette de la forme du contenu, pas un spinner nu.
- **empty** : succès, zéro résultat : dire si c'est normal et proposer une action.
- **ready** : succès, volume raisonnable.
- **partial** : succès incomplet : ne jamais l'afficher comme un succès plein ni une erreur totale.
- **overload** : volume trop grand pour l'UI : forcer un filtre plutôt que tout rendre.
- **error** : échec : nommer la cause si possible, proposer une action de récupération.

## Machine à états d'un formulaire critique

```
IDLE → DIRTY → ENVOI → CONFIRMÉ
                 │  ↑
                 └──┘ erreur récupérable (validation, conflit)
                 │
                 ▼
             INCERTAIN (timeout) → vérifier l'état réel avant de renvoyer
```

## Checklist formulaire qui ne ment pas

- [ ] Schéma de validation identique (idéalement littéralement le même fichier) client et serveur.
- [ ] Chaque erreur nomme le champ et la règle violée, jamais "erreur de validation" seul.
- [ ] Le serveur rejette tout payload invalide, même envoyé hors de l'UI (testable au curl).
- [ ] Une clé d'idempotence unique par tentative de soumission d'un même formulaire ouvert.
- [ ] Le serveur stocke la réponse associée à la clé et la rejoue, il ne recrée jamais la ressource.
- [ ] Le bouton de soumission se désactive pendant l'envoi (confort, pas garantie).

## Optimisme UI : grille de décision rapide

```
Action réversible + faible enjeu de conflit  → optimiste OK (archiver, marquer lu, aimer)
Action irréversible OU forte concurrence     → pessimiste (réserver, payer, envoyer définitif)
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

## Vocabulaire à tenir prêt à l'oral

- **Race condition** : deux actions concurrentes dont le résultat dépend de l'ordre d'arrivée.
- **Idempotence** : une opération répétée produit le même effet qu'exécutée une seule fois.
- **UI optimiste** : appliquer un changement à l'écran avant confirmation serveur.
- **État partiel** : succès incomplet, ni erreur totale ni succès plein.
- **Layout shift** : déplacement visuel du contenu causé par une mise en page instable au chargement.
