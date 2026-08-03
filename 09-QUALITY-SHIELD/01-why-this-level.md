# Pourquoi "ça marche chez moi" ne suffit jamais

## La scène

Karim livre l'API de réservation de créneaux du cabinet vétérinaire. En local, tout passe :
il crée un rendez-vous, l'annule, en recrée un autre. Trois semaines après la mise en
production, un vendredi soir, deux réceptionnistes de deux sites différents réservent le
même créneau du même vétérinaire itinérant à quelques secondes d'intervalle. Le système
accepte les deux. Le lundi, un client trouve porte close alors qu'il avait une confirmation
par SMS. Personne n'a été alerté dans l'intervalle — il n'existe aucune métrique qui aurait
montré un pic de doubles réservations, aucun test qui vérifiait la concurrence, et le seul
moyen de savoir qu'un problème existait était qu'un client se plaigne. Karim n'a pas manqué
de compétence. Il a manqué de bouclier : rien dans son système n'était construit pour
détecter et absorber ce que lui-même n'avait pas anticipé.

## Ce qui se passe vraiment

Le code qui "marche" en développement est validé sur un sous-ensemble de conditions choisi
par la personne qui l'a écrit — donc, par construction, un sous-ensemble qui exclut ses
propres angles morts. La qualité logicielle n'est pas un supplément moral ("bien coder"),
c'est une contre-mesure structurelle contre ce biais : elle ajoute des couches qui trouvent
les problèmes que l'auteur ne peut pas voir lui-même.

```text
Sans bouclier qualité                    Avec bouclier qualité
                                          
  code ──► démo locale ──► prod           code ──► tests ciblés sur le risque
     │         │                             │         │
     │      "ça marche"                      │      revue de code (angle mort
     │                                       │       d'un pair)
     │                                        │         │
     └──► seul juge : l'auteur               │      observabilité (le système
                                              │       parle avant le client)
                                              │         │
                                              └──► prod, avec des capteurs actifs
```

Ce bouclier a quatre couches, chacune couvrant un moment différent du cycle de vie du bug :

1. **Les tests** (leçon 02) : trouvent le problème *avant* que le code parte en production,
   à condition de tester ce qui coûte cher si ça casse, pas tout ce qui est facile à tester.
2. **L'observabilité** (leçon 03) : trouve le problème *en production*, en minutes, avant
   qu'un utilisateur ait besoin de se plaindre pour qu'on le sache.
3. **La revue et la CI** (leçon 04) : trouvent le problème *avant qu'il n'entre dans le
   code partagé*, en ajoutant un regard extérieur et des garde-fous automatiques.
4. **La gestion d'incident** (leçon 05) : traite le problème *une fois qu'il est arrivé
   quand même* — parce qu'il arrivera quand même — sans détruire la confiance de l'équipe.

Aucune de ces couches n'est suffisante seule. Des tests parfaits n'empêchent pas un problème
de concurrence qui n'apparaît qu'à charge réelle. Une observabilité parfaite ne répare rien
si personne ne regarde l'alerte. Une revue de code parfaite n'attrape pas un bug qui dépend
du comportement réel des utilisateurs. C'est la superposition qui protège, pas une couche
isolée.

### Le coût, toujours le coût

Ce niveau refuse une idée répandue et fausse : "plus de tests, plus de logs, plus de
process, c'est toujours mieux". Chaque couche a un coût réel (temps d'écriture, temps de
maintenance, bruit cognitif) et ce coût doit être mis en face du coût de la panne qu'elle
évite. Un test qui protège une fonction jamais appelée en production coûte plus qu'il ne
rapporte. Une alerte qui se déclenche dix fois par jour sans qu'aucune ne soit actionnable
coûte plus qu'elle ne rapporte, parce qu'elle entraîne l'équipe à ignorer les alertes.

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Aucun bouclier ("ça marche en démo") | Rapide à livrer, séduisant à court terme | Vitesse maximale sur un prototype jetable | Prototype exploratoire sans utilisateur réel, jamais un système qui reste en production |
| Bouclier qualité proportionné au coût de panne | Investissement continu (temps de test, d'observabilité, de revue) | Les pannes coûteuses sont détectées tôt et corrigées vite, la confiance dans le système grandit | Tout système avec des utilisateurs réels et des conséquences réelles en cas d'erreur |

## Pièges classiques

- Croire que "ça marche en local" est une preuve de qualité — le symptôme est un bug qui
  n'apparaît qu'en production, sous charge réelle ou concurrence réelle.
- Ajouter des tests et des logs partout sans les rattacher à un coût de panne réel — le
  symptôme est une CI lente et une suite de logs bruyante que personne ne lit plus.
- Traiter la qualité comme une étape finale ("on nettoiera après la démo") — le symptôme est
  qu'elle n'arrive jamais, parce que la prochaine urgence prend toujours la priorité.
- Considérer un incident comme un échec individuel à cacher — le symptôme est qu'il se
  reproduit, parce que personne n'a eu le droit d'en tirer une leçon publique.

## Ce que tu dois savoir défendre

1. Pourquoi "ça marche en local" ne prouve rien sur le comportement en production, même si
   le code est objectivement correct pour les cas testés.
2. Explique pourquoi les quatre couches du bouclier qualité (tests, observabilité, revue/CI,
   gestion d'incident) sont complémentaires et pas substituables l'une à l'autre.
3. Donne un exemple de coût caché d'un excès de qualité mal ciblée (trop de tests, trop
   d'alertes) et explique le mécanisme par lequel il devient contre-productif.
