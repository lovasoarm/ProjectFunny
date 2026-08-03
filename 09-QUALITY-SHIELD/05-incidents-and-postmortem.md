# Gestion d'incident, postmortem sans blâme

## Le piège

Un vendredi soir, un déploiement du service de vérification de température du système de
tournées de livraison casse silencieusement le calcul de conformité de la chaîne du froid :
toutes les tournées sont désormais marquées "conformes" par défaut, y compris celles qui ne
le sont pas. Personne ne le détecte pendant six heures, jusqu'à ce qu'un client reçoive une
livraison de produits périmés et appelle en furie. Dans l'heure qui suit, trois choses
tournent mal en même temps : personne ne sait qui a le droit de décider de couper le
déploiement, trois personnes différentes touchent à la base de données en parallèle sans se
coordonner en pensant "réparer plus vite", et le lundi, la réunion de debrief se transforme
en recherche du coupable — la développeuse qui a fait le déploiement du vendredi soir quitte
la réunion en larmes, jure de ne plus jamais déployer un vendredi, et l'équipe n'a toujours
aucune mesure concrète pour empêcher que ça se reproduise un mardi.

## Ce qui se passe vraiment

### Un incident se gère en deux temps qui ne doivent jamais se mélanger

Le temps de la réparation (pendant l'incident) et le temps de l'analyse (après l'incident)
ont des objectifs opposés, et les confondre aggrave les deux.

```text
Pendant l'incident : objectif = réduire l'impact, vite
  - Une seule personne décide (l'incident commander), même si elle n'a pas toutes les réponses.
  - On privilégie l'action réversible et rapide (rollback) à l'analyse complète de la cause.
  - On communique l'état à intervalles réguliers, même "on ne sait pas encore" est une info utile.

Après l'incident : objectif = comprendre et changer le système, sans urgence de temps
  - On rassemble les faits avec les logs et métriques réels, pas la mémoire de chacun.
  - On cherche les causes structurelles, pas la personne qui a appuyé sur le bouton.
  - On produit des actions concrètes assignées, pas des vœux pieux ("faire plus attention").
```

### Pendant l'incident : la coordination compte plus que l'expertise individuelle

Le rôle d'incident commander n'exige pas d'être la personne la plus compétente
techniquement sur le sujet en cause — il exige de centraliser les décisions pour éviter que
plusieurs personnes bien intentionnées se marchent dessus.

```text
Sans coordination                       Avec incident commander désigné
                                         
Dev A : je restaure la base              Dev A, B, C : diagnostiquent, remontent
Dev B : je redéploie l'ancienne version  les options à l'incident commander
Dev C : je coupe le service               │
  → trois actions simultanées,            ▼
    résultat final imprévisible,      Incident commander : décide "on redéploie
    personne ne sait ce que              l'ancienne version, tout le monde stoppe
    les autres viennent de faire"        les autres tentatives", communique l'état
                                          toutes les 15 minutes
```

Priorité pendant l'incident : réduire l'impact d'abord (rollback, désactivation d'une
fonctionnalité, bascule vers un mode dégradé connu), comprendre la cause racine ensuite. Un
rollback qui répare le symptôme sans qu'on comprenne encore la cause exacte est un succès
d'incident, pas un échec d'analyse — les deux temps sont différents.

```typescript
// Exemple de garde-fou simple qui limite l'impact pendant qu'on investigue :
// un feature flag qui permet de couper une fonctionnalité en un clic,
// sans redéploiement complet, pendant qu'on cherche la cause exacte.
if (featureFlags.isEnabled("cold-chain-auto-check")) {
  return checkColdChainCompliance(tourId, readings, thresholds);
}
return { status: "manual_review_required", tourId }; // dégradé mais honnête
```

### Le postmortem sans blâme : chercher le système, pas la personne

Un postmortem sans blâme part d'un principe non négociable : la personne qui a déclenché
l'incident a pris la meilleure décision qu'elle pouvait prendre avec l'information et les
outils qu'elle avait à ce moment-là. Si le résultat est mauvais, la question utile n'est pas
"pourquoi a-t-elle fait ça" mais "pourquoi le système a-t-il permis que cette action, prise
de bonne foi, cause autant de dégâts".

```text
Recherche du coupable                    Recherche de la cause structurelle
                                          
"Qui a déployé vendredi soir ?"          "Pourquoi le déploiement de vendredi
  → réponse : une personne, qui             soir n'a-t-il déclenché aucune alerte
    culpabilise, l'équipe apprend           pendant six heures ?"
    'ne jamais déployer le vendredi'        → réponse : aucune métrique ne
    (mais rien n'empêche le même              surveillait le taux de conformité
    bug un mardi)                            chaîne du froid en temps réel
                                          → action concrète : ajouter une alerte
                                             sur ce taux, avec seuil et propriétaire
```

Un postmortem sans blâme n'est pas un exercice de gentillesse — c'est un mécanisme
d'efficacité : une équipe qui craint d'être blâmée cache des informations (elle minimise ce
qu'elle a touché, elle tarde à signaler un problème par peur), ce qui ralentit directement
la résolution du prochain incident.

### Structure d'un postmortem qui produit un vrai changement

```text
1. Chronologie factuelle (horodatée, basée sur logs et métriques réels)
   14h01 — déploiement du service température, version 2.4.1
   14h03 — le taux de "conformité" passe de ~85% à 100% (aucune alerte configurée)
   20h12 — un client signale une livraison non conforme reçue
   20h45 — rollback vers la version 2.4.0, conformité réelle rétablie

2. Cause immédiate ET cause structurelle
   Immédiate : un changement de format de champ (`temp_celsius` → `temperature`) a fait
     échouer silencieusement la lecture des relevés, la fonction traitait l'absence de
     valeur comme "conforme par défaut".
   Structurelle : aucun test n'a couvert un changement de format de données externe, et
     aucune métrique ne surveillait le taux de conformité en temps réel.

3. Ce qui a bien fonctionné (à ne pas perdre en corrigeant le reste)
   Le rollback a été rapide une fois la cause identifiée (moins de 10 minutes).

4. Actions concrètes, assignées, avec une date
   - Ajouter une alerte sur une variation brutale du taux de conformité (responsable, date).
   - Ajouter un test d'intégration qui rejoue un changement de format de champ externe.
   - "Ne jamais échouer silencieusement" : traiter une donnée manquante comme un état
     d'erreur explicite, jamais comme une valeur par défaut favorable.
```

Un postmortem sans action assignée et datée n'a produit aucun changement — il a juste
documenté un problème qui se reproduira.

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Un incident commander unique désigné | Nécessite une astreinte ou un rôle tournant | Coordination claire, pas d'actions contradictoires | Systématique dès qu'un incident implique plus d'une personne |
| Réparer d'abord, comprendre la cause exacte ensuite | Peut laisser une cause mal comprise temporairement | Réduit l'impact utilisateur au plus vite | Presque toujours pendant un incident en cours |
| Postmortem sans blâme, orienté cause structurelle | Demande une discipline d'animation (recadrer si ça dérive vers l'accusation) | Actions correctives réelles, équipe qui reste honnête sur ses erreurs | Systématique après tout incident notable |
| Chercher un responsable individuel | Rapide, satisfait un besoin émotionnel immédiat | Aucune protection contre la récidive, équipe qui cache ses erreurs ensuite | Jamais comme pratique d'équipe |

## Pièges classiques

- Mélanger le temps de réparation et le temps d'analyse — le symptôme est une équipe qui
  débat de la cause exacte pendant que l'incident continue d'impacter des utilisateurs.
- Plusieurs personnes qui touchent au système en parallèle sans coordination pendant un
  incident — le symptôme est un état final imprévisible, parfois pire que le problème
  initial.
- Un postmortem qui se termine sans aucune action assignée et datée — le symptôme est le
  même incident, presque identique, six mois plus tard.
- Une culture qui blâme la personne à l'origine du déclenchement — le symptôme est que les
  incidents suivants sont signalés plus tard, ou minimisés, par peur des conséquences.
- Confondre "cause immédiate" (le bug technique précis) et "cause structurelle" (pourquoi le
  système a laissé ce bug produire autant de dégâts sans alerte) — le symptôme est un
  correctif qui empêche exactement ce bug de revenir, mais aucun autre.

## Ce que tu dois savoir défendre

1. Explique pourquoi il ne faut jamais mélanger le temps de réparation et le temps d'analyse
   pendant un incident, avec un exemple des conséquences si on les confond.
2. Explique le principe derrière le postmortem sans blâme, et pourquoi il produit de
   meilleurs résultats qu'une recherche de responsable, même en admettant qu'une personne a
   fait une erreur.
3. Donne un exemple de différence entre cause immédiate et cause structurelle d'un incident,
   et pourquoi corriger seulement la première ne suffit pas.
