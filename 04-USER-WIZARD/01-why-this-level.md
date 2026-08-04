# Pourquoi ce niveau existe

## La scène

Jeudi, 17h50. Le cabinet vétérinaire ferme dans dix minutes. L'accueil a trois personnes en attente
et deux appels en cours. Camille clique sur "Confirmer le rendez-vous" pour Monsieur Petit et son
chat. L'écran ne bouge pas pendant deux secondes : le wifi du cabinet est capricieux. Elle reclique.
Puis une troisième fois, en pestant. Le lendemain, la praticienne découvre trois rendez-vous
identiques à 9h00 pour le même chat, et un créneau bloqué pour rien toute la matinée.

Second incident, 2026-01-20, même cabinet : un client remplit le formulaire de prise de rendez-vous
en ligne sur son téléphone, dans le métro. La connexion coupe au moment de l'envoi. L'écran affiche
une page blanche. Le client, sans retour clair, referme l'appli et abandonne. L'analyse des logs
du mois montre 41 formulaires commencés et jamais soumis avec succès, soit environ 14% des tentatives,
sans qu'aucun message d'erreur exploitable n'ait été montré à l'utilisateur.

## Ce qui se passe vraiment quand on ignore ça

Un développeur qui code "l'écran qui marche" code en réalité 20% du produit. Les 80% restants sont
les chemins que personne ne montre en démo : la liste vide un lundi matin sans rendez-vous, l'erreur
réseau au milieu d'un scroll, le formulaire à moitié rempli quand la session expire, le tableau de
300 rendez-vous qui rend le navigateur inutilisable.

Ce niveau ne t'apprend pas de nouvelle techno. Il t'apprend à voir les états et les parcours que ton
cerveau de développeur a tendance à effacer, parce qu'ils n'apparaissent jamais dans le "happy path"
que tu testes en premier.

## Qui souffre en premier

Le client ou l'utilisateur final souffre en premier, immédiatement, en silence : il abandonne un
formulaire ou subit un doublon sans jamais remonter l'information au développeur. L'accueil du
cabinet souffre en second, en devant gérer l'incident humainement le lendemain.

## À quel moment du projet ça se manifeste

Ce risque se manifeste dès la première mise en usage réel, hors environnement de développement :
réseau instable, utilisateur pressé, double clic. Il ne se manifeste jamais en démonstration
interne, où le réseau est bon et le testeur patient et concentré.

## Ce que tu sais faire à la sortie

- Lister tous les états d'un écran avant de coder : idle, chargement, erreur, vide, partiel.
- Construire un formulaire qui empêche la double soumission par une clé d'idempotence, pas
  seulement par un bouton désactivé côté visuel.
- Écrire un message d'erreur actionnable, qui dit à l'utilisateur quoi faire, pas seulement
  qu'un problème est survenu.
- Repérer une hypothèse "l'utilisateur ne fera jamais ça" comme un signal d'alerte à vérifier.

## Ce que ce niveau ne couvre pas

Ce niveau ne couvre pas la garantie technique d'idempotence côté serveur, traitée au
[niveau 07](../07-API-DOJO/03-errors-and-idempotence.md). Il ne couvre pas non plus la
modélisation des données sous-jacentes à ces écrans, traitée au
[niveau 05](../05-DATA-SPELLS/README.md).

## Prérequis réels

Avoir suivi le [niveau 03](../03-MVP-SPLIT/README.md) : un parcours utilisateur ne se conçoit
qu'une fois le scénario livrable clarifié, sinon on dessine des écrans pour un périmètre encore
flou.

## Erreurs de débutant les plus coûteuses

- Ne concevoir que l'écran "ça a marché", en laissant les autres états au hasard de
  l'implémentation technique du framework.
- Désactiver un bouton uniquement côté visuel, sans protection côté serveur, ce qui laisse
  passer un double envoi dès qu'un utilisateur contourne l'interface, par exemple avec un
  double clic très rapide qui précède le rendu du bouton désactivé.
- Valider un champ uniquement côté client, ce qui revient à ne pas le valider du tout dès
  qu'une requête est envoyée par un autre moyen que le formulaire prévu.
- Écrire "une erreur est survenue" comme unique message d'erreur, ce qui ne donne à
  l'utilisateur aucune action possible.

## Le mécanisme sous-jacent

