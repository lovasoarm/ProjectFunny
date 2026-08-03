# Formulaires qui ne mentent pas

## Le piège
Le formulaire de nouveau dossier animal valide côté client que le poids est un nombre positif.
Propre, message d'erreur immédiat, tout va bien. Sauf que l'API accepte aussi les demandes directes
(import en masse, script de migration, futur client mobile) et là, rien ne vérifie que le poids est
positif. Un jour, un import CSV mal formé crée quinze fiches avec un poids de "-4kg", et le calcul
de dosage de médicament construit dessus renvoie des valeurs absurdes que personne ne remarque avant
qu'un animal soit sous-dosé.

La validation côté client est un confort d'interface. Ce n'est jamais une garantie. Une garantie ne
peut vivre que là où personne ne peut la contourner : le serveur.

## Ce qui se passe vraiment

### Deux validations, deux rôles différents

```text
Client (UX)                          Serveur (vérité)
────────────                         ─────────────────
But : feedback immédiat,             But : garantir l'intégrité des
      guider la saisie                     données, quoi qu'il arrive
Peut être contourné                  Ne peut pas être contourné
                                      (DevTools, curl, script, autre client)
Erreurs de forme                     Erreurs de forme ET de fond
(format, champ requis)               (règles métier, unicité, cohérence)
```

Les deux doivent exister. Le client sans le serveur est une passoire. Le serveur sans le client est
une interface hostile (l'utilisateur découvre l'erreur après un aller-retour réseau complet).

### Elles doivent parler le même langage

Le bug classique : le client dit "le poids doit être entre 0 et 200 kg", le serveur dit "le poids
doit être strictement positif et inférieur à 150 kg". Résultat : un poids de 180 kg passe la
validation client, échoue côté serveur, et l'utilisateur reçoit une erreur qu'il ne comprend pas
puisque son formulaire "était valide".

```typescript
// schema partagé — un seul fichier de vérité, importé des deux côtés si le stack le permet
// (ex. Zod, exécutable côté client ET côté serveur en TypeScript full-stack)
import { z } from "zod";

export const AnimalSchema = z.object({
  name: z.string().min(1, "Le nom est obligatoire").max(80),
  species: z.enum(["chien", "chat", "nac", "autre"]),
  weightKg: z
    .number({ invalid_type_error: "Le poids doit être un nombre" })
    .positive("Le poids doit être supérieur à 0")
    .max(150, "Poids supérieur à 150 kg : vérifie la saisie"),
  ownerEmail: z.string().email("Adresse email invalide"),
});

export type AnimalInput = z.infer<typeof AnimalSchema>;
```

Côté client, ce schéma alimente les messages d'erreur en direct. Côté serveur, exactement le même
schéma rejette toute requête qui l'aurait contourné :

```typescript
// route serveur — la seule porte d'entrée qui compte réellement
app.post("/animals", async (req, res) => {
  const parsed = AnimalSchema.safeParse(req.body);
  if (!parsed.success) {
    // 422 : la requête est bien formée au sens HTTP, mais son contenu est invalide
    return res.status(422).json({ errors: parsed.error.flatten().fieldErrors });
  }
  const animal = await createAnimal(parsed.data);
  return res.status(201).json(animal);
});
```

### Des messages qui disent quoi faire, pas juste ce qui est faux

```text
Message qui ment ou n'aide pas       Message qui dit quoi faire
────────────────────────────         ──────────────────────────
"Erreur de validation"               "Le poids doit être supérieur à 0 kg"
"Champ invalide"                     "L'email du propriétaire n'a pas de @ :
                                       vérifie l'orthographe"
"Une erreur est survenue"            "Ce créneau vient d'être réservé par
                                       quelqu'un d'autre. Choisis-en un autre
                                       ci-dessous."
```

Un message d'erreur utile nomme le champ, dit la règle violée, et si possible dit l'action de
récupération. "Erreur 500" n'est jamais un message utilisateur, seulement un message de log.

### L'idempotence : le vrai sujet du double submit

Le double submit n'est pas un bug de "clic trop rapide". C'est un problème structurel : envoyer
deux fois une commande de création doit produire un seul résultat, pas deux. La solution n'est pas
de désactiver le bouton (ça aide, mais ne suffit pas — un retry réseau côté client, un double
appel depuis deux onglets, ou un proxy qui rejoue la requête contournent un simple `disabled`).

