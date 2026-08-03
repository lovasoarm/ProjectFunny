# Pourquoi ce niveau existe

## La scène

Le cabinet vétérinaire lance enfin son appli mobile pour que les propriétaires d'animaux
prennent rendez-vous eux-mêmes. L'API existe déjà, elle sert le site web depuis un an, elle
marche bien. L'équipe mobile branche l'appli dessus en trois jours. Deux semaines après le
lancement, un vétérinaire retrouve trois fois le même rendez-vous pour le même chat, à la
même heure, créé en triple par le même utilisateur. En creusant : sur le parking de la
clinique, le réseau 4G de l'utilisateur coupait une seconde après l'envoi de la requête,
l'appli affichait une roue qui tourne, l'utilisateur appuyait à nouveau sur "confirmer", et
l'appli retentait automatiquement l'envoi trente secondes plus tard sans jamais savoir si la
première tentative avait abouti. L'API, elle, a fait exactement ce qu'on lui demandait : créer
un rendez-vous à chaque requête reçue. Le bug n'est dans aucune ligne de code défectueuse.
Il est dans une hypothèse jamais écrite : "un appelant n'enverra jamais deux fois la même
requête".

## Ce qui se passe vraiment

Une API interne, appelée uniquement par le code que tu contrôles toi-même, tolère des
hypothèses implicites. Tu sais que le front n'enverra jamais un champ manquant parce que
c'est toi qui as écrit le formulaire qui l'empêche. Tu sais qu'un seul serveur appelle l'API,
donc pas de concurrence surprise. Tu peux casser un format de réponse un mardi et le
redéployer le même jour, parce que "les deux bouts" sont dans le même déploiement.

Le jour où un deuxième appelant existe : une appli mobile, un partenaire, un service tiers,
même un autre projet écrit par une autre équipe dans la même entreprise : toutes ces
hypothèses s'effondrent en même temps, et aucune ne prévient avant de casser quelque chose :

```text
API interne, un seul appelant maîtrisé      API publique, appelants multiples et hors contrôle
─────────────────────────────────────      ──────────────────────────────────────────────────
Tu déploies front et API ensemble       →   Les appelants sont déployés à des rythmes différents,
                                             parfois jamais mis à jour (appli mobile ancienne)

Une seule requête à la fois par flux   →   Retries automatiques, doubles clics, connexions
                                             instables : la même intention peut arriver 3 fois

Le format de réponse est un détail     →   Le format de réponse EST le produit : le casser
d'implémentation                            casse le client sans prévenir, souvent en silence

Un message d'erreur en français        →   Un code d'erreur doit être lisible par une machine
suffit pour un humain qui debug             avant d'être lisible par un humain

N'importe qui dans l'équipe peut       →   Un client externe peut interroger 1000 fois par
appeler l'API autant qu'il veut             seconde par accident (boucle infinie côté partenaire)

"On se fait confiance, c'est notre     →   Un token volé, un scope trop large, une frontière de
code" suffit comme sécurité                confiance floue = fuite de données patients ou factures
```

C'est le passage d'un **code qui répond** à un **contrat qui engage**. Une fonction interne
peut changer de signature librement tant que tu mets à jour tous ses appelants dans le même
commit. Une API publique ne peut pas : tu ne sais ni qui l'appelle, ni avec quelle version de
client, ni quand ce client sera mis à jour. Le vétérinaire de la scène n'a pas un bug de
code, il a un système qui a grandi sans que personne ne décide consciemment de passer d'un
mode à l'autre.

## Ce que ce niveau corrige

Ce niveau te donne les cinq réflexes qui séparent une API qui survit à son succès d'une API
qui s'effondre dès qu'elle rencontre un vrai appelant extérieur :

1. **Écrire le contrat avant le code**, pour que "casser un client" devienne une décision
   consciente et versionnée, jamais un accident de refactoring.
2. **Rendre les erreurs exploitables et les opérations rejouables sans danger**, pour que
   les retries : inévitables sur un réseau réel : ne dupliquent jamais une action.
3. **Séparer qui es-tu de ce que tu as le droit de faire**, pour qu'un token volé ou mal
   scope ne devienne pas une fuite de données ou une facture modifiée sans autorisation.
4. **Anticiper le succès** : pagination, limites de débit, cache : les problèmes qui
   n'existent pas à dix appels par jour et qui deviennent une panne à dix mille.
5. **Penser en dehors de ton propre déploiement** : ton API vit plus longtemps et plus
   largement que le code qui l'a écrite au départ.

## Ce qui casse sans ce niveau

- Des doublons silencieux (rendez-vous, factures, livraisons) causés par des retries sans
  garde-fou, découverts des jours plus tard par un humain qui recoupe des chiffres à la main.
- Des appelants externes cassés à chaque déploiement, parce qu'un renommage de champ JSON,
  anodin dans un IDE, est en réalité une rupture de contrat pour quelqu'un d'autre.
- Des incidents de sécurité qui ne sont pas des piratages sophistiqués mais des scopes trop
  larges accordés par facilité ("donne-lui un accès admin, ça ira plus vite").
- Une API qui tient à dix appels par minute et tombe à mille, sans qu'aucune alerte n'ait
  prévenu avant l'incident, parce que personne n'avait mesuré ni limité la charge acceptable.

## Ce que tu dois savoir défendre

- Explique, avec l'exemple du rendez-vous vétérinaire dupliqué, pourquoi le bug n'est ni dans
  le front ni dans l'API prise séparément.
- Donne un exemple concret de décision qui est acceptable pour une API interne à un seul
  appelant, et inacceptable dès qu'un deuxième appelant externe apparaît.
- Pourquoi "on se fait confiance, c'est notre code" cesse d'être un argument de sécurité
  valable dès qu'un token peut être volé ou qu'un partenaire externe existe.
