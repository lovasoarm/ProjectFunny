# Niveau 15 — Bonus Vault

## Ce que c'est

Ce niveau n'enseigne pas de méthode nouvelle. Il archive, sous forme utilisable en situation
réelle, les outils transversaux que tous les niveaux précédents ont mobilisés sans jamais
les rassembler au même endroit : formats de décision, checklists d'exécution, ressources qui
ne périment pas, et catalogue des erreurs qui reviennent partout, sous des formes légèrement
différentes, dans toutes les équipes techniques.

Ce n'est pas un niveau à traverser dans l'ordre. C'est un coffre à ouvrir quand tu es en
situation réelle : tu dois trancher une décision d'architecture contestée, préparer une mise
en prod, gérer un incident, ou onboarder quelqu'un — et tu n'as pas le temps de réinventer le
format depuis zéro.

Durée estimée : pas de durée de lecture linéaire. Consultation ponctuelle, au moment où le
besoin réel se présente.

Prérequis : aucun niveau précédent n'est strictement requis pour lire ce coffre, mais son
usage prend tout son sens après avoir vécu une vraie décision d'architecture contestée ou un
vrai incident (Niveaux 6 à 12).

## Ce que tu sais faire à la sortie

- Tu sais choisir et remplir le bon format de décision (ADR, RFC, one-pager) selon l'enjeu
  et l'audience, sans sur-documenter une décision triviale ni sous-documenter une décision
  structurante.
- Tu as des checklists exécutables pour les quatre moments à plus haut risque d'un projet :
  mise en prod, revue de code, gestion d'incident, onboarding.
- Tu sais où chercher une réponse durable plutôt qu'un tutoriel qui périme en six mois.
- Tu reconnais, nommés et illustrés, les anti-patterns qui reviennent dans toutes les
  équipes techniques — et tu sais les repérer avant qu'ils ne deviennent la norme du projet.

## Structure du niveau

```text
15-BONUS-VAULT/
├── 01-decision-templates.md              → ADR, RFC, one-pager
├── 02-checklists.md                      → mise en prod, revue, incident, onboarding
├── 03-reading-list.md                    → ressources durables, sans liens morts
├── 04-anti-patterns-hall-of-fame.md      → catalogue commenté des erreurs qui reviennent
└── grimoire.md                            → mémo dense
```

## Comment lire ce niveau

Ouvre `01` la première fois qu'une décision technique dépasse le cadre d'une seule
personne. Ouvre `02` avant chaque mise en prod, revue, incident ou arrivée d'une nouvelle
personne dans l'équipe — pas après. `03` te donne où chercher quand un problème neuf sort du
cadre de ce curriculum. `04` se lit comme un miroir : la plupart des anti-patterns décrits,
tu les as déjà commis ou vus commettre, et les nommer est la première étape pour les éviter.

## Ce qui ne se passe pas ici

Ce coffre ne remplace aucun niveau précédent. Un template d'ADR mal rempli par quelqu'un qui
n'a jamais vécu une vraie décision contestée reste un exercice de style creux. Ce niveau
donne la forme ; les niveaux précédents donnent le fond.
