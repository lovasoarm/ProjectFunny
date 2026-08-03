# Les cinq états obligatoires de tout affichage

## Le piège

Le planning du jour du cabinet vétérinaire s'affiche très bien pendant la démo : huit rendez-vous,
bien répartis, jolis badges de couleur par praticien. En prod, le premier lundi de l'année, le
planning est vide (fermeture des fêtes) et affiche... rien. Une page blanche. Le stagiaire pense que
l'appli a planté. Trois mois plus tard, jour de rentrée scolaire, le planning déborde de 140 rendez-
vous compressés sur un même jour et le navigateur rame à chaque scroll.

Le composant `<Planning />` n'a été pensé que pour un seul état : "il y a des données, tout va bien".
C'est l'état le plus rare en réalité : la plupart du temps, ton composant est soit vide, soit en
train de charger, soit en erreur, soit surchargé.

## Ce qui se passe vraiment

Tout affichage de données asynchrones a exactement cinq états possibles, pas quatre, pas trois :

```text
┌─────────┐   requête lancée    ┌──────────┐  succès, 0 résultat  ┌───────┐
│  IDLE   │ ──────────────────► │ LOADING  │ ────────────────────► │ EMPTY │
└─────────┘                     └────┬─────┘                       └───────┘
                                      │
                    succès, N résultats (raisonnable)
                                      │
                                      ▼
                                 ┌─────────┐
                                 │  READY  │
                                 └─────────┘
                                      ▲
                    succès, N résultats (trop grand pour l'UI) │
                                      │
                                 ┌──────────┐
                                 │ OVERLOAD │
                                 └──────────┘

                                      │ échec réseau / serveur
                                      ▼
                                 ┌─────────┐
                                 │  ERROR  │
                                 └─────────┘
```

Il en manque un sixième, plus sournois : le **partiel**. La requête a "réussi" mais la donnée est
incomplète : 8 rendez-vous sur 12 sont revenus, les 4 autres ont échoué à charger le nom du
propriétaire (service annexe indisponible). Ce n'est ni une erreur totale, ni un succès complet.
L'afficher comme un succès plein cache une perte d'information ; l'afficher comme une erreur totale
cache huit rendez-vous parfaitement exploitables.

## Modéliser ça en code, pas en booléens

Le piège classique : des booléens indépendants.

```typescript
// À éviter : ces quatre booléens peuvent produire des états impossibles
// (isLoading=true ET hasError=true ET data=[...] en même temps, par exemple)
const [isLoading, setIsLoading] = useState(false);
const [hasError, setHasError] = useState(false);
const [data, setData] = useState<Appointment[] | null>(null);
```

Une union discriminée rend les états impossibles... impossibles à représenter :

```typescript
type PlanningState =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "empty" }
  | { status: "ready"; appointments: Appointment[] }
  | { status: "partial"; appointments: Appointment[]; failedCount: number }
  | { status: "overload"; total: number; sample: Appointment[] }
  | { status: "error"; reason: "network" | "server" | "unauthorized" };

function renderPlanning(state: PlanningState) {
  switch (state.status) {
    case "idle":
      return null; // rien à afficher avant la première demande

    case "loading":
      return <PlanningSkeleton />; // pas un spinner nu, voir leçon 5

    case "empty":
      return (
        <EmptyState
          title="Aucun rendez-vous aujourd'hui"
          hint="Rien de prévu, ou tout le monde est en repos ?"
          action={{ label: "Ajouter un rendez-vous", to: "/rendez-vous/nouveau" }}
        />
      );

    case "ready":
      return <AppointmentList items={state.appointments} />;

    case "partial":
      return (
        <>
          <Banner tone="warning">
            {state.failedCount} rendez-vous n'ont pas pu être chargés complètement.
            <RetryLink />
          </Banner>
          <AppointmentList items={state.appointments} />
        </>
      );

    case "overload":
      // au-delà d'un seuil, on ne rend pas tout : on force un filtre
      return (
        <OverloadNotice
          total={state.total}
          message="Trop de rendez-vous pour tout afficher. Filtre par praticien ou par créneau horaire."
        />
      );

    case "error":
      return <ErrorState reason={state.reason} onRetry={reload} />;
  }
}
```

