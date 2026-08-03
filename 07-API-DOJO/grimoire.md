# Grimoire : API Dojo

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| Contrat d'API | L'ensemble des garanties sur lesquelles un client a le droit de s'appuyer sans risquer une casse au prochain deploiement. La documentation decrit le contrat ; elle n'EST pas le contrat si elle n'est pas tenue. | Un deploiement casse des clients qui faisaient confiance a un comportement jamais formalise. | Ton contrat est-il ecrit quelque part, ou seulement implicite dans le code du serveur ? |
| Compatibilite ascendante | Un client ancien continue de fonctionner face a un serveur nouveau. C'est la direction qui protege les clients externes non maitrises. | Un client externe que tu ne controles pas casse a ton prochain deploiement, sans prevenir personne. | Un client qui n'a pas ete mis a jour depuis six mois fonctionne-t-il encore avec ta derniere version ? |
| Compatibilite descendante | Un client nouveau continue de fonctionner face a un serveur ancien. Critique en deploiement progressif et en systemes de messages. | Un deploiement progressif casse en plein rollout, entre l'ancienne et la nouvelle version du serveur. | Que se passe-t-il si ton client nouveau parle a un serveur pas encore mis a jour ? |
| Idempotence | Propriete d'une operation dont l'execution repetee produit le meme resultat que l'execution unique. GET, PUT, DELETE le sont par nature ; POST non. | Un retry reseau duplique une commande, un paiement, un envoi : l'utilisateur est facture deux fois. | Quelles operations de ton API sont idempotentes, et comment le garantis-tu pour les autres ? |
| Idempotency key | Identifiant genere cote client au moment de l'intention (pas a chaque tentative reseau), qui permet au serveur de reconnaitre et dedupliquer un retry. | Sans elle, chaque coupure reseau cree un doublon que le serveur ne peut pas detecter. | Ta cle d'idempotence est-elle generee a l'intention ou a chaque tentative reseau ? |
| Authn / Authz | Authn verifie qui appelle ; Authz verifie ce que cet appelant a le droit de faire, precisement. | Un appelant identifie mais mal autorise accede a des donnees ou actions hors de son perimetre. | Ton systeme confond-il "je sais qui tu es" avec "je sais ce que tu as le droit de faire" ? |
| Scope et frontiere de confiance | Le scope est le perimetre d'action accorde a un token (moindre privilege). La frontiere de confiance est le point ou une requete passe d'une zone controlee a une zone non controlee, et exige sa propre verification. | Un token trop large fuite au-dela de son usage prevu, et une frontiere non revalidee laisse passer une requete deja compromise en amont. | Chaque frontiere de confiance de ton systeme revalide-t-elle ce qui la traverse, sans supposer qu'un controle plus haut suffit ? |
| Rate limiting | Limitation du debit d'appels d'un client, protege autant contre les clients honnetes mal configures que contre les attaques deliberees. | Un client mal configure ou une attaque sature ton service sans limite, et degrade tout le monde. | Que se passe-t-il concretement si un client rejoue la meme requete cent fois par seconde ? |
| Cache HTTP (ETag, Cache-Control) | Mecanisme pour eviter de retransmettre un contenu identique a un appel precedent. | Chaque appel retransmet des donnees inchangees, gonflant latence et cout reseau pour rien. | Sur quel endpoint de ton API un cache HTTP ferait-il une vraie difference ? |
| Latence percue | Le temps d'attente ressenti par l'utilisateur, distinct du temps de reponse mesure cote serveur ; se reduit aussi par la structure de l'attente, pas seulement sa duree. | On optimise le temps serveur sans jamais ameliorer ce que l'utilisateur ressent reellement. | Comment reduirais-tu la latence percue sans forcement reduire le temps de reponse mesure ? |

## Comportements evalues en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'echec |
| --- | --- | --- |
| Refus argumente des deux raccourcis | Le refus s'appuie sur un mecanisme du niveau (scope, moindre privilege, frontiere de confiance, transmission de secret), pas sur une prudence generique | Tu dis juste "c'est risque" sans nommer le mecanisme concret qui casse |
| Solution livrable en trois jours | La proposition est reellement realisable dans le delai avec les moyens actuels, sans exiger un systeme generique complet non existant | Tu proposes un systeme de scopes generique complet, irrealiste en trois jours |
| Reponse au manque d'outillage | La solution minimale (table de correspondance verifiee a l'appel) resout le probleme reel sans sur-ingenierie disproportionnee au delai | Tu construis plus que necessaire, ou tu bricoles une solution qui ne verifie rien reellement |
| Mecanisme anti-recidive | L'engagement propose s'integre a un processus existant (signature commerciale) et est verifiable, pas un voeu pieux | Tu promets "on fera attention la prochaine fois" sans mecanisme dans un processus reel |
| Ton | La decision est assumee et defendable simultanement devant le commercial presse et le partenaire qui attend sa cle, sans ceder sur le fond pour gagner du temps | Tu cedes sur le fond (token admin, cle en clair) pour eviter le conflit immediat |

## Tableau des codes HTTP et de leur "retryabilite"

```text
400 non-retryable   401 non-retryable   403 non-retryable   404 non-retryable
409 retryable apres relecture d'etat    422 non-retryable
429 retryable apres Retry-After         500 retryable avec prudence + idempotence
503 retryable avec backoff
```

## Checklist avant d'exposer un endpoint a un deuxieme appelant

- [ ] Le contrat de reponse est ecrit (schema ou document), pas seulement implicite dans le
      code du serveur.
- [ ] Chaque erreur possible a un `code` machine stable, en plus d'un message humain.
- [ ] Tout `POST`/`PATCH` a effet accepte une cle d'idempotence et la verifie reellement.
- [ ] Tout endpoint de liste est pagine, avec une taille de page maximale imposee.
- [ ] Le token d'un appelant porte un scope et un perimetre de ressources explicites, jamais
      un acces admin reutilise par facilite.
- [ ] Chaque frontiere de confiance revalide ce qui la traverse, sans supposer qu'une
      verification faite plus haut suffit.
- [ ] Un timeout explicite existe sur chaque appel reseau sortant.
- [ ] Les retries automatiques utilisent un backoff exponentiel avec jitter, jamais une
      boucle serree.
- [ ] Aucun secret n'apparait dans le code source, une URL, ou un log.

## Strategie de versionnage : arbre de decision

```text
Le changement retire, renomme ou change le type d'un champ existant ?
  OUI --> rupture. Nouvelle version, ou report du changement a une refonte annoncee.
  NON |

Le changement ajoute une valeur a une enumeration existante ?
  OUI --> a risque pour les clients avec switch exhaustif. Documenter, annoncer, surveiller.
  NON |

Le changement ajoute un champ optionnel ou un nouvel endpoint ?
  OUI --> additif, safe. Pas de nouvelle version necessaire.
```

## Formule mentale pour la pagination

```text
Volume de la liste borne et petit (< quelques milliers, croissance lente) --> offset acceptable
Volume non borne ou en forte croissance, ou insertions/suppressions frequentes --> curseur
```

## Heuristique de secours

Avant de livrer un endpoint, demande-toi : "si cet appelant retente cette requete trois fois
d'affilee a cause d'un reseau instable, qu'est-ce qui se passe exactement dans mon systeme ?"
Si la reponse n'est pas "rien de plus qu'une seule tentative", l'idempotence n'est pas
reglee : independamment de ce que dit le reste du code.
