# ANNEXE : et après ? Spécialisations et signaux de progression

## Choisir une spécialisation sans se piéger

Une spécialisation utile est un endroit où tu absorbes de la complexité que les autres ne veulent
pas absorber. Ce n'est pas un langage, c'est un type de problème.

```text
TYPE DE PROBLEME              CE QUE TU ABSORBES               SIGNE QUE C'EST FAIT POUR TOI
----------------------------  -------------------------------  ------------------------------
Données et modélisation       le temps, l'historique, la        tu aimes les cas limites
                              cohérence                         métier
Fiabilité / production        la panne partielle, la charge     les incidents t'intéressent
                                                                plus qu'ils ne t'angoissent
Produit / cadrage             l'ambiguïté du besoin             tu poses les questions que
                                                                personne n'ose poser
Performance                   la mesure, les budgets            tu ne crois pas une intuition
                                                                sans profil
Plateforme / outillage        la friction des autres devs       tu automatises ton propre
                                                                agacement
```

Reste généraliste au moins deux ans avant de choisir. Une spécialisation prématurée te rend fragile
au changement de marché.

## Signaux de progression, dans l'ordre

1. Tu finis ce que tu commences. (Beaucoup de devs ne dépassent jamais ce point.)
2. Tu détectes une mauvaise idée avant de l'implémenter, et tu sais dire pourquoi.
3. Tu chiffres un compromis au lieu de le trancher au feeling.
4. Tu conçois pour le changement probable, pas pour tous les changements possibles.
5. On te consulte avant les décisions, pas après.
6. Tes absences ne bloquent pas l'équipe : tes décisions sont écrites.

Le passage 3 -> 4 est le plus dur : c'est là qu'on sur-généralise et qu'on fabrique de l'abstraction
inutile. Relis `06-ARCHI-LAB/05-choosing-architecture.md` chaque fois que tu sens venir un framework
maison.

## Ce qu'il faut lire ensuite

Pas de bibliographie de cent titres. Quatre livres, dans cet ordre, un par trimestre :

- _A Philosophy of Software Design_ (Ousterhout) : pour la profondeur des modules.
- _Designing Data-Intensive Applications_ (Kleppmann) : pour les données et la fiabilité.
- _Accelerate_ (Forsgren, Humble, Kim) : pour comprendre ce qui fait vraiment livrer une équipe.
- _Thinking in Systems_ (Meadows) : pour sortir de l'informatique et voir les boucles.

Lis-les en appliquant sur ton projet fil rouge. Un livre lu sans mise en pratique s'oublie en six
semaines.

## Le test final, à refaire tous les ans

Prends une décision technique que tu as prise il y a un an. Peux-tu :
la retrouver écrite, citer l'alternative écartée, dire si le critère de révision a été atteint ?

Trois oui : tu progresses. Sinon, tu accumules du kilométrage sans apprendre.
