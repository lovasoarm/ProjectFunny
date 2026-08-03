# Grimoire : Niveau 08 : Roadmap Run

## La règle en une phrase

Un plan ne vaut rien tant qu'il n'attaque pas d'abord ce qu'on ne sait pas encore et ce que
ça coûterait de se tromper dessus.

## Checklist avant de proposer une date

- [ ] J'ai listé au moins cinq hypothèses concrètes et vérifiables du projet, pas des
      intentions vagues.
- [ ] Pour chaque hypothèse, j'ai estimé incertitude (faible/élevée) et coût si fausse
      (faible/élevé).
- [ ] J'ai identifié le risque le plus cher : incertitude élevée + coût élevé si faux.
- [ ] J'ai une méthode concrète pour tester ce risque en moins d'une à deux journées.
- [ ] Mon découpage en phases est vertical (chaque phase traverse toutes les couches) et pas
      par couche technique.
- [ ] Chaque jalon de mon plan est binaire, vérifiable par un tiers, daté, rattaché à un
      artefact concret.
- [ ] Aucun jalon n'est formulé en pourcentage.

## Matrice de risque (à recopier pour chaque hypothèse)

```text
              coût faible si fausse    coût élevé si fausse
incertitude   à faire sans stress      à vérifier vite
faible

incertitude   à explorer, coût         RISQUE LE PLUS CHER
élevée        limité si faux           → phase 1, avant tout le reste
```

## Trois signaux de dérive à surveiller chaque semaine

1. Le même pourcentage d'avancement se répète deux fois de suite → creuser immédiatement.
2. Les questions posées deviennent plus vagues au lieu de plus précises → signal de blocage.
3. Le périmètre d'une tâche grossit sans que sa date bouge → risque absorbé en silence.

## Formule de la démo comme signal fiable

Ne jamais demander "où en es-tu ?". Toujours demander : "montre-moi ce qui marche maintenant,
avec des données réelles." Une réponse invérifiable n'est pas un signal, c'est du bruit.

## Vocabulaire

- **Jalon (milestone)** : point de vérification binaire, daté, rattaché à un artefact —
  jamais un pourcentage.
- **Tranche verticale (vertical slice)** : découpage qui traverse toutes les couches du
  système pour un scénario réduit mais complet, à l'opposé d'un découpage par couche
  technique.
- **Risque le plus cher** : hypothèse à la fois incertaine et coûteuse à corriger si fausse,
  souvent difficile à inverser une fois du travail construit dessus.
- **Dérive silencieuse** : écart entre avancement réel et avancement déclaré qui grandit
  sans être signalé, jusqu'à devenir un retard officiel soudain.

## Phrase à ressortir en réunion de planning

"Avant de donner une date, je veux d'abord savoir ce qu'on ne sait pas : et vérifier la
partie qui coûterait le plus cher si on se trompait."
