# Grimoire : Problem Hunt

## Vocabulaire

- **Demande** : ce que la personne dit vouloir ("il nous faudrait un tableau de bord").
- **Besoin réel** : le problème sous-jacent que la demande tente de résoudre, souvent
  différent de la demande formulée.
- **Contrainte** : ce qui limite la solution (budget, réglementation, matériel existant,
  formation des utilisateurs).
- **Signal faible** : indice observable qu'un besoin existe déjà et se débrouille sans
  solution officielle (tableur fantôme, contournement manuel, ticket récurrent, groupe de
  messagerie parallèle).
- **Job to be done** : ce que l'utilisateur essaie d'accomplir, indépendamment de qui il est.
  Format : "Quand [situation], je veux [action], pour pouvoir [bénéfice réel]."
- **Persona utile** : influence directement une décision de conception vérifiable.
- **Persona décoratif** : illustre un document sans jamais changer une décision.
- **Non-objectif** : décision explicite de ne jamais faire quelque chose, avec sa raison
  (risque, coût, complexité), écrite avant que la pression n'arrive.
- **Métrique de vanité** : peut monter sans que le problème réel soit résolu.
- **Métrique de succès produit** : reliée directement au symptôme initial, ne peut pas
  monter artificiellement sans progrès réel.
- **Seuil d'échec** : chiffre fixé avant le lancement, en dessous duquel l'équipe s'engage à
  reconnaître un échec et à décider (pivoter, arrêter, réitérer).

## Checklist avant de coder quoi que ce soit

- [ ] La demande initiale a été reformulée en besoin(s) réel(s), avec au moins deux
      interprétations envisagées et rejetées explicitement.
- [ ] Au moins un signal faible concret (pas hypothétique) appuie le besoin identifié.
- [ ] Les utilisateurs concernés sont décrits par leurs jobs to be done, pas par des
      personas décoratifs.
- [ ] Les non-objectifs sont écrits, chacun avec sa raison, et testés ("si on le supprime,
      une décision de conception change-t-elle ?").
- [ ] Une métrique de succès produit est définie, reliée au symptôme initial.
- [ ] Un seuil d'échec chiffré est fixé, avec une date d'évaluation.

## Questions à se poser en réunion de cadrage

- "Si cette fonctionnalité disparaissait, quel comportement observable des utilisateurs
  changerait ?"
- "Quel contournement les gens utilisent-ils aujourd'hui pour survivre sans cette solution ?"
- "Est-ce que cette métrique peut monter si le vrai problème n'est pas résolu ?"
- "Qu'est-ce qu'on refuse explicitement de faire, et pourquoi ?"
- "Dans six semaines, quel chiffre nous ferait dire honnêtement que ça n'a pas marché ?"

## Heuristique rapide : demande vs besoin

```text
Une demande commence souvent par une solution ("il nous faudrait une appli").
Un besoin réel se formule en termes de situation et de friction observable
("les secrétaires perdent cinq minutes par urgence à appeler les deux autres
cabinets pour savoir qui est disponible").

Règle pratique : si tu peux répondre à la demande sans savoir ce que la personne
fait concrètement aujourd'hui pour s'en sortir, tu n'as pas encore le besoin réel.
```

## Pièges à répéter à voix haute avant chaque cadrage

- Une demande formulée comme une solution cache presque toujours un besoin plus simple.
- Un persona qu'on ne recite jamais après le cadrage était décoratif.
- Un non-objectif qui ne referme aucune porte concrète n'a servi à rien.
- Une métrique facile à obtenir dès le premier jour est presque toujours une métrique de
  vanité.
- Un seuil d'échec écrit après avoir vu les résultats n'est plus un seuil, c'est une
  justification.
