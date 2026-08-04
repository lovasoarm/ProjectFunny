# Sécurité, coûts et données personnelles : ce que le capstone exige de savoir défendre

## La scène

Le club d'escalade fait fonctionner son système de trois salles depuis quatre mois. Un
membre du bureau, curieux, change l'identifiant dans l'URL de sa page de réservation :
`/reservations/482` devient `/reservations/483`, et il tombe sur le planning complet, le
numéro de téléphone et le certificat médical d'un autre adhérent. Il ne l'a dit à personne
d'hostile, il a juste voulu vérifier. La semaine suivante, la facture du fournisseur cloud
double sans explication visible. Deux problèmes différents, une seule cause commune : ces
sujets n'ont jamais eu de créneau dédié dans le projet, alors ils n'ont jamais eu de
réponse écrite, testée, ni datée.

## Ce qui se passe vraiment

La sécurité, les coûts et les données personnelles partagent un même défaut d'attention :
ils ne cassent rien en démo. Un formulaire mal protégé fonctionne très bien tant que
personne ne l'attaque. Une requête coûteuse tourne très bien tant que le volume de données
reste celui du jeu de test. Une donnée personnelle conservée sans limite ne pose aucun
souci tant que personne ne demande son effacement ou qu'un régulateur ne contrôle rien.
Ce sont des risques à retardement : le coût existe dès le premier jour, mais il ne se
matérialise que plus tard, au moment où il est le plus cher à corriger.

```text
Faille pas exploitée   -->  parait acceptable   -->  personne ne la corrige
Requête pas mesurée     -->  parait gratuite      -->  la facture explose au premier pic
Donnée pas purgée       -->  parait sans risque    -->  un contrôle ou une fuite la révèle
```

Ces trois familles ne sont pas des extras à ajouter en fin de projet. Ce sont des
propriétés du système, au même titre que la correction fonctionnelle. Un système qui
calcule juste mais qui expose les données d'un autre adhérent n'est pas un bon système
avec un défaut : c'est un système qui ne fait pas ce qu'on croit qu'il fait.

### Menace 1 : l'injection SQL

Une requête construite en collant directement une valeur venue de l'utilisateur dans le
texte SQL laisse cette valeur se comporter comme du code plutôt que comme une donnée.

```ts
// DANGEREUX : la valeur "search" est concaténée directement dans le SQL.
// Un utilisateur qui envoie search = "'; DROP TABLE adherents; --"
// fait exécuter sa propre instruction SQL par la base.
async function chercherAdherentDangereux(pool: Pool, search: string) {
  const sql = `SELECT id, nom FROM adherents WHERE nom = '${search}'`;
  return pool.query(sql);
}

// SUR : la valeur passe en parametre lie, jamais interpretee comme du SQL.
// Le pilote de base de donnees envoie le texte de la requete et les
// parametres separement : aucune valeur ne peut modifier la structure.
async function chercherAdherent(pool: Pool, search: string) {
  const sql = `SELECT id, nom FROM adherents WHERE nom = $1`;
  return pool.query(sql, [search]);
}
```

Le second exemple n'est pas "plus propre", il est structurellement différent : la base
sait, avant même de recevoir la valeur, quelle est la forme exacte de la requête. Aucune
valeur, aussi hostile soit-elle, ne peut faire dévier cette forme.

### Menace 2 : IDOR, l'autorisation par objet manquante

IDOR (Insecure Direct Object Reference) est le nom technique du problème du club
d'escalade : le système vérifie qu'un utilisateur est connecté, mais ne vérifie pas que
l'objet demandé lui appartient.

```ts
// DANGEREUX : verifie seulement que l'utilisateur est connecte.
// Il n'y a aucun controle sur le fait que la reservation 483
// appartient bien a l'utilisateur qui la demande.
app.get("/reservations/:id", requireAuth, async (req, res) => {
  const reservation = await db.reservations.findById(req.params.id);
  res.json(reservation);
});

// SUR : le controle d'acces se fait cote serveur, sur l'objet precis,
// jamais sur la simple presence d'un id dans l'URL.
app.get("/reservations/:id", requireAuth, async (req, res) => {
  const reservation = await db.reservations.findById(req.params.id);
  if (!reservation) return res.status(404).json({ erreur: "introuvable" });
  const appartientAUtilisateur = reservation.adherentId === req.user.id;
  const estAdminDeCetteSalle =
    req.user.role === "admin" && req.user.salleId === reservation.salleId;
  if (!appartientAUtilisateur && !estAdminDeCetteSalle) {
    return res.status(403).json({ erreur: "acces refuse" });
  }
  res.json(reservation);
});
```

