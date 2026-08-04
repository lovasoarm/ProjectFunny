# Grimoire : Niveau 04, User Wizard

Mémo à ouvrir avant de coder un écran ou un formulaire critique. Sert à vérifier que chaque
état est géré et que rien ne ment à l'utilisateur, pas à réviser la théorie du DOM.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| États obligatoires | Les six situations qu'un affichage de données doit gérer explicitement (idle, loading, empty, ready, partial, overload, error). | `type EtatEcran = "idle" \| "loading" \| "empty" \| "ready" \| "partial" \| "overload" \| "error";` | plat pas encore commandé vs plat en cuisine vs plat servi en salle / ancre levée vs cap tenu vs mouillage forcé |
| État partiel | Succès incomplet, à ne jamais afficher comme un succès plein ni comme une erreur totale. | `if (recu < attendu) return { statut: "partial", recu, attendu };` | service à moitié terminé annoncé comme tel en salle / ravitaillement incomplet signalé avant le sommet |
| Overload | Volume trop grand pour l'UI, il faut forcer un filtre plutôt que tout rendre. | `if (items.length > SEUIL) return renderFiltreObligatoire();` | trop de commandes en cuisine pour tout servir d'un coup, on priorise / trop de bagages pour une seule cordée, on répartit |
| Machine à états d'un formulaire | Modélisation explicite des transitions (IDLE, DIRTY, ENVOI, CONFIRME, INCERTAIN), y compris le timeout. | `const suivant = { IDLE: "DIRTY", DIRTY: "ENVOI", ENVOI: "CONFIRME" }[etat];` | commande transmise en cuisine puis confirmée en salle, jamais l'inverse / manœuvre annoncée puis exécutée en mer, jamais l'inverse |
| Idempotence | Une opération répétée produit le même effet qu'exécutée une seule fois. | `await creerReservation({ idempotencyKey: cleUnique });` | un bénévole qui ne ressert pas deux fois le même plat sur un même bon / une même manœuvre rejouée qui ne double pas le nœud |
| Race condition | Deux actions concurrentes dont le résultat dépend de l'ordre d'arrivée. | `if (version !== versionAttendue) throw new ConflitEcriture();` | deux commis qui modifient le même plat en même temps sans se parler / deux cordées qui tirent la même corde sans coordination |
| UI optimiste | Appliquer un changement à l'écran avant confirmation serveur. | `setEtatLocal(nouvelEtat); api.confirmer().catch(() => rollback());` | annoncer un plat prêt avant la validation du chef, avec rattrapage si refusé / annoncer un créneau libre avant confirmation du refuge, avec rattrapage si complet |
| Formulaire qui ne ment pas | Validation identique côté client et serveur, erreurs nommées par champ et par règle. | `const erreur = schema.safeParse(payload).error?.formErrors.fieldErrors;` | ticket de commande relu en cuisine avant préparation, pas seulement pris en salle / plan de route revérifié au refuge avant de partir, pas seulement au départ |

## Défense orale

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| États obligatoires | L'UI ment sur l'état réel des données : succès affiché sur une erreur, ou inverse | Pour cet écran, montre-moi les six états rendus, pas seulement le cas ready |
| État partiel | L'utilisateur croit avoir toutes les données alors qu'il n'en a qu'une partie, décide sur une base fausse | Comment distingues-tu visuellement un succès partiel d'un succès complet ? |
| Overload | Le navigateur se fige à essayer de rendre des milliers de lignes d'un coup | Quel seuil de volume déclenche le passage en mode filtré forcé ? |
| Machine à états d'un formulaire | Un double clic ou un timeout renvoie deux fois la même action sans que personne ne le détecte | Que fait ton formulaire si la réponse serveur n'arrive jamais (timeout) ? |
| Idempotence | Un retry réseau ou un double clic crée deux fois la ressource (double réservation, double paiement) | Quelle est la clé d'idempotence de cette soumission, et qui la génère ? |
| Race condition | Deux utilisateurs modifient la même ressource en même temps, l'un écrase l'autre sans le savoir | Quel est le pire ordre d'arrivée possible pour ces deux actions concurrentes ? |
| UI optimiste | Un rollback silencieux laisse l'utilisateur croire à un succès qui n'a jamais eu lieu | Pour cette action, l'optimisme est-il justifié ou dangereux ? |
| Formulaire qui ne ment pas | Le client valide, le serveur accepte n'importe quoi envoyé hors UI (curl, script) | Que se passe-t-il si ce formulaire est soumis directement en curl avec un payload invalide ? |

Grille détaillée : voir [boss-fight.md](./boss-fight.md).

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
IDLE -> DIRTY -> ENVOI -> CONFIRME
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

## Si tu rates le boss-fight

Relis d'abord le critère qui a plafonné ta note : compréhension du risque, qualité de
l'alternative, ou honnêteté sur les limites. Reprends la scène et liste séparément les états
gérés et ceux oubliés avant de répondre à nouveau. Relis la machine à états ci-dessus. Attends
48 h avant de retenter le boss-fight pour juger la scène à froid. Si l'échec se reproduit sur
le même critère, redescends au niveau 03 relire "invariant d'une feature" : un état mal géré
est souvent un invariant que tu n'as pas nommé.
