# Authn vs authz, tokens, scopes, frontières de confiance

## La scène

Le cabinet d'audit énergétique, partenaire de la plateforme de refacturation, reçoit un
token d'API pour "récupérer la consommation de ses clients". Par facilité, l'équipe interne
lui génère un token identique à celui utilisé par le service de reporting interne : un token
"admin" qui a accès à tous les sites, de tous les clients, pas seulement ceux de ce cabinet.
Six mois plus tard, un stagiaire de ce cabinet, en testant une intégration, boucle
accidentellement sur tous les identifiants de site de 1 à 999999 et récupère les données de
consommation de milliers de foyers qui n'ont jamais entendu parler de ce cabinet d'audit.
Personne n'a piraté quoi que ce soit. Le token fonctionnait exactement comme configuré : le
problème est que "fonctionnait" et "aurait dû fonctionner" n'avaient jamais été distingués.

## Ce qui se passe vraiment

### Authentification et autorisation répondent à deux questions différentes

```text
Authentification (authn)  → "Qui es-tu ?"          Répond : une identité vérifiée
Autorisation (authz)      → "As-tu le droit ?"      Répond : oui/non pour UNE action précise

Un système peut authentifier parfaitement quelqu'un (il sait sans doute que c'est bien
le cabinet d'audit qui appelle) et pourtant mal autoriser (il ne vérifie pas QUELS sites
ce cabinet a le droit de consulter).
```

Confondre les deux mène systématiquement au même bug : "il a un token valide donc il a le
droit de tout faire". Un token valide prouve une identité, jamais un périmètre d'action.
Le périmètre doit être vérifié séparément, à chaque appel, pour chaque ressource précise.

### Tokens : ce qu'ils prouvent, ce qu'ils ne prouvent pas

```text
Type de token          Ce qu'il prouve                    Ce qu'il NE prouve pas
──────────────────     ─────────────────────────────      ──────────────────────────────
Clé API statique       "Cette clé a été émise par nous"   Que le porteur actuel est bien le
                                                            partenaire d'origine (vol de clé)
JWT signé              "Ce contenu n'a pas été modifié     Que le token n'a pas été volé ni
                        depuis son émission par nous"      rejoué avant son expiration
Token opaque + session Rien en lui-même : la vérité vit    Ne fonctionne pas sans un aller-
côté serveur           côté serveur, révocable à tout      retour de vérification ; latence
                       instant                             additionnelle par rapport au JWT
```

Le piège classique du JWT : parce qu'il est auto-porteur (le serveur peut le valider sans
appeler une base), on oublie qu'un JWT volé reste valide jusqu'à expiration : il n'existe pas
de révocation immédiate native. La durée de vie courte (minutes, pas jours) et un token de
rafraîchissement séparé, révocable, compensent ce manque.

### Scopes : réduire ce qu'un token prouvé peut faire

Un scope répond à la question qu'authn ne répond jamais : _une fois que je sais qui tu es,
que puis-je te laisser faire précisément ?_

```json
{
  "sub": "partner_audit_energie_782",
  "scopes": ["consumptions:read"],
  "siteIds": ["site_4471", "site_4472", "site_4499"],
  "exp": 1735689600
}
```

Le token de la scène aurait dû porter `siteIds` limité aux sites réellement sous contrat avec
ce cabinet, pas un accès global. La règle de conception : **un scope large "pour aller plus
vite à configurer" est une dette de sécurité qui ne se voit qu'au moment de l'incident.**

```text
Principe du moindre privilège appliqué à un scope d'API :

  Trop large   : scope "energie:*"            → un bug ou un vol expose tout
  Correct      : scope "consumptions:read"     → limité à l'action, mais tous les sites
                 + siteIds explicites            + limité aux ressources réellement autorisées
  Trop étroit  : un scope par endpoint exact   → explosion du nombre de scopes à gérer,
                 sans regroupement logique        friction opérationnelle sans gain réel
```

### Frontières de confiance : où la vérification doit obligatoirement avoir lieu

Une frontière de confiance est le point où des données ou une requête passent d'une zone où
elles sont contrôlées à une zone où elles ne le sont plus. Chaque franchissement de frontière
exige sa propre vérification : faire confiance à une vérification faite "plus haut" dans la
chaîne est l'erreur la plus fréquente en sécurité applicative.