Le compilateur TypeScript refuse une branche `switch` non exhaustive si tu ajoutes un état plus
tard sans mettre à jour l'affichage : c'est le vrai bénéfice, pas la joliesse du code.

## Le cas "vide" mérite un vrai traitement, pas un `if (data.length === 0) return null`

Un état vide silencieux est indiscernable d'un bug pour l'utilisateur. Un bon état vide répond à
trois questions : est-ce normal, pourquoi c'est vide, et qu'est-ce que je peux faire maintenant.

```text
Mauvais état vide            Bon état vide
──────────────────           ─────────────
(rien ne s'affiche)          "Aucun rendez-vous aujourd'hui"
                              "Le cabinet est fermé, ou personne n'a encore réservé."
                              [ Ajouter un rendez-vous ]
```

## Le cas "trop de données" est un problème de design, pas de performance pure

Tu peux virtualiser une liste de 10 000 lignes techniquement : React virtualization, pagination,
fenêtrage. Mais la vraie question précède la technique : est-ce que l'utilisateur a besoin de voir
10 000 lignes d'un coup ? Souvent non : au-delà d'un seuil, il a besoin d'un filtre ou d'un résumé,
pas d'un scroll plus rapide.

```typescript
// Règle de décision simple à coder au niveau du state, pas seulement du rendu
const OVERLOAD_THRESHOLD = 200;

function toPlanningState(appointments: Appointment[]): PlanningState {
  if (appointments.length === 0) return { status: "empty" };
  if (appointments.length > OVERLOAD_THRESHOLD) {
    return {
      status: "overload",
      total: appointments.length,
      sample: appointments.slice(0, 20),
    };
  }
  return { status: "ready", appointments };
}
```

## Compromis

| Option                                        | Coût                                                 | Bénéfice                                       | Quand choisir                                                                    |
| --------------------------------------------- | ---------------------------------------------------- | ---------------------------------------------- | -------------------------------------------------------------------------------- |
| Union discriminée (state machine légère)      | Un peu plus de code au départ, réflexion sur les cas | États impossibles interdits par le compilateur | Tout composant qui affiche des données asynchrones dans une appli qui doit durer |
| Booléens indépendants                         | Rapide à écrire                                      | Bugs d'état incohérent tôt ou tard             | Prototype à jeter sous 48h                                                       |
| Virtualisation de liste (react-virtual, etc.) | Complexité technique, scroll custom                  | Affiche réellement 10 000 lignes sans ramer    | Cas où l'utilisateur a un vrai besoin de tout voir défiler (rare)                |
| Seuil + filtre forcé                          | Il faut définir un seuil métier, parfois arbitraire  | Interface qui reste utilisable et rapide       | Cas où au-delà d'un seuil, personne ne lit ligne par ligne de toute façon        |

## Pièges classiques

- L'état "vide" et l'état "erreur silencieuse" rendent tous les deux une liste vide : impossible de
  les distinguer en observant seulement l'écran, il faut vérifier les logs.
- Le squelette de chargement ne ressemble pas à la forme réelle du contenu : l'utilisateur perçoit
  un "saut" de mise en page à l'arrivée des données (cumulative layout shift), qui donne une
  impression de lenteur même quand c'est rapide.
- L'état "partiel" est traité comme un succès total : des informations manquantes sont interprétées
  par erreur comme "il n'y a rien à cet endroit" (ex. propriétaire manquant lu comme "pas de
  propriétaire").
- Le seuil de surcharge est choisi au doigt mouillé sans mesurer le temps de rendu réel sur un
  poste bas de gamme, celui qu'utilise effectivement l'accueil du cabinet.

## Ce que tu dois savoir défendre

- Pourquoi des booléens indépendants pour représenter le chargement/l'erreur/le succès sont une
  source de bugs, même si chaque booléen pris seul semble correct ?
- Comment distingues-tu, dans ton code, un état "vide légitime" d'une erreur silencieuse ?
- Pourquoi l'état "partiel" a-t-il besoin d'un traitement différent du succès total et de l'erreur
  totale ? Donne un exemple concret où le confondre coûte cher.
