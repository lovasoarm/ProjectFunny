# Pourquoi ce niveau existe : ROADMAP-RUN

## 1. Pourquoi ce niveau existe

Savoir coder une fonctionnalité et savoir dire quand elle sera prête sont deux compétences
différentes. Ce niveau existe parce que la deuxième s'apprend rarement en autodidacte : elle
demande de nommer l'incertitude à voix haute, ce que personne n'enseigne spontanément. Sans
méthode de planification, un projet techniquement bien construit peut quand même échouer,
parce qu'il arrive trop tard, avec un budget dépassé, et une confiance client déjà brûlée.

## 2. Ce qui casse sans lui (deux incidents chiffrés)

Incident 1 : une régie de refacturation d'énergie annonce "six semaines" pour un module de
répartition de charges. Le projet livre en semaine 11, soit un dépassement de 83 %, parce que
trois inconnues métier (règles dérogatoires, format d'export non documenté, erreur de
tantièmes historique) n'avaient jamais été isolées avant l'estimation.

Incident 2 : une entreprise de livraison frigorifique découpe sa roadmap par couche technique
(backend, frontend, intégration). Le défaut d'incompatibilité réseau n'est découvert qu'en
phase 3, sur 8 semaines de projet : 2 semaines de backend doivent être refaites, soit 25 % du
temps total du projet perdu à un stade où le budget est déjà engagé.

## 3. Qui souffre en premier

Un troisième cercle souffre presque toujours sans le savoir : les utilisateurs finaux, qui
reçoivent une version pressée en fin de course pour rattraper le retard, avec moins de tests
et moins de vérification que ce qui avait été prévu au départ.

Le développeur qui a donné l'estimation optimiste souffre en premier, en public, devant le
client ou le sponsor. Vient ensuite l'équipe entière, qui absorbe les heures supplémentaires
non planifiées. Le sponsor souffre en dernier, mais le plus durablement : sa propre crédibilité
externe (auprès de ses clients ou de sa direction) a été engagée sur une date qui n'a pas tenu.

## 4. Quand ça se manifeste

Le symptôme apparaît rarement au début. Les deux ou trois premières semaines d'un projet mal
planifié ressemblent à un projet bien planifié : le travail visible avance. Le décalage se
révèle typiquement au premier tiers du calendrier annoncé, quand la première inconnue non
nommée doit enfin être affrontée, et qu'aucun budget de temps ne lui avait été réservé.

Un signe additionnel : le silence en reunion de suivi. Tant que personne ne pose
de question precise sur une zone floue du projet, il y a de bonnes chances que cette
zone n'ait pas encore ete regardee en detail, pas qu'elle soit reglee.

## 5. Ce que tu sais faire à la sortie

Tu sais découper un projet en phases qui livrent une tranche verticale vérifiable plutôt qu'une
couche technique invérifiable. Tu sais repérer, avant de planifier, l'hypothèse la plus chère à
inverser si elle est fausse, et l'attaquer en premier. Tu sais distinguer un avancement déclaré
d'un avancement démontré, et repérer les trois signaux de dérive avant que le retard devienne
officiel.

## 6. Ce qui n'est pas couvert et où

- La négociation du budget ou du contrat avec le client n'est pas couverte ici : voir
  [10-TEAM-QUEST/04-communication-under-pressure.md](../10-TEAM-QUEST/04-communication-under-pressure.md).
- Le découpage du travail en tranches de valeur (base de ce niveau) est détaillé dans
  [03-MVP-SPLIT/02-slicing-value.md](../03-MVP-SPLIT/02-slicing-value.md), pas réexpliqué ici.
- La gestion d'un incident de production en cours de projet est couverte par
  [09-QUALITY-SHIELD/05-incidents-and-postmortem.md](../09-QUALITY-SHIELD/05-incidents-and-postmortem.md).

## 7. Prérequis

- Avoir déjà découpé un besoin en tranches verticales de valeur (niveau 03).
- Savoir estimer une tâche avec une fourchette plutôt qu'un chiffre unique
  ([03-MVP-SPLIT/04-estimating-honestly.md](../03-MVP-SPLIT/04-estimating-honestly.md)).
- Avoir une base de projet réelle sur laquelle appliquer la méthode : ce niveau ne s'apprend
  pas sur un exercice abstrait.
- Avoir déjà vécu, au moins une fois, un dépassement de délai non anticipé : la méthode se
  comprend mieux avec une douleur réelle en mémoire qu'en abstrait.

## 8. Erreurs de débutant les plus coûteuses

- Découper par couche technique pour rassurer l'organisation des équipes, en repoussant le
  risque d'intégration à la fin du projet.