Le mécanisme est de traiter un écran comme une fonction de l'état du système, pas du succès
supposé d'une action. Un état a toujours plusieurs valeurs possibles : en attente, en erreur,
vide, partiellement chargé, chargé avec succès. Concevoir l'écran pour une seule de ces valeurs
revient à garantir que les autres seront gérées par accident, au moment où elles arrivent en
production sur un vrai réseau instable.

```text
Sans ce niveau                          Avec ce niveau
---------------                         --------------
"ca marche chez moi"           -->      "ca marche sur 3G, en double-clic,
                                          hors-ligne, et avec 10 000 lignes"

ecran = fonction du succes     -->      ecran = fonction de l'etat
                                          (idle / loading / error / empty / partial)

formulaire = appel API         -->      formulaire = validation double +
                                          cle d'idempotence + message actionnable

"le serveur va gerer ca"       -->      "je sais ce que fait le serveur, et
                                          ce que je fais s'il ne repond pas"
```

## Pourquoi la véto et pas une todo-list

Une todo-list n'a pas de concurrence réelle (peu importe qui coche la case en premier), pas
d'enjeu (une tâche en double, on s'en fiche), pas de vraie donnée sensible. Un cabinet vétérinaire a
tout ça : deux personnes qui réservent le même créneau, un client qui appelle en même temps que
l'accueil clique, un dossier médical qu'on ne veut pas perdre à cause d'un rafraîchissement de page.
C'est un terrain qui te force à prendre les décisions que les vrais produits t'imposent.

## Analogie

Analogie : concevoir tous les états d'un écran, c'est comme le régisseur qui prépare un plan B
pour chaque panne possible avant le lever de rideau, et le skipper qui prévoit la manoeuvre de
sécurité avant que la tempête n'arrive.
Où l'analogie casse : le régisseur et le skipper préparent un nombre fini de scénarios connus
d'avance. Les états d'un écran incluent des combinaisons imprévues, comme une erreur réseau
survenant pendant l'affichage d'un état déjà partiel, que personne n'a listées à l'avance.

## Contre-exemple : quand appliquer ce niveau serait une erreur

Un écran de diagnostic interne, utilisé une seule fois par un développeur pour vérifier une
donnée en base, n'a pas besoin de gérer tous les états avec le même soin qu'un formulaire
client : le coût de traiter l'état d'erreur proprement dépasse largement le bénéfice pour un
outil jetable à usage unique et contrôlé.

## Le coût de l'apprentissage

Compter 3 à 4 heures de lecture pour les quatre leçons, et un effort de pratique plus long
que prévu au début : lister tous les états d'un écran prend l'habitude d'environ dix écrans
avant de devenir un réflexe rapide plutôt qu'un exercice laborieux.

## Comment savoir que tu maîtrises

Le signal observable : avant de coder un écran, tu listes spontanément ses états possibles
sur un papier ou dans un commentaire, sans qu'on te le rappelle. Un deuxième signal : un
testeur qui coupe le réseau au milieu de ton parcours ne trouve pas d'écran figé ou de
doublon créé.

## Ce que l'IA fait à ta place, et ce qu'elle ne fait pas

Une IA peut générer le code des cinq états d'un écran une fois que tu les as listés. Elle ne
liste pas ces états à ta place de façon fiable : demandée seule, elle produit souvent
uniquement l'état de succès, parce que c'est celui qui apparaît le plus dans les exemples sur
lesquels elle a été entraînée. Le rôle qui reste le tien : nommer ce qui peut mal tourner
avant de demander le code.

## Comment ce niveau est réutilisé plus tard

La notion de clé d'idempotence introduite ici se retrouve avec sa version serveur complète au
[niveau 07](../07-API-DOJO/03-errors-and-idempotence.md). La discipline de lister les états
avant de coder prépare directement l'observabilité traitée au
[niveau 09](../09-QUALITY-SHIELD/03-observability.md).

## Ce que tu dois savoir défendre

- Pourquoi un écran qui gère bien le cas "ça marche" n'est fiable qu'à 20% ?
- Donne un exemple concret où l'absence de gestion d'état a un coût métier mesurable (pas juste
  "c'est moche").
- Pourquoi la concurrence (deux personnes qui agissent en même temps) est un problème de design
  d'interface, pas seulement un problème de base de données ?
