# Découper en phases livrables, jalons vérifiables

## Le piège

Le projet : un système de suivi de tournées pour une entreprise de livraison de colis
frigorifiques, 30 chauffeurs, contraintes de température par créneau. Le chef de projet
découpe la roadmap en "Phase 1 : Backend (3 semaines)", "Phase 2 : Frontend (3 semaines)",
"Phase 3 : Intégration (2 semaines)". À la fin de la phase 1, le backend "est fini" — tous
les endpoints répondent. Mais personne n'a vérifié qu'un chauffeur peut réellement démarrer
une tournée depuis une tablette avec une connexion 3G intermittente en zone rurale, parce que
ça, c'est "phase 3". Résultat : l'intégration révèle que le format d'API choisi en phase 1 ne
supporte pas la reprise après coupure réseau, et il faut refaire une partie du backend en
phase 3. Le découpage par couche technique a caché le vrai risque jusqu'à la fin.

## Ce qui se passe vraiment

Une phase n'est utile que si elle réduit une incertitude ou livre une valeur vérifiable — et
idéalement les deux. Découper par couche technique (backend / frontend / intégration) ne
fait ni l'un ni l'autre avant la toute fin, parce qu'aucune couche seule ne prouve que le
système fonctionne pour l'utilisateur final.

```text
Découpage par couche (mauvais signal de progrès)
┌────────────┬────────────┬────────────┐
│  Backend   │  Frontend  │Intégration │
└────────────┴────────────┴────────────┘
     "fini"       "fini"      → premier moment où on sait si ça marche vraiment

Découpage par tranche verticale (bon signal de progrès)
┌──────────────┬──────────────┬──────────────┐
│ Une tournée,  │ Plusieurs    │ Coupures      │
│ un chauffeur, │ chauffeurs,  │ réseau,       │
│ réseau stable │ conflits de  │ reprise,      │
│ (bout en bout)│ créneaux     │ cas limites   │
└──────────────┴──────────────┴──────────────┘
   → à chaque phase, le système marche pour un
     cas réel, de bout en bout, vérifiable
```

Une tranche verticale traverse toutes les couches (base de données, backend, frontend) pour
un scénario réduit mais réel. Elle coûte plus cher à démarrer (il faut un peu de chaque
couche dès la phase 1) mais elle révèle les incompatibilités entre couches immédiatement,
au lieu de les cacher jusqu'à l'intégration.

### Un jalon vérifiable, ce n'est pas un pourcentage

"Phase 2 terminée à 80 %" n'est pas un jalon, c'est une estimation invérifiable — personne
ne peut confirmer ou infirmer ce chiffre de l'extérieur. Un jalon réel a trois propriétés :

1. **Binaire** : soit il est atteint, soit non. Pas de degré.
2. **Vérifiable par quelqu'un d'autre que l'auteur** : un tiers peut constater le résultat
   sans avoir à faire confiance à une déclaration.
3. **Daté et rattaché à un artefact** : un jalon sans date n'engage à rien ; un jalon sans
   artefact concret (un écran qui tourne, un rapport, un test qui passe) est une opinion.

```text
Mauvais jalon                          Bon jalon
"Le module de tournées                 "Un chauffeur peut, depuis une tablette
avance bien"                           réelle en mode avion puis reconnecté,
                                        voir sa tournée du jour se synchroniser
                                        sans perte de donnée — démontré le 14/03
                                        devant le product owner"
```

## Exemple : squelette de plan par tranches

```text
Objectif final : suivi de tournées avec 30 chauffeurs, contraintes de température,
                 réseau terrain instable.

Phase 1 (risque : le protocole de synchronisation tient-il hors ligne ?)
  → 1 chauffeur, 1 tournée, coupure réseau simulée, reprise vérifiée
  → jalon : démo live avec coupure réseau provoquée devant témoin

Phase 2 (risque : les conflits de créneaux entre chauffeurs sont-ils gérables ?)
  → 5 chauffeurs, créneaux qui se chevauchent, alertes de conflit
  → jalon : scénario de conflit réel rejoué, résolution visible à l'écran

Phase 3 (risque : le système tient-il à l'échelle réelle ?)
  → 30 chauffeurs simulés, charge réseau réaliste, alerte de température
  → jalon : test de charge avec seuils de température déclenchés et notifiés
```

Chaque phase attaque un risque nommé (voir la leçon suivante) et se termine par un jalon
vérifiable, pas par une couche technique terminée.

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Découpage par couche technique | Plus simple à planifier au début, chaque personne reste dans sa spécialité | Rassurant à court terme | Seulement si le système est déjà connu et sans risque d'intégration (rare) |
| Découpage par tranche verticale | Demande de la coordination dès la phase 1, chaque phase touche plusieurs couches | Révèle les incompatibilités tôt, chaque jalon prouve une valeur réelle | Dès qu'il y a une intégration incertaine (réseau, matériel, système tiers) |

## Pièges classiques

- Un jalon formulé comme un état d'esprit ("le module avance bien") — le symptôme est que
  personne ne peut dire si le jalon est atteint sans demander à l'auteur.
- Découper par couche parce que c'est plus confortable pour l'organisation des équipes — le
  symptôme est une intégration catastrophique en toute fin de projet.
- Fixer des jalons à des dates rondes (fin de mois, fin de sprint) sans lien avec un
  artefact réel — le symptôme est que le jalon "glisse" avec la date suivante sans
  conséquence.
- Multiplier les phases très fines pour donner une impression de contrôle — le symptôme est
  un plan illisible où personne ne voit plus quel risque chaque phase attaque.

## Ce que tu dois savoir défendre

1. Pourquoi un découpage par couche technique cache le risque d'intégration jusqu'à la fin
   du projet, même si chaque couche est bien construite individuellement.
2. Donne un exemple de jalon formulé en pourcentage, et reformule-le en jalon binaire,
   vérifiable et daté.
3. Pourquoi une tranche verticale coûte plus cher à démarrer qu'un découpage par couche, et
   pourquoi ce surcoût initial est rentable dès qu'il y a une incertitude d'intégration.
