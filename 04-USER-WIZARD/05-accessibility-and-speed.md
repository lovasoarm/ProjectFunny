# Accessibilité utile, perception de vitesse, et les dangers de l'optimisme

## Le piège
L'application du cabinet vétérinaire passe tous les tests manuels de l'équipe : rapide sur le
Wi-Fi du bureau, jolie sur l'écran 27 pouces du designer. En vrai usage : l'assistante vétérinaire
qui a une tendinite au poignet ne peut pas utiliser la souris toute la journée et navigue au
clavier — le focus disparaît visuellement dès qu'elle tabule dans le formulaire, elle ne sait jamais
où elle est. Le client au téléphone en salle d'attente, en 4G faible, voit une interface qui "clique
sans rien faire" pendant deux secondes avant de réagir. Le rendez-vous "confirmé instantanément" en
UI optimiste s'avère en fait refusé cinq secondes plus tard — trop tard, le client a déjà raccroché
en pensant avoir un rendez-vous.

Trois sujets différents, une même cause : on a conçu pour son propre poste, sa propre connexion, sa
propre paire de mains — pas pour les conditions réelles d'usage.

## Ce qui se passe vraiment

### Accessibilité utile, pas cosmétique

L'accessibilité n'est pas une case à cocher RGAA à la fin du projet. C'est un sous-ensemble des cas
d'usage réels que ton produit doit couvrir, au même titre que "ça marche sur mobile". Trois règles
qui couvrent l'essentiel du quotidien d'un développeur produit (pas l'exhaustivité WCAG, la partie
qui a le plus d'impact rapporté au coût) :

**1. Tout ce qui est cliquable est atteignable et actionnable au clavier.**

```html
<!-- Mauvais : une div avec un onClick n'est ni focusable ni activable au clavier -->
<div onClick={confirmAppointment}>Confirmer</div>

<!-- Bon : un vrai bouton hérite gratuitement du focus, de Entrée/Espace, du rôle sémantique -->
<button type="button" onClick={confirmAppointment}>Confirmer</button>
```

**2. Le focus est visible et suit le sens de l'action, pas juste l'ordre du DOM.**

Quand une modale de confirmation de rendez-vous s'ouvre, le focus doit être envoyé dedans (sur le
premier champ ou le bouton principal), et rendu à l'élément d'origine à la fermeture. Sinon, la
personne au clavier continue de tabuler dans une page invisible derrière la modale.

```typescript
function useFocusTrap(modalRef: React.RefObject<HTMLElement>, isOpen: boolean) {
  useEffect(() => {
    if (!isOpen || !modalRef.current) return;
    const previouslyFocused = document.activeElement as HTMLElement | null;
    modalRef.current.querySelector<HTMLElement>("[data-autofocus]")?.focus();
    return () => previouslyFocused?.focus(); // rendre le focus à la fermeture
  }, [isOpen]);
}
```

**3. Les messages d'état sont annoncés, pas seulement affichés visuellement.**

Une erreur de validation qui apparaît en rouge sous un champ n'existe pas pour quelqu'un qui utilise
un lecteur d'écran, sauf si elle est reliée sémantiquement au champ et annoncée.

```html
<label for="weight">Poids (kg)</label>
<input id="weight" aria-describedby="weight-error" aria-invalid="true" />
<p id="weight-error" role="alert">Le poids doit être supérieur à 0</p>
```