Le contrôle côté client (cacher un bouton, désactiver un lien) ne compte pas comme
contrôle d'accès : n'importe qui peut appeler l'API directement, sans jamais passer par
l'interface. Le seul contrôle qui vaut quelque chose est celui exécuté côté serveur, sur
chaque lecture et chaque écriture d'un objet identifié.

### Menace 3 : les secrets en clair

Un secret (mot de passe de base de données, clé d'API de paiement, jeton de service
externe) qui vit dans le code source ou dans un fichier versionné est un secret qui a déjà
fuité, même si personne ne l'a encore remarqué : il circule dans l'historique Git, dans les
sauvegardes, dans les copies locales de chaque développeur qui a cloné le dépôt un jour.

```ts
// DANGEREUX : la cle secrete du fournisseur de paiement est ecrite en dur.
// Elle finit dans l'historique Git des le premier commit, meme si elle
// est retiree plus tard : Git garde toutes les versions precedentes.
const cleStripe = "sk_live_TA_CLE_ICI";

// SUR : la valeur vient d'une variable d'environnement, jamais du code.
// Le fichier qui definit ces variables en local (.env) est exclu du
// depot par .gitignore, et la valeur de production vit dans le
// gestionnaire de secrets de l'hebergeur, jamais dans un fichier texte.
const cleStripe = process.env.STRIPE_SECRET_KEY;
if (!cleStripe) throw new Error("STRIPE_SECRET_KEY manquante");
```

Un secret qui a fuité une seule fois doit être révoqué et régénéré, pas simplement retiré
du code : retirer la ligne ne supprime pas la valeur de l'historique.

### Menace 4 : les données personnelles conservées trop longtemps

Un adhérent qui résilie son abonnement au club d'escalade en 2022 ne devrait pas avoir son
certificat médical, son numéro de téléphone et l'historique complet de ses passages encore
présents en base en 2026, "au cas où". Chaque donnée personnelle conservée au-delà de sa
finalité initiale est un risque qui ne rapporte rien : elle n'aide aucune fonctionnalité
active, mais elle grossit la surface exposée en cas de fuite, et elle expose l'organisation
à une non-conformité si un contrôle a lieu.

## Le contrôle d'accès au niveau de la base : RLS Postgres

Le contrôle d'accès côté serveur, dans le code applicatif, reste vulnérable si une seconde
route, un script de migration ou un accès direct à la base oublie de le réappliquer. La
Row Level Security (RLS) de PostgreSQL 16 déplace une partie de ce contrôle dans la base
elle-même : une politique qui s'applique quel que soit le chemin par lequel la requête
arrive.

```sql
-- Active le controle ligne par ligne sur la table des reservations.
-- Sans cette ligne, RLS n'est pas applique meme si des politiques existent.
ALTER TABLE reservations ENABLE ROW LEVEL SECURITY;

-- Politique de lecture : un adherent ne voit que ses propres reservations.
-- current_setting('app.adherent_id') est fixe par l'application au debut
-- de chaque session, a partir du jeton d'authentification verifie.
CREATE POLICY reservations_lecture_adherent
  ON reservations
  FOR SELECT
  USING (adherent_id = current_setting('app.adherent_id')::uuid);

-- Politique separee pour les admins d'une salle : ils voient les
-- reservations de leur salle uniquement, jamais celles des deux autres.
CREATE POLICY reservations_lecture_admin_salle
  ON reservations
  FOR SELECT
  USING (
    current_setting('app.role') = 'admin'
    AND salle_id = current_setting('app.salle_id')::uuid
  );

-- Meme logique en ecriture : un adherent ne peut modifier
-- que sa propre reservation, jamais celle d'un autre.
CREATE POLICY reservations_ecriture_adherent
  ON reservations
  FOR UPDATE
  USING (adherent_id = current_setting('app.adherent_id')::uuid)
  WITH CHECK (adherent_id = current_setting('app.adherent_id')::uuid);
```

RLS n'annule pas le besoin de contrôler l'accès dans le code applicatif : c'est une
seconde barrière, pas un remplacement. Le code applicatif reste responsable de fixer
correctement `app.adherent_id` à partir d'un jeton vérifié, pas d'une valeur envoyée
librement par le client.

