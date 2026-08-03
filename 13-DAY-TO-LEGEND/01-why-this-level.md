# Pourquoi ce niveau existe

## Le piège

Tu es développeur depuis trois ans dans une entreprise de logistique de tournées de
livraison. Tu maîtrises parfaitement la stack de ton équipe. Tu livres tes tickets à temps,
personne ne se plaint. Tu postules à un poste de développeur senior ailleurs, mieux payé.
Premier entretien technique : on te demande de justifier un choix d'architecture sur un cas
que tu n'as jamais rencontré dans ton entreprise actuelle — un système avec forte contrainte
de cohérence entre deux bases de données. Tu bloques. Pas parce que tu manques
d'intelligence : parce que tu as fait la même chose, dans le même contexte, avec les mêmes
contraintes, pendant trois ans. Ton nombre d'années d'expérience a augmenté. Ta compétence,
elle, a plafonné après la première année — le reste n'était que de la répétition.

## Ce qui se passe vraiment

### La confusion entre ancienneté et compétence

Une carrière technique se mesure en années. La compétence, elle, ne progresse pas de façon
linéaire avec le temps passé — elle progresse avec le nombre de **situations nouvelles
correctement digérées**. Un développeur qui reste cinq ans sur le même produit, avec la même
équipe, les mêmes types de bugs, atteint un plateau bien avant l'année cinq, et le reste du
temps ne fait que consolider ce qu'il savait déjà.

```text
Compétence réelle dans le temps — deux trajectoires possibles

  Compétence
     │
     │           ___________________________  ← trajectoire A : plateau après 18 mois
     │          /                              (même contexte, mêmes problèmes répétés)
     │         /
     │        /        __/‾‾/‾‾/‾‾/‾‾         ← trajectoire B : progression par paliers
     │       /        /                          (exposition volontaire à du nouveau,
     │      /        /                            feedback, correction)
     │_____/________/_______________________ Temps
          1 an     3 ans    5 ans

  Même durée. Compétence radicalement différente à l'arrivée.
```

La différence entre les deux trajectoires n'est pas le talent de départ. C'est un mécanisme
observable : la trajectoire B contient des boucles de feedback régulières qui forcent la
correction d'erreurs invisibles en autonomie totale. La trajectoire A n'en contient aucune —
le travail quotidien devient une routine confortable qui ne remet jamais rien en question.

### Pourquoi "travailler beaucoup" ne suffit pas

Le travail professionnel quotidien optimise pour livrer, pas pour apprendre. Ce sont deux
objectifs différents, parfois même en tension. Livrer vite pousse à réutiliser ce qu'on
sait déjà faire — c'est rationnel à court terme, un ticket urgent n'est pas le bon moment
pour essayer une technique qu'on maîtrise mal. Mais si 100% de ton temps est consacré à
livrer avec ce que tu sais déjà, 0% est consacré à élargir ce que tu sais. Le "deliberate
practice" (pratique délibérée), concept étudié depuis les années 1990 sur les musiciens et
les joueurs d'échecs, distingue précisément ces deux régimes :

```text
Travail ordinaire                     Pratique délibérée
─────────────────                     ───────────────────
Objectif : produire un résultat       Objectif : améliorer une capacité précise
Zone de confort (ce qu'on sait faire) Zone d'inconfort ciblée (juste au-delà du connu)
Feedback : "ça marche / ça marche     Feedback : spécifique et rapide sur CE qui
pas" en général, tardif                a raté et pourquoi
Répétable indéfiniment sans           S'arrête ou change de cible une fois la
progrès garanti                        capacité acquise
```

Un développeur qui ne fait jamais de pratique délibérée peut très bien réussir
professionnellement pendant des années — le marché récompense la fiabilité, pas seulement
la croissance de compétence. Mais le jour où le contexte change (nouvel employeur, nouveau
type de projet, montée en responsabilité), l'absence de réserve de compétence transférable
se voit immédiatement, et brutalement.

### Le vrai risque : l'obsolescence silencieuse

Le risque n'est pas de rester "mauvais". C'est de rester bon **dans un contexte de plus en
plus étroit**, sans s'en rendre compte, parce que personne ne te le dit — ton équipe actuelle
n'a aucune raison de remarquer que tu ne saurais pas résoudre un problème qu'elle ne
rencontre jamais. La seule façon de détecter ce risque tôt est de s'exposer volontairement,
régulièrement, à des problèmes hors de ton contexte habituel : du code d'autres équipes, des
décisions d'architecture que tu n'as pas prises, des katas qui simulent des situations que
tu n'as pas encore vécues.

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Rester dans sa zone de confort technique | Aucun coût immédiat visible | Vitesse de livraison maximale à court terme | Périodes de forte charge, crise, deadline serrée — jamais comme régime permanent |
| Routine de pratique délibérée régulière | 3-5 heures/semaine prélevées sur du temps "libre" ou du temps pro dédié | Compétence transférable, résilience au changement de contexte | Dès que tu vises une progression au-delà de ton poste actuel |
| Changer souvent de contexte (mission, projet, entreprise) | Coût de ramp-up répété, perte de profondeur sur un domaine | Large exposition à des problèmes variés | Début et milieu de carrière, avant de viser une expertise verticale profonde |
| Rester longtemps sur un seul domaine complexe | Risque de plateau si aucune pratique délibérée en parallèle | Expertise verticale rare et recherchée | Une fois qu'on a déjà une base large, pour devenir la référence d'un domaine précis |

## Pièges classiques

- **La confusion "j'apprends" / "je fais de la veille".** Symptôme : tu lis des articles
  techniques tous les jours, tu ne codes jamais rien de nouveau à partir de ce que tu lis.
  La lecture passive sans application ne change quasiment pas la compétence pratique.
- **Le syndrome du CV qui grossit sans le cerveau.** Symptôme : ta liste de technologies
  "connues" s'allonge chaque année, mais tu ne pourrais pas justifier un choix d'architecture
  à l'oral sans notes. Tu as accumulé du vocabulaire, pas du jugement.
- **La routine trop ambitieuse abandonnée en trois semaines.** Symptôme : tu te fixes deux
  heures de kata par jour, tu craques la première semaine chargée, tu culpabilises, tu
  arrêtes tout. Une routine tenable à 20 minutes vaut infiniment mieux qu'une routine
  parfaite abandonnée.
- **L'illusion de compétence par ancienneté.** Symptôme : tu te dis senior parce que tu as
  cinq ans d'XP, sans pouvoir citer une seule fois où tu as changé d'avis sur ta façon de
  concevoir un système suite à un retour ou une erreur.

## Ce que tu dois savoir défendre

- Explique la différence entre "travailler beaucoup" et "pratique délibérée" avec un exemple
  tiré de ta propre semaine de travail.
- Pourquoi une carrière technique peut plafonner malgré des années d'ancienneté qui
  continuent de s'accumuler.
- Donne un exemple concret de situation professionnelle qui t'a forcé, contre ta volonté, à
  sortir de ta zone de confort — et ce que tu en as retiré.
