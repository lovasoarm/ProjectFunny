# Pourquoi ce niveau existe

## Le piège

Une association de tournées de livraison à vélo pour commerçants de quartier te demande une
plateforme complète : inscription des commerçants, catalogue produit, prise de commande,
optimisation de tournée, paiement en ligne, notifications SMS, tableau de bord de suivi
en temps réel pour les livreurs. Tu regardes ça, tu estimes trois mois, tu te lances en
construisant "les fondations" d'abord : base de données complète, authentification, API
générique pour tous les modules. Après six semaines, rien n'est utilisable. Pas une seule
commande n'a encore été livrée en vrai. L'association, qui payait sur trésorerie limitée,
arrête le projet à mi-chemin. Ce qui existe : une base solide, zéro valeur livrée.

## Ce qui se passe vraiment

Le découpage d'un projet en couches techniques (d'abord la base de données, puis l'API,
puis le frontend) donne l'illusion de progresser methodiquement, mais ne produit aucune
valeur testable avant que toutes les couches soient terminées. À l'inverse, un découpage en
tranches verticales — un scénario complet, du bouton cliqué jusqu'à la ligne en base, même
limité à un seul commerçant et une seule tournée — produit une chose utilisable et testable
en quelques jours. Ce niveau existe parce que la compétence de découpage n'est pas innée :
la tentation naturelle d'un développeur est de construire par couches, parce que c'est ainsi
qu'on raisonne techniquement. Il faut apprendre à raisonner par valeur livrée à la place.

```text
Découpage horizontal (couches)          Découpage vertical (tranches de valeur)
┌─────────────────────────┐             ┌───────┐ ┌───────┐ ┌───────┐
│ Base de données complète│             │Scénario│ │Scénario│ │Scénario│
├─────────────────────────┤             │   1    │ │   2    │ │   3    │
│ API générique            │             │(bout à │ │(bout à │ │(bout à │
├─────────────────────────┤             │ bout)  │ │ bout)  │ │ bout)  │
│ Frontend complet          │             └───────┘ └───────┘ └───────┘
└─────────────────────────┘             livrable    livrable    livrable
rien d'utilisable avant la fin           dès la semaine 1
```

## Ce qui casse sans ce niveau

- **Le projet meurt avant de livrer une seule valeur mesurable**, souvent parce que le budget
  ou la patience s'épuisent avant la fin de la dernière couche.
- **Les estimations explosent silencieusement.** Sans tranches, l'incertitude s'accumule sans
  qu'aucun signal d'alerte n'apparaisse avant la date de livraison finale.
- **Impossible de prioriser en cours de route.** Si tout est découpé par couche technique, tu
  ne peux pas dire "livrons d'abord ce qui a le plus de valeur" — tout dépend de tout.
- **Le retour terrain arrive trop tard.** Sans tranche livrée tôt, personne ne sait avant des
  semaines si l'hypothèse produit tient la route.

## Ce que tu dois savoir défendre

- Pourquoi un découpage par couches techniques retarde systématiquement le retour terrain,
  même si chaque couche est bien construite.
- Donne un exemple, différent de celui de ce fichier, où découper par couches semblait
  raisonnable mais a fait échouer un projet.
- Ce qui distingue une "fondation nécessaire" d'un prétexte pour retarder la livraison de
  valeur.