## Les coûts : ordres de grandeur à savoir citer

Un ordre de grandeur mal connu produit deux erreurs symétriques : sous-estimer une facture
qui va exploser, ou refuser une fonctionnalité par peur d'un coût qui, en réalité, est
négligeable. Les chiffres suivants sont des ordres de grandeur pour un projet de la taille
du capstone (quelques milliers d'utilisateurs actifs), pas des tarifs contractuels : ils
varient selon le fournisseur et évoluent avec le temps, à revérifier avant toute décision
budgétaire réelle.

```text
Base de donnees managee (petite instance)   : de l'ordre de 10 a 30 euros / mois
Fonction serveur (par million d'executions) : de l'ordre de 0.20 a 2 euros
Stockage de fichiers (par Go / mois)        : de l'ordre de 0.02 a 0.03 euros
Egress reseau (par Go sortant)              : de l'ordre de 0.05 a 0.15 euros, souvent
                                               le poste le plus sous-estime d'un projet
Envoi d'email transactionnel (par 1000)     : de l'ordre de 0.30 a 1 euro
```

Le poste le plus souvent oublié en revue de risques est l'egress : les données qui sortent
du fournisseur cloud vers l'extérieur (export, API consommée par un tiers, image servie
sans cache) coûtent plus cher, volume pour volume, que le stockage. Un en-tête
`Cache-Control: public, max-age=604800` sur une photo qui ne change pas divise ce poste
sans changer de fournisseur.

## Le RGPD, en pratique et sans jargon juridique

Le RGPD n'est pas un obstacle administratif ajouté au projet : c'est une manière de coder
qui limite la surface de risque. Quatre réflexes suffisent pour un projet de la taille du
capstone.

**Minimisation.** Ne collecte que la donnée dont une fonctionnalité active a besoin
aujourd'hui. Le numéro de téléphone d'un adhérent se justifie pour l'envoi d'un rappel de
créneau. Sa date de naissance complète ne se justifie pas si seul un seuil d'âge (mineur ou
majeur) est utilisé par le système : un booléen ou une tranche suffit.

**Durée de conservation.** Chaque table qui contient une donnée personnelle porte une
règle de conservation explicite, pas une conservation par défaut infinie.

```sql
-- Purge les donnees des adherents resilies depuis plus de trois ans,
-- duree choisie et documentee dans le registre, pas une valeur au hasard.
DELETE FROM adherents
WHERE statut = 'resilie'
  AND date_resiliation < now() - interval '3 years';
```

**Droit à l'effacement.** Un adhérent qui demande la suppression de ses données doit
pouvoir l'obtenir sans qu'un développeur ne doive écrire une requête manuelle dans
l'urgence. Une fonction dédiée, testée, est un prérequis, pas un luxe.

```ts
// Supprime ou anonymise les donnees personnelles d'un adherent sur
// demande explicite, tout en conservant les lignes necessaires a la
// comptabilite (obligation legale distincte du RGPD, qui prime ici).
async function effacerAdherent(db: Db, adherentId: string) {
  await db.adherents.update(adherentId, {
    nom: "anonymise",
    telephone: null,
    email: `anonymise-${adherentId}@supprime.local`,
    certificatMedicalUrl: null,
  });
  await db.reservations.deleteMany({
    adherentId,
    dateDebut: { gt: new Date() },
  });
}
```

**Registre.** Un tableau simple qui liste chaque catégorie de donnée personnelle traitée,
sa finalité, sa durée de conservation et qui y a accès. Ce registre n'a pas besoin d'être
long pour un projet de la taille du capstone : il doit être exact et à jour.

```text
Donnee                | Finalite                | Duree de conservation | Acces
-----------------------|--------------------------|------------------------|-------------
Certificat medical     | Obligation federation    | Duree de l'adhesion    | Adherent, admin salle
Numero de telephone    | Rappel de creneau        | Duree de l'adhesion    | Adherent, admin salle
Historique de passages | Statistiques d'usage     | 3 ans apres resiliation| Admin salle
```

## Compromis