La vraie solution est une **clé d'idempotence** : un identifiant unique généré côté client pour
cette tentative de soumission, envoyé avec la requête, et que le serveur utilise pour reconnaître
"j'ai déjà traité celle-ci".

```typescript
// côté client : une clé générée une seule fois par formulaire ouvert, pas par clic
function useIdempotencyKey() {
  const keyRef = useRef<string>(crypto.randomUUID());
  return keyRef.current;
}

async function submitAppointment(payload: AppointmentInput, idempotencyKey: string) {
  const response = await fetch("/api/appointments", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "Idempotency-Key": idempotencyKey,
    },
    body: JSON.stringify(payload),
  });
  return response;
}
```

```sql
-- côté serveur : table dédiée pour retenir les clés déjà traitées
create table idempotency_keys (
  key            text primary key,
  request_hash   text not null,       -- empêche de réutiliser la clé pour un autre payload
  response_body  jsonb not null,      -- on rejoue la même réponse sans refaire le travail
  response_status int not null,
  created_at     timestamptz not null default now()
);
```

```typescript
// logique serveur : avant de créer, vérifier si cette clé a déjà une réponse
app.post("/api/appointments", async (req, res) => {
  const key = req.header("Idempotency-Key");
  if (!key) return res.status(400).json({ error: "Idempotency-Key manquante" });

  const existing = await db.query(
    "select response_body, response_status from idempotency_keys where key = $1",
    [key],
  );
  if (existing.rowCount > 0) {
    // requête déjà traitée : on renvoie exactement la même réponse, sans recréer le rendez-vous
    const row = existing.rows[0];
    return res.status(row.response_status).json(row.response_body);
  }

  const parsed = AppointmentSchema.safeParse(req.body);
  if (!parsed.success) {
    return res.status(422).json({ errors: parsed.error.flatten().fieldErrors });
  }

  // transaction : créer le rendez-vous et enregistrer la clé, atomiquement
  const appointment = await db.transaction(async (tx) => {
    const created = await createAppointment(tx, parsed.data);
    await tx.query(
      `insert into idempotency_keys (key, request_hash, response_body, response_status)
       values ($1, $2, $3, $4)`,
      [key, hashPayload(req.body), JSON.stringify(created), 201],
    );
    return created;
  });

  return res.status(201).json(appointment);
});
```

Avec ce mécanisme, peu importe combien de fois le formulaire est soumis avec la même clé (retry
réseau automatique, double-clic, onglet dupliqué) : un seul rendez-vous existera, et chaque
soumission recevra la même réponse.

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Schéma de validation partagé client/serveur | Discipline à maintenir un seul fichier de vérité | Élimine les contradictions de règles | Stack full-stack TypeScript, formulaire à enjeu réel |
| Validation dupliquée mais indépendante | Simple à démarrer | Dérive garantie dans le temps | Prototype très court terme uniquement |
| Bouton désactivé pendant l'envoi | Trivial à coder | Réduit le double-clic humain | Toujours, mais jamais suffisant seul |
| Clé d'idempotence + table dédiée | Migration DB, un peu de code serveur | Garantit un seul effet, quelle que soit la source de duplication | Toute opération de création/paiement/réservation qui coûte cher si dupliquée |

## Pièges classiques
- La règle de validation change côté client sans que quelqu'un pense à la reporter côté serveur (ou
  l'inverse) : les deux divergent en quelques sprints si elles ne sont pas dans le même fichier.
- Le message d'erreur générique "Une erreur est survenue" est affiché même quand le serveur a
  renvoyé un détail exploitable — le détail est jeté dans le `catch` sans être lu.
- La clé d'idempotence est générée à chaque clic sur "envoyer" au lieu d'une fois par ouverture de
  formulaire : elle ne protège alors plus contre rien.
- Le bouton est désactivé visuellement (`opacity: 0.5`) mais reste cliquable au clavier ou via un
  script, parce que l'attribut `disabled` n'a pas été posé sur l'élément réel.

## Ce que tu dois savoir défendre
- Pourquoi la validation côté client seule n'est jamais une garantie, même si elle est bien écrite ?
- Explique avec un exemple concret pourquoi désactiver le bouton ne suffit pas à empêcher un
  doublon de création.
- Comment fonctionne une clé d'idempotence, et pourquoi le serveur doit stocker la réponse complète,
  pas juste "cette clé a déjà été vue" ?
