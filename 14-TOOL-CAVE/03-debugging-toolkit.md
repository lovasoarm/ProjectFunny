# Méthode de debug : bissection, hypothèses, profilers, logs

## Le piège

La bibliothèque d'un club d'escalade gère le prêt de baudriers et de cordes avec un système
maison. Un vendredi, le rapport de disponibilité affiche parfois deux baudriers de plus que
le stock réel : mais pas toujours, et jamais de manière reproductible en local. Tu ouvres le
code, tu regardes la fonction de calcul de stock, elle te semble correcte. Tu ajoutes un
`console.log`, tu ne vois rien d'anormal sur ton poste. Tu conclus "ça doit être un problème
de cache côté client" et tu passes à autre chose. Trois semaines plus tard, le même bug
revient, plus grave. Tu n'as jamais eu de méthode : tu as eu une intuition non vérifiée que
tu as prise pour une conclusion.

## Ce qui se passe vraiment

Déboguer sans méthode, c'est chercher une aiguille dans une botte de foin en retournant le
foin au hasard. Déboguer avec méthode, c'est diviser la botte en deux, vérifier dans quelle
moitié se trouve l'aiguille, et répéter : une recherche qui converge de manière garantie,
quelle que soit la taille du foin.

```text
Recherche au hasard (sans méthode)
────────────────────────────────────
Complexité pire cas : O(n)  : proportionnelle à la taille du code suspect
Complexité moyenne  : imprévisible, dépend de la chance et de l'intuition

Bissection (avec méthode)
────────────────────────────────────
Complexité pire cas : O(log n) : pour 1000 commits suspects, ~10 étapes suffisent
Complexité garantie : converge toujours, indépendamment de la chance
```

### Étape 1 : Reproduire avant de chercher

Un bug non reproductible n'est pas un bug à corriger, c'est une hypothèse à confirmer.
Avant toute investigation dans le code, construis le scénario minimal qui déclenche le
problème de manière fiable : même si ce scénario est laid, même s'il ne ressemble pas à
l'usage réel. Un bug reproduit à volonté à 100% des essais divise le temps de correction par
cinq, parce que chaque hypothèse peut être testée immédiatement, sans attendre une
réapparition aléatoire en production.

### Étape 2 : Bissection systématique

La bissection ne s'applique pas qu'à l'historique Git. C'est un principe général : diviser
l'espace des causes possibles en deux, tester quel côté contient le problème, répéter.

```bash
# Bissection sur l'historique Git : trouver le commit qui a introduit une régression
git bisect start
git bisect bad                  # le commit actuel (HEAD) est buggé
git bisect good v1.4.0          # cette version-là était saine
# Git choisit automatiquement un commit au milieu, tu le testes, tu réponds :
git bisect good   # ou
git bisect bad
# Répète jusqu'à isoler le commit exact : log(n) étapes, jamais plus.
git bisect reset
```

La même logique s'applique sans Git : si le bug apparaît dans une fonction de 200 lignes,
commente ou isole la moitié, vérifie si le symptôme persiste, répète sur la moitié
suspecte. Chaque étape élimine la moitié de l'espace de recherche restant : c'est la seule
approche qui garantit une convergence rapide sur du code inconnu.

### Étape 3 : Hypothèses falsifiables, pas des intuitions

Une hypothèse de debug utile a une propriété précise : elle prédit un résultat observable
différent selon qu'elle est vraie ou fausse. "Ça doit être un problème de cache" n'est pas
une hypothèse falsifiable tant que tu n'as pas dit comment le vérifier. "Si c'est un
problème de cache client, alors désactiver le cache dans les DevTools doit faire disparaître
le symptôme immédiatement" est falsifiable : le test tranche.

```text
Hypothèse non falsifiable          Hypothèse falsifiable
──────────────────────────         ─────────────────────────────────────
"Ça doit venir de la base"    →    "Si ça vient de la base, la requête SQL
                                     isolée doit reproduire le doublon
                                     directement dans un client SQL, hors
                                     application"
```

### Étape 4 : Logs ciblés, pas logs partout

Un log ajouté au hasard dans une fonction suspecte pollue le signal. Un log utile répond à
une question précise posée avant de l'écrire : "à cet instant précis, quelle est la valeur
qui doit être X si mon hypothèse est vraie ?"

```typescript
// Mauvais : log générique, aucune hypothèse derrière
console.log("stock", stock);

// Utile : log qui teste une hypothèse précise (race condition sur deux requêtes concurrentes)
console.log(
  `[stock-check] req=${requestId} avant_lecture=${Date.now()} valeur_lue=${stock}`,
);
// → si deux req logguent la même valeur_lue avant qu'aucune n'ait écrit, c'est une race
//   condition confirmée, pas une supposition.
```