Le coût de ces trois règles est faible si elles sont prises dès le départ (utiliser les bons
éléments HTML, câbler `aria-describedby`), et devient élevé en correctif après coup (il faut
réaudite tout l'existant).

### Perception de latence : la vitesse ressentie n'est pas la vitesse mesurée

Deux écrans peuvent avoir exactement le même temps de réponse serveur et donner une impression de
vitesse complètement différente selon ce qu'ils affichent pendant l'attente.

```text
Écran A : rien pendant 1200ms, puis contenu d'un coup
  → perçu comme lent, l'utilisateur se demande si ça a fonctionné

Écran B : squelette de la forme du contenu affiché en moins de 100ms,
          puis contenu réel à 1200ms
  → perçu comme rapide, l'attente est occupée et prévisible
```

Techniques qui changent la perception sans changer le temps réel serveur :
- Squelette (skeleton) qui a la même forme que le contenu final, affiché immédiatement.
- Feedback de clic instantané (changement d'état du bouton en moins de 100ms), avant même que la
  requête réseau ne soit terminée.
- Chargement progressif : afficher les 20 premiers rendez-vous de la liste dès qu'ils arrivent,
  sans attendre les 200 suivants.
- Prévenir plutôt que faire attendre en silence : au-delà de 3 secondes annoncées, dire "encore
  quelques secondes" plutôt que laisser un spinner muet indéfiniment.

### UI optimiste : gagner de la vitesse perçue en empruntant de la confiance

L'UI optimiste applique le changement à l'écran avant que le serveur ait confirmé, en pariant que
ça va marcher. Annuler un rendez-vous, par exemple : la ligne disparaît de la liste instantanément,
et la requête part en arrière-plan.

```typescript
async function cancelAppointment(id: string, appointments: Appointment[]) {
  // 1. On retire optimistiquement de l'UI, en gardant une copie pour annuler
  const previous = appointments;
  setAppointments(appointments.filter((a) => a.id !== id));

  try {
    await api.cancelAppointment(id);
    // succès : rien à faire, l'UI était déjà correcte
  } catch (error) {
    // échec : on revient en arrière ET on explique pourquoi, jamais un revert silencieux
    setAppointments(previous);
    showToast({
      tone: "error",
      message: "L'annulation a échoué. Le rendez-vous est toujours confirmé.",
    });
  }
}
```

### Le danger réel de l'optimisme : la confiance qui ment

L'UI optimiste n'est pas gratuite. Elle transfère un risque du serveur vers la crédibilité de
l'interface. Trois dangers concrets :

```text
1. Le revert silencieux
   L'action échoue, l'UI revient en arrière sans explication.
   → l'utilisateur pense avoir annulé, agit en conséquence (dit au client "c'est annulé"),
     découvre plus tard que non.

2. L'incohérence entre deux vues du même état
   La liste des rendez-vous du jour est optimiste et à jour immédiatement,
   mais le compteur "rendez-vous restants" dans l'en-tête, lui, vient du cache serveur
   et ne se met à jour qu'au prochain rafraîchissement.
   → deux sources de vérité affichées en même temps, contradictoires.

3. L'optimisme sur une action non réversible ou concurrente
   Confirmer un rendez-vous de manière optimiste alors qu'un autre utilisateur
   vient peut-être de prendre le même créneau.
   → l'UI ment sur un fait qui a un impact réel (le client croit avoir un rendez-vous).
```

Règle pratique : l'UI optimiste convient aux actions réversibles, à faible risque de conflit,
où l'échec est rare et sans conséquence grave (archiver, aimer, marquer comme lu). Elle est
dangereuse pour les actions irréversibles ou à forte concurrence (réserver un créneau, débiter un
paiement, envoyer un message définitif) — là, mieux vaut un état "en cours" honnête plutôt qu'un
succès emprunté.

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| UI optimiste avec rollback explicite | Code de compensation à écrire, gestion du cas d'échec | Interface perçue comme instantanée | Actions réversibles, faible risque de conflit (archiver, marquer lu) |
| UI pessimiste (attendre la confirmation serveur) | Latence perçue plus élevée | Jamais de mensonge affiché | Actions irréversibles ou à fort enjeu (réservation, paiement) |
| Squelette de chargement adapté à la forme du contenu | Un peu de CSS/composants dédiés | Réduit le layout shift et la perception de lenteur | Tout chargement de plus de 300ms |
| Accessibilité clavier dès la conception | Discipline sur les composants (vrais boutons, focus géré) | Coût quasi nul en amont, énorme en correctif | Toujours — c'est un sous-ensemble d'usage réel, pas une option |

## Pièges classiques
- Un composant custom "bouton" est construit sur une `div` stylée, invisible au clavier et aux
  lecteurs d'écran, découvert seulement lors d'un audit tardif.
- Un revert d'UI optimiste se fait sans message : l'utilisateur ne comprend pas pourquoi une ligne
  qu'il venait de faire disparaître réapparaît sans explication.
- Le spinner tourne indéfiniment sans limite de temps annoncée : au-delà de quelques secondes,
  l'utilisateur ne sait pas s'il doit attendre ou recommencer.
- L'UI optimiste est appliquée à une action à forte concurrence (réservation de créneau) sans
  vérification serveur derrière : deux personnes voient chacune "leur" créneau confirmé.

## Ce que tu dois savoir défendre
- Donne un exemple d'action où l'UI optimiste est un bon choix, et un exemple où elle est
  dangereuse. Explique la différence de critère entre les deux.
- Pourquoi un squelette de chargement réduit-il la perception de lenteur sans changer le temps de
  réponse réel du serveur ?
- Pourquoi une `div` avec un `onClick` est-elle un problème d'accessibilité concret, pas une
  question de conformité abstraite ?
