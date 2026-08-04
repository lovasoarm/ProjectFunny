# UI, cas d'usage, domaine, infra : l'inversion de dépendance concrète

## Le piège

Tu construis le système de réservation de créneaux d'escalade pour un club sportif. Un
adhérent peut réserver un créneau si : il a moins de 3 réservations actives, le mur n'est pas
déjà plein pour ce créneau, et sa cotisation est à jour. Tu écris cette règle directement dans
le gestionnaire de route HTTP `POST /reservations`, avec un appel SQL pour chaque condition.
Ça fonctionne. Six mois plus tard, le club veut : une commande vocale via un assistant pour
les réservations par téléphone, un job automatique qui libère les créneaux non confirmés après
15 minutes, et des tests automatisés sur la règle des 3 réservations actives (un bug a laissé
passer un adhérent avec 7 réservations simultanées). Tu réalises que la règle des 3
réservations n'existe nulle part sous une forme isolée : elle est mélangée à du code HTTP, du
SQL, et de la sérialisation JSON. Il faut la réécrire trois fois : une pour la route HTTP
existante, une pour la commande vocale, une pour le job automatique. Et la tester correctement
supposerait de monter un serveur HTTP complet avec une vraie base de données.

## Ce qui se passe vraiment

### Les quatre couches, et à quoi chacune sert réellement

```text
┌─────────────────────────────────────────────────────────┐
│  UI (présentation)                                        │
│  Route HTTP, composant React, commande CLI, message vocal │
│  Rôle : traduire une intention externe en appel de cas    │
│  d'usage, et traduire le résultat en réponse externe.      │
└───────────────────────┬─────────────────────────────────┘
                         │ appelle
                         ▼
┌─────────────────────────────────────────────────────────┐
│  Cas d'usage (application)                                │
│  "RéserverCréneau", "AnnulerRéservation"                   │
│  Rôle : orchestrer une opération métier de bout en bout,   │
│  sans connaître le protocole d'entrée (HTTP, CLI, voix).    │
└───────────────────────┬─────────────────────────────────┘
                         │ utilise
                         ▼
┌─────────────────────────────────────────────────────────┐
│  Domaine (métier)                                          │
│  Entités, règles, invariants : "un adhérent ne peut pas    │
│  avoir plus de 3 réservations actives"                     │
│  Rôle : porter la connaissance métier, sans savoir ce       │
│  qu'est une base de données, une requête HTTP, un JSON.     │
└───────────────────────┬─────────────────────────────────┘
                         │ définit des interfaces, utilisées par
                         ▼
┌─────────────────────────────────────────────────────────┐
│  Infra (technique)                                          │
│  Requêtes SQL, appel API externe, envoi d'email/SMS         │
│  Rôle : implémenter concrètement l'accès aux ressources     │
│  externes, selon des interfaces définies par le domaine.     │
└─────────────────────────────────────────────────────────┘
```

La colonne vertébrale de ce découpage n'est pas "où mettre le fichier". C'est une règle de
dépendance à sens unique : **une couche ne connaît que les couches situées en dessous d'elle
dans ce schéma, jamais celles au-dessus, et le Domaine ne connaît rien du tout d'externe.**

### L'inversion de dépendance, concrètement

Le point le plus contre-intuitif : la flèche entre Domaine et Infra semble aller dans le
mauvais sens dans le schéma ("Infra implémente une interface définie par le Domaine" plutôt
que "Domaine appelle Infra directement"). C'est exactement le principe d'inversion de
dépendance, et il résout le problème concret de l'exemple du club d'escalade.

Sans inversion (couplage direct, le piège classique) :

```typescript
// domaine/reservation.ts
import { db } from "../infra/database"; // le Domaine connaît l'infra concrète !

async function peutReserver(adherentId: string): Promise<boolean> {
  const reservations = await db.query(
    "SELECT COUNT(*) FROM reservations WHERE adherent_id = $1 AND statut = 'active'",
    [adherentId],
  );
  return reservations[0].count < 3;
}
```

Ce code mélange une règle métier ("pas plus de 3 réservations actives") avec un détail
technique (SQL, nom de colonnes, driver de base de données). Tester la règle exige une vraie
base de données. Changer de base de données oblige à retoucher la règle métier.

Avec inversion de dépendance :

```typescript
// domaine/reservation.ts : ne connaît AUCUN détail technique
interface ReservationRepository {
  compterReservationsActives(adherentId: string): Promise<number>;
}

function peutReserver(nbReservationsActives: number): boolean {
  return nbReservationsActives < 3; // règle pure, testable sans I/O
}

// application/reserverCreneau.ts : orchestre, connaît l'interface, pas l'implémentation
async function reserverCreneau(
  adherentId: string,
  repo: ReservationRepository,
): Promise<Resultat> {
  const nb = await repo.compterReservationsActives(adherentId);
  if (!peutReserver(nb)) {
    return { succes: false, raison: "Trop de réservations actives" };
  }
  // ... suite de l'orchestration
}

// infra/postgresReservationRepository.ts : implémente l'interface, connaît SQL
class PostgresReservationRepository implements ReservationRepository {
  async compterReservationsActives(adherentId: string): Promise<number> {
    const rows = await db.query(
      "SELECT COUNT(*) FROM reservations WHERE adherent_id = $1 AND statut = 'active'",
      [adherentId],
    );
    return rows[0].count;
  }
}
```