### Étape 5 : Profilers : mesurer avant d'optimiser, et savoir ce qu'ils cachent

Un profiler te dit où le temps est passé : mais seulement dans les conditions où tu l'as
lancé. Trois pièges de mesure reviennent sans cesse :

```text
Piège 1 : Environnement non représentatif
  Profiler en local avec une base de données vide ne révèle jamais les problèmes
  de volume qui n'apparaissent qu'à 100 000 lignes.

Piège 2 : Échantillonnage qui masque les événements rares
  Un profiler statistique (sampling) peut manquer un appel lent qui se produit une fois
  sur mille : exactement le genre de bug qu'on cherche à isoler.

Piège 3 : Optimiser la fonction la plus visible plutôt que la plus coûteuse
  Le profiler montre du temps passé dans une fonction utilitaire appelée partout ;
  ce n'est pas forcément elle le goulot : regarde le temps cumulé, pas le nombre d'appels.
```

## Compromis

| Option                                          | Coût                                               | Bénéfice                                           | Quand choisir                                                                    |
| ----------------------------------------------- | -------------------------------------------------- | -------------------------------------------------- | -------------------------------------------------------------------------------- |
| Tâtonnement intuitif                            | Rapide sur les bugs triviaux                       | Ne scale pas, aucune garantie de convergence       | Bug évident, une ligne, contexte que tu connais déjà bien                        |
| Bissection systématique                         | Discipline, quelques étapes incompressibles        | Convergence garantie même sur du code inconnu      | Bug non local, régression apparue entre deux versions                            |
| Logs ciblés par hypothèse                       | Temps de réflexion avant d'écrire le log           | Signal exploitable immédiatement, pas de bruit     | Bug intermittent, race condition, comportement dépendant du contexte d'exécution |
| Profiler en environnement de prod représentatif | Coût d'infrastructure, accès aux données réalistes | Seule mesure qui ne ment pas sur les vrais goulots | Avant toute optimisation de performance, jamais après                            |

## Pièges classiques

- **Corriger sans avoir reproduit.** Symptôme : le correctif "semble" marcher en local, le
  bug revient en production trois semaines plus tard identique.
- **Changer plusieurs choses à la fois pendant une bissection.** Symptôme : le bug disparaît
  mais tu ne sais pas lequel des trois changements l'a réellement corrigé : tu ne peux rien
  documenter ni réutiliser.
- **Logs laissés en production après le debug.** Symptôme : les logs de debug polluent les
  vrais logs applicatifs pendant des mois, jusqu'à ce que quelqu'un les retrouve par hasard.
- **Optimiser sur la base d'une intuition sans profiler.** Symptôme : le code "optimisé" est
  plus complexe, plus dur à maintenir, et le profiler montre après coup que le vrai goulot
  était ailleurs.

## Ce que tu dois savoir défendre

- Explique pourquoi la bissection garantit une convergence en O(log n) alors que le
  tâtonnement n'a aucune garantie.
- Donne un exemple d'hypothèse non falsifiable que tu as toi-même formulée un jour, et
  reformule-la en hypothèse falsifiable.
- Cite les trois pièges de mesure d'un profiler et pour chacun un exemple où il t'aurait
  fait tirer une conclusion fausse.

## Atelier : le bug qui n'arrive qu'une fois sur cinquante

### La scène

Un club d'escalade gère ses créneaux de mur avec un service Node qui accepte les
réservations. Chaque créneau a une capacité fixe, par exemple huit places. Deux grimpeurs
cliquent "réserver" à la même seconde sur le dernier créneau du mardi soir. La CI fait
tourner la suite de tests avant chaque déploiement, et un test précis, `reserve.concurrent.test.ts`,
échoue environ une fois sur cinquante exécutions, jamais plus, jamais moins souvent. L'équipe
a pris l'habitude de relancer la CI quand ça arrive. Un vendredi, neuf personnes se
retrouvent sur un créneau à huit places, un adhérent fait un scandale à l'accueil, et
personne ne peut expliquer pourquoi le compteur a menti.

### Le code fourni

