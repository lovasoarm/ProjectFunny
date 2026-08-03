# Le contrat avant le code

## Le piège

L'équipe de refacturation d'énergie expose une API `GET /consumptions/{siteId}` à trois
partenaires : un comparateur de prix, un cabinet d'audit énergétique, et le service interne
de reporting. Un développeur remarque que le champ `unit` (toujours `"kWh"`) est redondant
puisqu'il ne varie jamais, et le supprime pour "nettoyer la réponse" dans le même commit qui
ajoute un nouveau champ `co2Estimate`. Déploiement un vendredi soir, aucun changement de
version d'API affiché nulle part : c'est toujours `/v1/consumptions`. Le comparateur de prix,
qui lisait `response.unit` pour l'afficher à côté du chiffre, plante en production le samedi
matin sur une exception "cannot read property of undefined", pendant le pic de trafic du
week-end où les gens comparent leurs fournisseurs. Personne dans l'équipe de refacturation
n'a fait exprès de casser quoi que ce soit : supprimer un champ qui semble inutile ressemble
à un ménage, pas à une décision de rupture de contrat. C'est exactement le problème : ça en
était une, et personne ne l'a vue.

## Ce qui se passe vraiment

### Un contrat, c'est ce que le client peut supposer vrai sans te demander

Le contrat d'une API n'est pas sa documentation : la documentation peut être fausse ou en
retard. Le contrat, c'est l'ensemble des garanties sur lesquelles un client a le droit de
s'appuyer sans risquer une casse au prochain déploiement. Tant que ces garanties sont
respectées, tu peux changer n'importe quoi à l'intérieur de ton système. Dès qu'une seule est
violée, tu casses potentiellement chaque client qui existe, même ceux que tu n'as jamais vus.

Écrire le contrat avant le code force une question qu'on saute sinon presque toujours :
**qu'est-ce que je promets, exactement, et à qui ?** Pas "qu'est-ce que mon endpoint retourne
aujourd'hui", mais "qu'est-ce qu'un client a le droit de dépendre".

```text
Contrat explicite (OpenAPI, JSON Schema, ou a minima un document écrit et versionné) :

GET /v1/consumptions/{siteId}
  Réponse 200 :
    siteId: string (requis, jamais null)
    periodeDebut: string ISO-8601 (requis)
    periodeFin: string ISO-8601 (requis)
    valeurKwh: number (requis, >= 0)
    unit: string (requis, toujours "kWh" pour l'instant : mais présent, ne pas retirer)
  Réponse 404 : si siteId inconnu
  Réponse 403 : si le token n'a pas le scope "consumptions:read" sur ce site

  Garanties additionnelles (non visibles dans un schéma seul) :
  - Les champs listés ne disparaissent jamais dans une version donnée.
  - De nouveaux champs peuvent apparaître à tout moment (les clients doivent les ignorer).
  - L'ordre des champs JSON n'est jamais garanti.
```

Ce document se discute et se revoit _avant_ d'écrire une ligne d'implémentation, avec les
équipes consommatrices si elles existent. Un champ qu'on veut retirer devient une conversation
explicite ("qui utilise `unit` aujourd'hui ?") au lieu d'un ménage solitaire un vendredi soir.

### Compatibilité ascendante et descendante : deux directions, deux garanties

Les deux termes se confondent souvent à l'oral ; ils protègent deux populations différentes.

```text
Compatibilité ASCENDANTE (backward compatibility)
  Un client ANCIEN continue de fonctionner face à un serveur NOUVEAU.
  → Le serveur évolue, les vieux clients ne le remarquent pas.
  → C'est la direction qui protège les clients externes que tu ne contrôles pas
    (l'appli mobile du vétérinaire encore en version 1.2 sur le téléphone d'un client).

Compatibilité DESCENDANTE (forward compatibility)
  Un client NOUVEAU continue de fonctionner face à un serveur ANCIEN.
  → Rare à devoir garantir côté API HTTP classique (le serveur est en général sous ton
    contrôle et à jour), mais critique dans les systèmes de messages/événements où un
    consommateur peut être déployé avant un producteur, ou dans un déploiement progressif
    (canary) où d'anciennes instances du serveur tournent encore quelques minutes.
```

Un changement rétrocompatible ascendant typique : ajouter un champ optionnel, ajouter un
nouvel endpoint, assouplir une validation d'entrée (accepter plus de valeurs qu'avant).

Un changement qui casse la compatibilité ascendante, presque toujours à tort pris pour un
détail : retirer un champ, renommer un champ, changer le type d'un champ (`number` devient
`string`), changer le sens d'un champ existant, resserrer une validation (refuser une valeur
auparavant acceptée), changer un code de statut HTTP pour un cas déjà documenté.

```text
Changement                              Ascendant-compatible ?
─────────────────────────────────────  ───────────────────────
Ajouter un champ optionnel en réponse   Oui
Ajouter un endpoint                     Oui
Ajouter une valeur d'énumération        Non : un client avec un switch/case exhaustif
                                         qui ne gère pas la valeur inconnue peut planter
                                         ou, pire, mal se comporter silencieusement
Retirer un champ, même "inutile"        Non, presque toujours
Rendre un champ optionnel obligatoire   Non : les anciens clients ne l'envoient pas
Changer number en string                Non, casse tout parseur strict côté client
Ajouter un champ obligatoire en entrée  Non, sauf valeur par défaut appliquée côté serveur
```

Le cas des énumérations mérite un arrêt : ajouter une valeur semble anodin, mais un client
qui fait `switch (status) { case "OPEN": ...; case "CLOSED": ...; default: throw }` plante
sur toute nouvelle valeur. La bonne pratique côté client est de toujours prévoir un cas par
défaut qui dégrade proprement (afficher "statut inconnu" plutôt que planter) : mais tu ne
contrôles pas comment un client externe a écrit son code, donc côté API, traite toute
extension d'énumération comme un changement à risque, à documenter et si possible à
introduire progressivement.