- Donner un chiffre unique d'estimation en réunion, sous pression sociale, sans avoir ouvert le
  problème.
- Confondre "occupé" et "en avance" : accepter un pourcentage déclaré sans jamais demander une
  démonstration de ce qui fonctionne réellement.
- Traiter la découverte d'une inconnue comme une tâche de développement normale, avec la même
  estimation qu'une implémentation connue.

## 9. Le mécanisme sous-jacent

Un projet a un budget de risque, pas seulement un budget de temps. Chaque inconnue non nommée
consomme silencieusement ce budget. Le mécanisme de ce niveau consiste à rendre l'inconnue
visible tôt, quand elle coûte encore peu à corriger, plutôt que de la laisser remonter tard,
quand tout le reste du système a été construit par-dessus une hypothèse fausse.

```text
Budget de risque d'un projet
 semaine 1        semaine 5        semaine 10
   [connu]  [connu]  [inconnu decouvert tot, cout faible]
                                    [inconnu decouvert tard, cout multiplie]
Regle : plus une inconnue est decouverte tard, plus son cout de correction
augmente, car le code construit dessus doit etre defait, pas seulement corrige.
```

Analogie : planifier un projet, c'est le régisseur qui teste chaque effet technique avant la
générale plutôt que le jour du spectacle, et le chef de cordée qui repère les passages
dangereux sur la carte avant de les affronter en pleine paroi.
Où l'analogie casse : le régisseur et le chef de cordée peuvent observer directement le danger
avant d'agir. Un risque projet reste souvent invisible tant qu'on n'a pas construit la partie
du système qui le révèle : on planifie contre une incertitude qu'aucun instrument ne montre.

## 10. Contre-exemple : quand appliquer ce niveau serait une erreur

Un prototype jetable, construit en deux jours pour tester une idée devant trois utilisateurs et
destiné à être jeté quel que soit le résultat, ne mérite pas un découpage par phases ni un
risk-first planning complet. Le coût de la méthode dépasserait le coût du projet lui-même.
Ici, la règle de discernement est simple : la méthode de ce niveau se justifie dès qu'un projet
a plus d'une semaine de développement réel et qu'un tiers (client, sponsor, équipe) attend une
date. En dessous, planifier formellement est une perte de temps déguisée en rigueur.

## 11. Le coût d'apprentissage

Compter environ 6 à 10 heures pour intégrer la méthode sur un projet réel de taille moyenne : le
temps d'appliquer une fois le découpage en tranches verticales, une fois l'identification du
risque le plus cher, et de tenir un suivi d'avancement par démonstration sur au moins deux
semaines. La compétence ne se fixe pas en lisant, seulement en la pratiquant sur un projet où
une vraie date compte pour quelqu'un d'autre que toi.

## 12. Le signal observable de maîtrise

Tu maîtrises ce niveau quand, face à une nouvelle demande de date, ton premier réflexe est de
demander "quelles sont les inconnues" avant de donner un chiffre, et quand tu peux nommer, sans
préparation, l'hypothèse la plus chère à inverser d'un projet en cours.

## 13. Ce que l'IA fait et ne fait pas à ta place

Une IA peut t'aider à décomposer une tâche large en sous-tâches candidates, et à rédiger un
squelette de plan par phases. Elle ne peut pas savoir, à ta place, quelle inconnue métier est
réellement mal documentée dans ton contexte : cette information vit dans des personnes et des
systèmes réels que l'IA n'a jamais consultés. Elle ne peut pas non plus porter la responsabilité
de l'annonce de date faite à un sponsor : c'est un acte humain, engageant, qui reste le tien.

## 14. Réutilisation dans les niveaux aval

- [09-QUALITY-SHIELD](../09-QUALITY-SHIELD/README.md) réutilise la notion de jalon vérifiable
  pour définir ce qu'est un test qui "prouve" quelque chose plutôt qu'un test décoratif.
- [12-CAPSTONE-ARENA](../12-CAPSTONE-ARENA/README.md) exige un plan par phases avec risque
  nommé pour son propre projet final.
- [13-DAY-TO-LEGEND](../13-DAY-TO-LEGEND/README.md) réutilise le suivi par démonstration
  régulière comme discipline personnelle.

## 15. Trois questions à défendre à l'oral

1. Pourquoi un découpage par couche technique cache le risque d'intégration jusqu'à la fin du
   projet, même si chaque couche prise seule est bien construite.
2. Donne un exemple de jalon formulé comme un pourcentage, et reformule-le en jalon binaire,
   daté et rattaché à un artefact vérifiable.
3. Explique le mécanisme qui fait qu'une inconnue découverte tard coûte plus cher qu'une
   inconnue découverte tôt, et pas de façon linéaire.