```ts
// Node 20 LTS (verifie le 2026-08-03)
// reservation.ts : service de réservation de créneaux du mur d'escalade

type Creneau = {
  id: string;
  capacite: number;
  placesReservees: number;
};

const creneaux = new Map<string, Creneau>();

creneaux.set("mardi-19h", { id: "mardi-19h", capacite: 8, placesReservees: 7 });

// Simule un accès base de données avec une latence réseau variable, comme en production.
function latenceReseau(): Promise<void> {
  const ms = Math.random() * 5; // 0 à 5ms, variation réaliste d'un pool de connexions
  return new Promise((resolve) => setTimeout(resolve, ms));
}

export async function reserverPlace(creneauId: string): Promise<boolean> {
  const creneau = creneaux.get(creneauId);
  if (!creneau) return false;

  // Lecture du compteur : une requête SELECT en conditions réelles.
  await latenceReseau();
  const placesActuelles = creneau.placesReservees;

  if (placesActuelles >= creneau.capacite) {
    return false; // complet, refus
  }

  // Écriture du compteur : une requête UPDATE en conditions réelles.
  // Entre la lecture ci-dessus et l'écriture ci-dessous, une autre requête concurrente
  // a le temps de lire la même valeur non encore mise à jour.
  await latenceReseau();
  creneau.placesReservees = placesActuelles + 1;

  return true;
}

export function getCreneau(creneauId: string): Creneau | undefined {
  return creneaux.get(creneauId);
}
```

Le test qui échoue environ une fois sur cinquante :

```ts
// reserve.concurrent.test.ts
import { test, expect } from "vitest";
import { reserverPlace, getCreneau } from "./reservation";

test("le compteur ne dépasse jamais la capacité sous accès concurrent", async () => {
  // Une seule place restante, deux tentatives simultanées : une seule doit réussir.
  const creneau = getCreneau("mardi-19h")!;
  creneau.placesReservees = creneau.capacite - 1;

  const [resultat1, resultat2] = await Promise.all([
    reserverPlace("mardi-19h"),
    reserverPlace("mardi-19h"),
  ]);

  const succes = [resultat1, resultat2].filter(Boolean).length;
  expect(succes).toBe(1); // échoue environ 1 fois sur 50 : les deux réussissent parfois
  expect(getCreneau("mardi-19h")!.placesReservees).toBe(creneau.capacite);
});
```

### Étape 1 : rendre le bug déterministe AVANT toute correction

Règle absolue de cet atelier : **tu n'as pas le droit de toucher au correctif tant que tu ne
peux pas déclencher le bug à volonté.** Un bug qu'on corrige "à l'instinct" sans l'avoir rendu
reproductible n'est pas corrigé, il est masqué : la preuve, c'est que ce club l'a déjà vécu
trois semaines plus tôt sous une autre forme.

Techniques pour transformer un bug d'une fois sur cinquante en un bug d'une fois sur une :

- **Seed fixe sur l'aléatoire.** Remplace `Math.random()` par un générateur seedé
  (`mulberry32(42)` ou équivalent) le temps de l'enquête, pour que chaque exécution du test
  produise exactement la même séquence de latences.
- **Injection d'ordonnancement.** Force l'entrelacement le plus dangereux : les deux appels
  doivent lire avant qu'aucun n'écrive. Un simple `await` placé à la main entre la lecture et
  l'écriture, contrôlé par un paramètre de test, transforme la course en scénario garanti.
- **Sleep contrôlé, jamais un sleep au hasard.** Remplace la latence aléatoire par une
  latence paramétrable dans le test : `latenceReseau(ms: number)`. Une requête à 10ms et
  l'autre à 0ms reproduit l'entrelacement dangereux à chaque exécution.
- **Journalisation horodatée à la milliseconde**, posée avant de toucher au code de
  production, pour observer l'ordre réel des lectures et écritures :

```ts
console.log(`[${Date.now()}] req=${id} lecture placesReservees=${placesActuelles}`);
console.log(`[${Date.now()}] req=${id} ecriture placesReservees=${placesActuelles + 1}`);
```

- **Harnais de répétition** : fais tourner le test en boucle et compte les échecs réels avant
  de croire à la fréquence "une fois sur cinquante" annoncée de mémoire.

```bash
# Harnais de répétition : mesure la fréquence réelle d'échec, ne la suppose pas
for i in $(seq 1 200); do
  npx vitest run reserve.concurrent.test.ts --reporter=dot >> /tmp/run-$i.log 2>&1 \
    || echo "ECHEC run $i" >> /tmp/echecs.log
done
wc -l /tmp/echecs.log
```

Sans ce travail de reproduction, tu ne peux pas savoir si un futur correctif marche : tu ne
peux que constater, encore une fois, que "ça semble aller mieux".