### Versionnage : donner un nom au moment où tu casses le contrat

Le versionnage n'est pas une case à cocher esthétique dans l'URL. C'est le mécanisme qui te
permet de dire honnêtement "ce contrat a changé" sans forcer tous les clients à changer au
même instant que toi.

```text
Stratégies de versionnage, avec leur coût réel :

1. Version dans l'URL           /v1/consumptions   /v2/consumptions
   + Explicite, facile à router, facile à observer dans les logs d'accès
   - Multiplie les endpoints si beaucoup de ressources versionnent indépendamment
   - Tentation de versionner toute l'API d'un coup pour un seul changement local

2. Version dans un header        Accept: application/vnd.energie.v2+json
   + Une seule URL logique par ressource, plus "propre" au sens REST strict
   - Invisible dans les logs bruts, invisible en collant une URL dans un navigateur,
     oublié par la moitié des clients qui n'envoient pas le header et tombent sur un défaut

3. Pas de version, contrat additif uniquement (approche "tolerant reader")
   + Aucune gestion de version à faire vivre, pas de v1/v2 à maintenir en parallèle
   - Exige une discipline stricte de non-rupture, ne permet jamais de retirer une erreur
     de conception initiale sans détour (champ mal nommé qui reste à vie)
```

Le choix le plus robuste en pratique combine les deux dernières approches : versionner
uniquement quand un changement est _réellement_ non rétrocompatible (rare si le contrat est
pris au sérieux), et pousser fort vers des changements additifs le reste du temps. Verser
dans `/v2/` à la moindre modification produit un cimetière de versions que personne ne migre
jamais : le pire état possible, pire encore que ne pas versionner du tout.

### Le cycle de vie d'une rupture de contrat assumée

Quand une rupture est réellement nécessaire (le champ `unit` doit vraiment disparaître parce
que l'unité varie enfin selon les pays), le contrat impose un chemin, pas un couperet :

```text
1. Publier la v2 en parallèle de la v1, jamais en remplacement immédiat
2. Annoncer une date de dépréciation de la v1 (ex: 6 mois), avec un canal réel vers les
   consommateurs connus (email, changelog, header Deprecation dans les réponses v1)
3. Observer les logs d'accès pour voir qui appelle encore la v1, et avec quelle fréquence
4. Ne retirer la v1 qu'après un signal explicite (période écoulée + trafic résiduel proche
   de zéro ou accord explicite des derniers appelants connus)
```

Sauter l'étape 3 est le piège le plus fréquent : une équipe suppose que "tout le monde a
migré" parce que l'annonce a été envoyée, sans jamais vérifier dans les métriques réelles
qui appelle encore l'ancienne version.

## Compromis

| Option                                                   | Coût                                                                                   | Bénéfice                                                                                                 | Quand choisir                                               |
| -------------------------------------------------------- | -------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| Contrat formel écrit avant le code (OpenAPI/JSON Schema) | Temps de rédaction et de revue avant de coder, discipline de mise à jour               | Rupture détectable automatiquement (diff de schéma en CI), onboarding de nouveaux consommateurs facilité | Dès qu'un deuxième consommateur existe ou est prévu         |
| Contrat implicite ("le code fait foi")                   | Rien à écrire à l'avance                                                               | Vitesse initiale                                                                                         | Prototype à un seul consommateur interne, jetable sous peu  |
| Versionnage dans l'URL                                   | Endpoints dupliqués à maintenir en parallèle pendant la transition                     | Simplicité de routage, visibilité dans les logs                                                          | API publique avec des consommateurs externes non coordonnés |
| Additif uniquement, sans version                         | Discipline stricte, impossible de corriger une erreur de conception sans contournement | Zéro gestion de version, migration continue sans à-coup                                                  | API interne à consommateurs peu nombreux et coordonnés      |

## Pièges classiques

- **Le "nettoyage" qui casse un client.** Symptôme : un champ retiré parce qu'il semblait
  redondant, un client externe plante en silence des jours après, sans lien évident avec le
  déploiement du vendredi précédent.
- **La v2 jamais migrée.** Symptôme : `/v1/` reçoit encore 40% du trafic deux ans après le
  lancement de `/v2/`, parce que personne n'a jamais suivi les métriques d'usage réel.
- **Le contrat qui existe seulement dans la tête d'un développeur parti.** Symptôme : aucune
  documentation à jour, la seule source de vérité est le code du serveur, que personne n'ose
  changer par peur de casser un consommateur inconnu.
- **L'énumération étendue sans avertissement.** Symptôme : un partenaire dont le code plante
  ou se comporte mal dès qu'une nouvelle valeur de statut apparaît, alors que "on n'a fait
  qu'ajouter une valeur, on n'a rien cassé" du point de vue de l'équipe qui a publié.
- **Le changement de type silencieux.** Symptôme : un montant qui passait de `12.50` (number)
  à `"12.50"` (string) pour "gérer la précision décimale plus proprement", qui casse tout
  calcul côté client fait sur la valeur brute reçue.

## Ce que tu dois savoir défendre

- Explique la différence entre compatibilité ascendante et descendante avec un exemple
  concret pour chacune, tiré du fil rouge de ce niveau.
- Pourquoi retirer un champ JSON "inutile" est presque toujours une rupture de contrat, même
  si aucun consommateur connu ne semble l'utiliser au moment du changement.
- Donne les trois étapes minimales pour dépréciez une version d'API sans casser les
  consommateurs qui ne migrent pas immédiatement.