| Option A                                            | Option B                                                 | Coût, bénéfice, quand choisir                                                                                                                                                                                                  |
| --------------------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Contrôle d'accès uniquement dans le code applicatif | Contrôle d'accès dans le code et RLS en base             | RLS coûte du temps de mise en place et complique le débogage local, mais protège même si une route oublie le contrôle. Choisis RLS dès que plusieurs équipes touchent la même base.                                            |
| Purger les données automatiquement selon une règle  | Purger manuellement au fil des demandes                  | La purge automatique demande d'écrire et tester une tâche planifiée, mais élimine l'oubli humain. Choisis l'automatique dès que le volume d'adhérents dépasse ce qu'une personne peut suivre à la main.                        |
| Stocker les secrets dans un gestionnaire dédié      | Stocker les secrets en variables d'environnement simples | Un gestionnaire dédié ajoute un service à opérer, mais permet la rotation et l'audit des accès. Les variables d'environnement suffisent pour un projet solo ou un capstone, pas pour une équipe avec plusieurs environnements. |

## Pièges classiques

- Un formulaire de recherche qui construit sa requête SQL par concaténation de chaînes :
  symptôme observable, une valeur contenant une apostrophe fait planter la requête au lieu
  d'être traitée comme du texte normal.
- Une route API qui vérifie l'authentification mais jamais la propriété de l'objet demandé :
  symptôme observable, changer un identifiant dans l'URL donne accès aux données d'un
  autre utilisateur.
- Un `.env` accidentellement versionné une seule fois : symptôme observable, un secret
  apparaît dans l'historique Git même après avoir été supprimé du fichier actuel.
- Une facture d'hébergement qui grimpe sans qu'aucune fonctionnalité nouvelle n'ait été
  ajoutée : symptôme observable, une requête ou un export tourne en boucle ou sans cache.
- Un registre de données personnelles qui n'existe que dans la tête d'une seule personne :
  symptôme observable, personne d'autre ne sait répondre à "quelles données avons-nous sur
  cet utilisateur" en moins d'une heure.

## Le gabarit REVUE-DE-RISQUES.md exigé par le capstone

Ce gabarit est celui déjà défini dans
[challenge.md](challenge.md) et consommé par
[../12-CAPSTONE-ARENA/03-deliverables.md](../12-CAPSTONE-ARENA/03-deliverables.md). Il est
reproduit ici sous sa forme complète, avec les colonnes de coût de mesure et de décision
signée que ce niveau ajoute pour aller au-delà du strict minimum.

```text
# Revue de risques : [nom du projet]

Date de la revue : [date]
Auteur : [ton nom]
Signature : [ton nom]

## Securite

| Risque | Probabilite | Impact | Mesure | Cout de la mesure | Decision signee et datee |
| ------ | ----------- | ------ | ------ | ------------------ | ------------------------- |
| ...    | ...         | ...    | ...    | ...                 | ...                        |

## Couts

| Risque | Probabilite | Impact | Mesure | Cout de la mesure | Decision signee et datee |
| ------ | ----------- | ------ | ------ | ------------------ | ------------------------- |
| ...    | ...         | ...    | ...    | ...                 | ...                        |

## Donnees personnelles

| Risque | Probabilite | Impact | Mesure | Cout de la mesure | Decision signee et datee |
| ------ | ----------- | ------ | ------ | ------------------ | ------------------------- |
| ...    | ...         | ...    | ...    | ...                 | ...                        |

## Risque le plus critique et plan d'action

[Le risque qui, s'il se realise, coute le plus cher a corriger a posteriori. Une phrase
de plan d'action concret, pas une intention vague.]
```

La colonne "Cout de la mesure" force une décision explicite : une mesure qui coûte plus
cher que le risque qu'elle couvre n'est pas automatiquement à écarter, mais elle doit être
justifiée, pas appliquée par réflexe. La colonne "Décision signée et datée" empêche une
mitigation de rester à l'état d'intention : quelqu'un de nommé a tranché, à une date
précise, ce qui est fait ou explicitement reporté.

## Ce que tu dois savoir défendre

- Pourquoi un contrôle d'accès fait uniquement côté client (bouton caché, lien désactivé)
  ne compte pour rien face à un attaquant qui appelle l'API directement.
- Pourquoi retirer un secret du code ne suffit pas à le protéger une fois qu'il a été
  commité, et ce qu'il faut faire à la place.
- Pourquoi l'egress réseau est souvent le poste de coût le plus sous-estimé d'un projet, et
  comment le réduire sans changer de fournisseur.