### Étape 2 : le format HYPOTHESES.md imposé

Chaque hypothèse suit exactement six champs, dans cet ordre, jamais résumés ni fusionnés :

```text
Symptôme observable    : ...
Hypothèse               : ...
Prédiction falsifiable  : ...
Expérience               : ...
Résultat                 : ...
Conclusion               : ...
```

Exemple rempli sur le bug de cet atelier, avec au moins trois hypothèses dont une réfutée :

```text
Symptôme observable    : le test de concurrence échoue environ une fois sur cinquante,
                          avec un compteur qui dépasse la capacité de un.

Hypothèse 1             : le générateur Math.random() de Node partage un état global qui
                          se corrompt sous appels concurrents.
Prédiction falsifiable  : si c'est vrai, remplacer Math.random() par un compteur fixe ne
                          doit rien changer à la fréquence d'échec, puisque le problème
                          serait dans le générateur lui-même, pas dans son usage.
Expérience               : remplacement de latenceReseau() par un délai fixe de 2ms des
                          deux côtés, 200 exécutions du test.
Résultat                 : le test échoue quand même, à une fréquence proche de celle
                          observée avant, tant que les deux délais restent égaux.
Conclusion               : hypothèse réfutée. Le générateur aléatoire n'est pas en cause :
                          le problème survient même avec une latence fixe, donc il tient à
                          l'ordre relatif des opérations, pas à Math.random().

Hypothèse 2             : la Map JavaScript n'est pas thread-safe et corrompt la valeur
                          stockée sous accès concurrent.
Prédiction falsifiable  : si c'est vrai, lire directement creneau.placesReservees juste
                          après l'écriture, dans la même fonction, sans await entre les
                          deux, doit parfois renvoyer une valeur incohérente.
Expérience               : ajout d'un log juste après l'écriture, sur 200 exécutions,
                          comparaison de la valeur écrite et de la valeur relue
                          immédiatement.
Résultat                 : la valeur relue correspond toujours exactement à la valeur
                          écrite, dans les 200 cas.
Conclusion               : hypothèse réfutée. Node est mono-thread pour ce code
                          JavaScript : la Map ne se corrompt pas toute seule, le problème
                          est ailleurs.

Hypothèse 3             : deux appels concurrents peuvent tous les deux lire l'ancienne
                          valeur de placesReservees avant qu'aucun des deux n'ait écrit sa
                          mise à jour, parce que la lecture et l'écriture ne forment pas
                          une opération atomique.
Prédiction falsifiable  : si c'est vrai, forcer l'entrelacement lecture A, lecture B,
                          écriture A, écriture B (avec un délai contrôlé) doit produire un
                          dépassement de capacité à 100% des exécutions, pas seulement une
                          fois sur cinquante.
Expérience               : sleep contrôlé imposant cet ordre exact, 200 exécutions du
                          test avec cet entrelacement forcé.
Résultat                 : les 200 exécutions dépassent la capacité de un, à chaque fois.
Conclusion               : hypothèse confirmée. La combinaison lecture puis écriture, sans
                          verrou ni contrainte, permet à deux requêtes concurrentes de
                          fonder leur décision sur la même valeur périmée. C'est une race
                          condition classique de type lecture-modification-écriture (read
                          modify write) non atomique.
```

### Étape 3 : la correction et sa preuve

Une fois l'hypothèse 3 confirmée, le correctif retire toute décision prise côté application
sur une valeur lue séparément, et déplace la vérification de capacité dans une seule
opération atomique côté base de données, protégée par une contrainte :

```sql
-- Migration : la capacité est vérifiée et incrémentée en une seule opération atomique,
-- impossible à interrompre par une requête concurrente.
ALTER TABLE creneaux
  ADD CONSTRAINT places_dans_la_capacite
  CHECK (places_reservees <= capacite);

-- La réservation devient un UPDATE conditionnel unique, plus de lecture séparée.
UPDATE creneaux
SET places_reservees = places_reservees + 1
WHERE id = $1
  AND places_reservees < capacite;
-- Si la ligne modifiée est 0, la place n'a pas pu être prise : le créneau était complet
-- au moment exact de l'écriture, pas au moment d'une lecture périmée de quelques
-- millisecondes.
```

La preuve de correction n'est pas "le test passe une fois" : c'est le harnais de répétition
qui échouait avant, relancé 200 fois de suite sans aucun échec.

```bash
for i in $(seq 1 200); do
  npx vitest run reserve.concurrent.test.ts --reporter=dot \
    || { echo "ECHEC run $i sur le correctif"; exit 1; }
done
echo "200/200 : correction prouvée, pas seulement supposée"
```

