# Grimoire — API Dojo

## Vocabulaire

- **Contrat d'API** : l'ensemble des garanties sur lesquelles un client a le droit de
  s'appuyer sans risquer une casse au prochain déploiement. La documentation décrit le
  contrat ; elle n'EST pas le contrat si elle n'est pas tenue.
- **Compatibilité ascendante** : un client ancien continue de fonctionner face à un serveur
  nouveau. C'est la direction qui protège les clients externes non maîtrisés.
- **Compatibilité descendante** : un client nouveau continue de fonctionner face à un serveur
  ancien. Critique en déploiement progressif et en systèmes de messages.
- **Idempotence** : propriété d'une opération dont l'exécution répétée produit le même
  résultat que l'exécution unique. `GET`, `PUT`, `DELETE` le sont par nature ; `POST` non.
- **Idempotency key** : identifiant généré côté client au moment de l'intention (pas à
  chaque tentative réseau), qui permet au serveur de reconnaître et dédupliquer un retry.
- **Authn (authentification)** : vérifier qui appelle.
- **Authz (autorisation)** : vérifier ce que cet appelant a le droit de faire, précisément.
- **Scope** : périmètre d'action accordé à un token, censé suivre le principe du moindre
  privilège (juste assez d'accès, pas plus).
- **Frontière de confiance** : point où une donnée ou une requête passe d'une zone contrôlée
  à une zone non contrôlée ; chaque franchissement exige sa propre vérification.
- **Rate limiting** : limitation du débit d'appels d'un client, protège autant contre les
  clients honnêtes mal configurés que contre les attaques délibérées.
- **Cache HTTP (ETag, Cache-Control)** : mécanisme pour éviter de retransmettre un contenu
  identique à un appel précédent.
- **Latence perçue** : le temps d'attente ressenti par l'utilisateur, distinct du temps de
  réponse mesuré côté serveur ; se réduit aussi par la structure de l'attente, pas seulement
  sa durée.

## Tableau des codes HTTP et de leur "retryabilité"

```text
400 non-retryable   401 non-retryable   403 non-retryable   404 non-retryable
409 retryable après relecture d'état    422 non-retryable
429 retryable après Retry-After         500 retryable avec prudence + idempotence
503 retryable avec backoff
```

## Checklist avant d'exposer un endpoint à un deuxième appelant

- [ ] Le contrat de réponse est écrit (schéma ou document), pas seulement implicite dans le
      code du serveur.
- [ ] Chaque erreur possible a un `code` machine stable, en plus d'un message humain.
- [ ] Tout `POST`/`PATCH` à effet accepte une clé d'idempotence et la vérifie réellement.
- [ ] Tout endpoint de liste est paginé, avec une taille de page maximale imposée.
- [ ] Le token d'un appelant porte un scope et un périmètre de ressources explicites, jamais
      un accès admin réutilisé par facilité.
- [ ] Chaque frontière de confiance revalide ce qui la traverse, sans supposer qu'une
      vérification faite plus haut suffit.
- [ ] Un timeout explicite existe sur chaque appel réseau sortant.
- [ ] Les retries automatiques utilisent un backoff exponentiel avec jitter, jamais une
      boucle serrée.
- [ ] Aucun secret n'apparaît dans le code source, une URL, ou un log.

## Stratégie de versionnage — arbre de décision

```text
Le changement retire, renomme ou change le type d'un champ existant ?
  OUI → rupture. Nouvelle version, ou report du changement à une refonte annoncée.
  NON ↓

Le changement ajoute une valeur à une énumération existante ?
  OUI → à risque pour les clients avec switch exhaustif. Documenter, annoncer, surveiller.
  NON ↓

Le changement ajoute un champ optionnel ou un nouvel endpoint ?
  OUI → additif, safe. Pas de nouvelle version nécessaire.
```

## Formule mentale pour la pagination

```text
Volume de la liste borné et petit (< quelques milliers, croissance lente) → offset acceptable
Volume non borné ou en forte croissance, ou insertions/suppressions fréquentes → curseur
```

## Heuristique de secours

Avant de livrer un endpoint, demande-toi : "si cet appelant retente cette requête trois fois
d'affilée à cause d'un réseau instable, qu'est-ce qui se passe exactement dans mon système ?"
Si la réponse n'est pas "rien de plus qu'une seule tentative", l'idempotence n'est pas
réglée — indépendamment de ce que dit le reste du code.