```text
Appli mobile vétérinaire  ──[frontière 1: internet public]──>  API Gateway
        │ non fiable, code lisible et modifiable par l'utilisateur (device rooté, proxy MITM)

API Gateway  ──[frontière 2: réseau interne]──>  Service Rendez-vous
        │ le gateway a vérifié le token, MAIS le service ne doit pas supposer que
        │ "ça vient du gateway donc c'est propre" : un autre service interne compromis
        │ ou mal écrit peut appeler directement le Service Rendez-vous en contournant le gateway

Service Rendez-vous  ──[frontière 3: appel base de données]──>  Base de données
        │ une requête SQL construite à partir d'une entrée utilisateur non échappée
        │ transforme la base en frontière franchie sans vérification (injection SQL)
```

Chaque frontière doit revalider ce qui la traverse, même si une frontière précédente l'a
déjà fait. "Le frontend valide déjà ce champ" n'est jamais une raison de ne pas revalider
côté serveur : le frontend est sous le contrôle de l'utilisateur, pas le tien, dès l'instant
où le code tourne sur son appareil.

### Secrets : ce qu'on ne met jamais dans un contrat ni dans un dépôt

```text
Jamais dans le code source, jamais dans un commit, même privé :
  - Clés d'API, secrets de signature JWT, mots de passe de base de données

Jamais dans une URL :
  - Un token dans une query string (?api_key=...) finit dans les logs d'accès,
    dans l'historique du navigateur, dans les en-têtes Referer envoyés à des tiers

Toujours :
  - Variables d'environnement ou gestionnaire de secrets dédié (Vault, secret manager cloud)
  - Rotation possible sans redéploiement de code
  - Un secret compromis doit pouvoir être révoqué en minutes, pas nécessiter un redéploiement
```

Analogie : authentification et autorisation, c'est le contrôle d'accès en régie de spectacle
qui distingue le badge qui prouve qui tu es du laissez-passer qui dit où tu as le droit
d'aller, et le bracelet d'identification aux urgences qui prouve l'identité sans dire quels
soins sont autorisés.
Où l'analogie casse : un badge ou un bracelet volé se repère souvent à vue par un humain
vigilant. Un token volé est indiscernable du vrai pour un système automatisé tant que le
scope ou l'expiration n'a pas été vérifié.

## Compromis

| Option                                                    | Coût                                                                                    | Bénéfice                                                                   | Quand choisir                                                                                          |
| --------------------------------------------------------- | --------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| Scopes fins + périmètre de ressources explicite (siteIds) | Configuration plus lourde à l'émission de chaque token, plus de logique de vérification | Un token volé ou mal configuré limite les dégâts au strict périmètre prévu | Tout accès partenaire ou tiers, dès qu'une donnée sensible est en jeu                                  |
| Token "admin" unique réutilisé partout                    | Zéro configuration, rapide à mettre en place                                            | Aucun                                                                      | Jamais en dehors d'un script interne jetable à usage unique et éphémère                                |
| JWT auto-porteur                                          | Pas d'appel réseau pour valider, faible latence                                         | Scalabilité de la vérification                                             | Sessions courtes, API à fort volume où l'appel de vérification serait un goulot                        |
| Token opaque + vérification serveur                       | Latence additionnelle sur chaque appel                                                  | Révocation immédiate possible à tout instant                               | Accès sensibles où une révocation instantanée doit être garantie (accès partenaire suspecté compromis) |

## Pièges classiques

- **Le token partagé "pour aller plus vite".** Symptôme : un partenaire externe a accès à des
  données qui ne le concernent pas, découvert seulement au moment d'un incident ou d'un audit.
- **La confiance en cascade non revalidée.** Symptôme : un service interne accepte n'importe
  quelle requête venant du réseau interne sans revérifier de token, parce que "c'est interne
  donc c'est sûr" : jusqu'à ce qu'un autre service interne, compromis ou buggé, en abuse.
- **Le scope large "temporaire" qui devient permanent.** Symptôme : un accès élargi accordé
  pour débloquer un test un vendredi, jamais retiré, retrouvé un an plus tard en audit.
- **Le secret dans un commit historique.** Symptôme : même après suppression du fichier, le
  secret reste lisible dans l'historique git ; la seule remédiation valable est la rotation
  du secret, jamais un simple `git rm`.
- **Le token dans les logs.** Symptôme : un token transmis en query string ou loggé en clair
  dans les journaux applicatifs, exploitable par quiconque a accès en lecture aux logs, un
  périmètre souvent plus large que celui des accès à la base de données elle-même.

## Ce que tu dois savoir défendre

- Explique la différence entre authentification et autorisation avec l'exemple du cabinet
  d'audit énergétique, et pourquoi un token valide n'a jamais suffi à lui seul.
- Pourquoi "le frontend valide déjà ce champ" n'est jamais une raison suffisante de sauter
  la validation côté serveur.
- Donne un exemple concret où un JWT auto-porteur pose un problème qu'un token opaque
  résout, et le coût que ce dernier introduit en échange.