### Arbre de décision d'enquête

```text
Un test échoue de facon intermittente
|
+-- Peux-tu le reproduire a volonte (>90% des essais) ?
|     |
|     +-- NON --> ne touche pas au code de production.
|     |           Applique seed fixe, sleep controle, injection d'ordonnancement,
|     |           harnais de repetition, jusqu'a obtenir une reproduction fiable.
|     |
|     \-- OUI --> continue.
|
+-- As-tu au moins 3 hypotheses falsifiables ecrites dans HYPOTHESES.md ?
|     |
|     +-- NON --> ecris-les avant d'ouvrir le code source suspect.
|     |
|     \-- OUI --> teste chaque hypothese par une experience separee.
|
+-- Une hypothese est-elle confirmee par une experience qui la rend impossible a nier ?
|     |
|     +-- NON --> formule de nouvelles hypotheses, ne corrige rien au hasard.
|     |
|     \-- OUI --> ecris le correctif cible sur la cause confirmee.
|
\-- Le test qui echouait passe-t-il 200 fois de suite apres correctif ?
      |
      +-- NON --> le correctif ne traite pas la vraie cause, retour a HYPOTHESES.md.
      |
      \-- OUI --> correction prouvee, documente-la, retire les logs de debug temporaires.
```

Analogie : traquer un bug intermittent, c'est la garde aux urgences qui refuse de traiter un
malaise sans en avoir observé une crise déclenchée en conditions contrôlées, et la cordée qui
refuse de retenter un passage tant qu'elle n'a pas identifié la prise exacte qui a lâché.
Où l'analogie casse : aux urgences et en cordée, il existe une limite éthique et physique à
provoquer une crise ou une chute pour l'observer. En informatique, provoquer le bug à volonté
dans un environnement de test est non seulement permis mais obligatoire : c'est ce qui rend
l'enquête possible sans mettre personne en danger.

## Cas E : la double réservation, deux histoires possibles

### Le symptôme

Deux clients réservent le même créneau du club d'escalade à quelques secondes d'écart.
Les deux réservations sont enregistrées. Le symptôme est identique dans les deux
scénarios suivants ; un seul est la vraie cause sur ce système précis.

### Hypothèse A : race condition en base

Deux écritures concurrentes lisent le compteur de capacité avant que l'une des deux
n'ait eu le temps de l'incrémenter et de committer, donc les deux passent le test de
capacité disponible.

### Hypothèse B : cache applicatif périmé

Le compteur de capacité est mis en cache côté application avec une durée de vie courte ;
la deuxième réservation lit une valeur de cache qui ne reflète pas encore la première
écriture, donc elle passe le test de capacité disponible sur une donnée déjà obsolète.

### Consigne

Conçois une seule expérience qui, quel que soit son résultat, élimine l'une des deux
hypothèses sans ambiguïté et sans avoir besoin d'une deuxième expérience pour trancher.
Écris cette expérience avant de lire le corrigé.

Ce cas n'est pas le même exercice que le format `HYPOTHESES.md` ci-dessus : là, tu explores
au moins trois pistes l'une après l'autre ; ici, deux explications mutuellement exclusives
produisent exactement le même symptôme, et c'est la conception de l'expérience décisive qui
est notée, pas le nombre de pistes listées.

### Corrigé, à ne lire qu'après avoir écrit ton expérience

```text
Expérience décisive : espacer les deux réservations concurrentes d'un délai strictement
supérieur à la durée de vie du cache (TTL), tout en gardant les deux écritures dans la
même fenêtre de transaction en base (sleep contrôlé injecté entre la lecture du compteur
et le commit de la première réservation).

Résultat 1 : le bug persiste malgré un délai > TTL
  --> le cache ne peut plus être périmé au moment de la seconde lecture
  --> hypothèse B réfutée, hypothèse A confirmée (race condition en base)

Résultat 2 : le bug disparaît dès que le délai dépasse le TTL
  --> les deux écritures restent concurrentes en base, donc la race condition
      aurait dû se reproduire si elle était la cause
  --> hypothèse A réfutée, hypothèse B confirmée (cache périmé)

Une seule expérience, deux résultats possibles, une hypothèse éliminée dans les deux cas :
c'est ce qui la rend décisive. Une expérience qui laisserait les deux hypothèses vivantes
après coup (par exemple "on relance l'appel et on regarde si ça recommence") ne tranche
rien et coûte le même temps.
```