Résultat concret pour l'exemple du club d'escalade :

- La règle `peutReserver` se teste avec un simple nombre entier, aucune base de données à
  monter, un test qui tourne en microsecondes.
- Le cas d'usage `reserverCreneau` se réutilise tel quel pour la route HTTP, la commande
  vocale, et le job automatique : seule la couche UI change, pas la logique.
- Remplacer PostgreSQL par un autre système de stockage revient à écrire une nouvelle classe
  qui implémente `ReservationRepository`, sans toucher au domaine ni aux cas d'usage.

### Le flux complet, dans l'ordre réel d'exécution

```text
1. Requête HTTP POST /reservations arrive       (couche UI)
2. La route extrait adherentId, creneauId du body JSON
3. La route appelle le cas d'usage reserverCreneau(adherentId, creneauId, repo, ...)
                                                  (couche Application)
4. Le cas d'usage appelle les règles du domaine  (couche Domaine)
   - peutReserver(nb)
   - créneauEstDisponible(creneau)
   - cotisationEstAJour(adherent)
5. Le cas d'usage appelle le repository pour persister la réservation
                                                  (couche Infra, via interface)
6. Le cas d'usage retourne un résultat métier (succès/échec + raison)
7. La route traduit ce résultat en réponse HTTP (200, 409, 422...)
                                                  (retour couche UI)
```

Note ce qui ne remonte jamais vers le haut : le domaine ne sait jamais qu'il répond à une
requête HTTP. Il ne renvoie jamais de code de statut HTTP, jamais de JSON. Il renvoie des
concepts métier ("créneau indisponible"), et c'est la couche UI qui les traduit dans le
langage du protocole utilisé.

Analogie : les couches et l'inversion de dépendance, c'est la chaîne de commandement d'une
régie de spectacle où le metteur en scène ordonne sans exécuter, et la relation entre la
passerelle et la salle des machines en navigation maritime, où la passerelle décide du cap
et la machine obéit sans connaître la destination.
Où l'analogie casse : le régisseur et le mécanicien restent des humains capables d'interpréter
un ordre flou. Une couche infra mal inversée ne devine rien, elle exécute exactement ce que
l'interface lui permet, et une dépendance dans le mauvais sens oblige à réécrire le contrat.

## Compromis

| Option                                                              | Coût                                                                         | Bénéfice                                                                                   | Quand choisir                                                    |
| ------------------------------------------------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | ---------------------------------------------------------------- |
| Séparation stricte en 4 couches avec interfaces                     | Plus de fichiers, plus d'indirection, courbe d'apprentissage pour l'équipe   | Testabilité du métier sans I/O, réutilisation multi-canal, remplacement d'infra sans casse | Règles métier non triviales, produit destiné à vivre et évoluer  |
| Domaine et Infra fusionnés (accès direct à la base dans les règles) | Règles non testables sans I/O, changement de techno = réécriture du métier   | Rapidité d'écriture initiale, moins de fichiers à naviguer                                 | CRUD sans règle métier significative, prototype jetable          |
| Cas d'usage fusionné avec l'UI (logique dans le contrôleur)         | Logique dupliquée dès qu'un deuxième canal d'entrée apparaît (CLI, job, API) | Une seule couche à comprendre pour un flux simple                                          | Un seul canal d'entrée prévu à vie, projet de très petite taille |

## Pièges classiques

- **L'entité domaine qui porte des annotations d'infra.** Symptôme : une classe métier
  `Reservation` décorée avec `@Entity()` et `@Column()` d'un ORM : le domaine est en fait
  couplé à un framework de persistance précis, l'inversion n'existe que sur le papier.
- **Le "cas d'usage" qui ne fait qu'un appel direct au repository.** Symptôme : une couche
  application qui existe en apparence mais qui n'orchestre rien : signe qu'on a ajouté une
  couche par cargo-culte plutôt que parce qu'elle porte une vraie responsabilité.
- **La règle métier dupliquée entre frontend et backend.** Symptôme : la validation "3
  réservations actives max" écrite en JavaScript côté client pour l'UX ET en SQL côté serveur
  pour la sécurité, sans qu'aucune des deux ne soit la source de vérité documentée : elles
  divergent au premier changement de règle oublié d'un côté.
- **L'interface trop large côté Infra.** Symptôme : `ReservationRepository` expose une
  méthode `executerRequeteSQL(query: string)` : l'abstraction ne protège plus rien, n'importe
  quel détail SQL peut fuiter dans le domaine par ce trou.
- **Les couches respectées en théorie, violées par un raccourci "juste cette fois".**
  Symptôme : un import direct de la couche Infra depuis un composant UI pour "gagner du
  temps" sur une fonctionnalité urgente : ce raccourci devient permanent dans 90% des cas.

## Ce que tu dois savoir défendre

- Explique pourquoi le Domaine ne doit connaître ni HTTP, ni SQL, ni JSON, avec un exemple de
  bug que cette règle évite.
- Dessine (verbalement ou sur un tableau) le flux complet d'une opération métier à travers
  les 4 couches, en précisant ce qui ne remonte jamais.
- Explique l'inversion de dépendance à quelqu'un qui ne connaît pas le terme, sans utiliser
  le mot "interface" plus d'une fois.
