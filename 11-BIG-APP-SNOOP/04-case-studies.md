# Trois études de cas

Chaque étude applique la méthode des deux leçons précédentes sur un domaine différent. Lis-les
en te forçant à deviner la contrainte avant de lire la réponse.

## Étude 1 : Plateforme de réservation (restaurants)

### Ce que tu trouves

Un système de réservation de tables pour un réseau de restaurants. En explorant le code de
réservation, tu trouves ceci :

```typescript
// booking/confirmSlot.ts
async function confirmSlot(slotId: string, partySize: number) {
  const slot = await db.slots.findById(slotId);

  // Pourquoi verrouiller la ligne avant de lire la capacité restante ?
  await db.query("SELECT * FROM slots WHERE id = $1 FOR UPDATE", [slotId]);

  if (slot.remainingCapacity < partySize) {
    throw new SlotFullError();
  }

  await db.slots.decrementCapacity(slotId, partySize);
  await db.bookings.create({ slotId, partySize, status: "confirmed" });
}
```

Le `SELECT ... FOR UPDATE` est redondant en apparence avec la vérification de capacité qui
suit. Un développeur pressé serait tenté de le retirer pour "simplifier".

### Enquête

Phase 1 (cartographie) : le dossier `booking/` est petit, isolé, avec un seul point d'écriture
sur `slots`. Phase 2 (historique) : `git log` sur ce fichier montre un commit vieux de deux
ans avec le message "fix : double réservation le samedi soir, cause = race condition sur
lecture-puis-écriture sans verrou".

### Contrainte reconstruite

Contrainte de concurrence réelle : plusieurs clients peuvent confirmer le même créneau au
même instant (un vendredi soir à forte affluence). Sans verrou pessimiste (`FOR UPDATE`),
deux requêtes peuvent lire "2 places restantes" simultanément et confirmer chacune une
réservation de 2 personnes, dépassant la capacité réelle du service.

### Ce que tu retiens

Le verrou n'est pas de la paranoïa, c'est la mémoire d'un incident client réel. Sans lire le
commit, tu l'aurais probablement supprimé au nom de la simplicité, et réintroduit le bug six
mois plus tard.

```text
Leçon transférable : toute vérification qui semble redondante autour d'une écriture
concurrente mérite une recherche d'historique avant suppression. La redondance apparente
est souvent la trace d'une race condition déjà vécue et corrigée.
```

## Étude 2 : Back-office logistique (tournées de livraison)

### Ce que tu trouves

Un back-office qui planifie des tournées de camions de livraison. Tu remarques que le calcul
d'itinéraire n'appelle jamais directement le service de cartographie externe, mais passe par
une file d'attente (`route_calculation_queue`) avec un traitement asynchrone qui peut prendre
jusqu'à deux minutes avant que le résultat apparaisse à l'utilisateur.

```text
Planificateur (UI) → enqueue("calculate_route", tourId)
                          │
                          ▼
                  Worker asynchrone (traite un job à la fois par entrepôt)
                          │
                          ▼
                  Appel service cartographie externe
                          │
                          ▼
                  Résultat écrit en base → notification UI (polling ou websocket)
```

Un calcul d'itinéraire simple ne devrait, en théorie, prendre que quelques centaines de
millisecondes en appel direct. Pourquoi cette complexité asynchrone ?

### Enquête

Phase 3 (zones à risque) : le fichier `routeCalculationWorker.ts` est un des plus modifiés du
dépôt. Phase 4 (historique ciblé) : plusieurs commits mentionnent des dépassements de quota
sur le service de cartographie externe en heure de pointe, et un incident où 40 tournées ont
été bloquées simultanément parce que l'appel synchrone attendait une réponse qui n'arrivait
jamais, saturant les connexions serveur disponibles.

### Contrainte reconstruite

Contrainte de fournisseur externe : le service de cartographie tiers impose un quota de
requêtes par seconde et par entrepôt, et répond de façon instable en heure de pointe. La
file d'asynchrone n'est pas là pour la performance perçue, elle est là pour lisser la charge
envoyée au fournisseur et éviter qu'un pic ne fasse tomber tout le calcul de tournées d'un
coup.

### Ce que tu retiens

```text
Leçon transférable : une architecture asynchrone "trop complexe" pour un calcul en
apparence simple cache presque toujours une dépendance externe non fiable ou limitée en
débit. Vérifie les quotas et la fiabilité du service tiers avant de proposer un appel
synchrone "plus simple".
```

## Étude 3 : Suivi de capteurs (surveillance de température en chambre froide)

### Ce que tu trouves

Un système qui reçoit des relevés de température de capteurs installés dans des chambres
froides d'un réseau de commerces alimentaires. Tu trouves une table `sensor_readings_raw`
alimentée en continu, et une deuxième table `sensor_readings_hourly` recalculée toutes les
heures par une tâche planifiée, avec une moyenne, un minimum et un maximum. Les alertes de
dépassement de seuil, elles, ne lisent jamais `sensor_readings_hourly` mais uniquement
`sensor_readings_raw`, avec une requête qui semble coûteuse exécutée toutes les trente
secondes.

```sql
-- Pourquoi ne pas juste attendre l'agrégat horaire pour déclencher une alerte ?
SELECT sensor_id, value, recorded_at
FROM sensor_readings_raw
WHERE recorded_at > NOW() - INTERVAL '2 minutes'
  AND value > (SELECT threshold FROM sensor_thresholds WHERE sensor_id = sensor_readings_raw.sensor_id);
```

### Enquête

Phase 2 (suivre le cas d'usage) : tu traces le flux d'alerte de bout en bout et découvres
qu'il déclenche un SMS au responsable de magasin. Phase 4 (historique) : un commentaire dans
le code d'alerte cite une exigence réglementaire de sécurité alimentaire imposant une
détection de rupture de chaîne du froid en moins de cinq minutes, avec traçabilité légale de
chaque dépassement.

### Contrainte reconstruite

Contrainte légale/sanitaire : la réglementation sur la sécurité alimentaire impose un délai
de détection court, incompatible avec un agrégat horaire. La table brute et la requête
fréquente existent pour respecter un délai légal de réaction, pas par choix de performance.
La table horaire, elle, sert à un usage différent (reporting, tendances), jamais aux alertes.

### Ce que tu retiens

```text
Leçon transférable : deux tables qui semblent redondantes (brute et agrégée) répondent
souvent à deux exigences différentes avec des contraintes de délai incompatibles. Ne
fusionne jamais deux pipelines de données sans vérifier séparément l'exigence de délai de
chacun de leurs consommateurs.
```

## Ce que tu dois savoir défendre

- Pour chacune des trois études, l'indice concret (dans le code, l'historique ou le schéma)
  qui t'aurait permis de deviner la contrainte avant de lire la réponse.
- Pourquoi les trois contraintes trouvées (concurrence, fournisseur externe, réglementation)
  appartiennent à des familles différentes de la leçon précédente, et pourquoi cette
  diversité est la preuve que la méthode généralise au-delà d'un seul type de domaine.
- Un contre-exemple plausible : un cas où une redondance de tables ou un mécanisme
  asynchrone serait, cette fois, une vraie dette technique sans contrainte active derrière.
