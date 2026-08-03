# Grimoire — Niveau 01 Mindset

Mémo dense à garder ouvert pendant le challenge et le boss-fight. Pas de nouvelle théorie
ici : uniquement des heuristiques, gabarits et questions prêts à l'emploi.

## Heuristiques de coût de changement

- **Portée = coût.** Une décision qui touche un nom de variable locale coûte des minutes à
  changer. Une décision qui touche un format échangé entre services coûte des heures. Une
  décision qui touche un schéma de données partagé coûte des semaines et de la coordination
  humaine. Avant de trancher, situe la portée réelle de ta décision sur cette échelle.
- **Le coût de changement n'est pas symétrique.** Ajouter une contrainte plus tard est presque
  toujours plus facile que retirer une contrainte que d'autres ont commencé à exploiter.
  En cas de doute, commence permissif, resserre ensuite — pas l'inverse.
- **La dette n'est acceptable que nommée.** Une phrase qui ne contient ni le raccourci pris
  ni le signal de remboursement n'est pas de la dette volontaire : c'est de la dette subie
  déguisée.
- **La valeur d'option a un prix, elle aussi.** Ne préserve une option ("on pourrait migrer
  vers un event store plus tard") que si tu peux nommer le scénario concret qui l'activerait
  dans les 12 prochains mois. Sinon, c'est du sur-provisionnement.
- **Compare le coût de la preuve au coût de la construction, jamais la construction seule.**
  La question n'est pas "combien coûte de construire X" mais "combien coûte de savoir si X
  est nécessaire, comparé à ce que coûterait de se tromper en le construisant en aveugle".

## Questions de cadrage à poser avant toute décision structurante

1. Si je me trompe, qui s'en aperçoit, et dans combien de temps ?
2. Quel est le coût si je me trompe dans un sens ? Dans l'autre sens ? (asymétrie des erreurs)
3. Qu'est-ce que je crois savoir mais que je n'ai en réalité jamais vérifié ?
4. Quelle est l'expérience la moins chère qui réduirait cette incertitude avant que je
   m'engage plus loin ?
5. Cette décision est-elle réversible en heures, en jours, ou en semaines — et cette
   réponse a-t-elle changé la façon dont j'y réfléchis ?
6. Si un lead expérimenté contestait ce choix dans dix minutes, quel serait son meilleur
   argument, et ai-je une réponse fondée sur des faits, ou seulement une préférence ?
7. Quelle métrique ou quel seuil observerais-je qui me ferait revenir sur cette décision ?

## Gabarit ADR (à copier-coller)

```text
# ADR-NN : [titre court, orienté décision, pas problème]

## Statut
[Proposé | Accepté | Rejeté | Remplacé par ADR-XX] (date)

## Contexte
[2-4 phrases : quel problème, observé comment, pour qui]

## Options considérées
A. [option] — coût : [...] — bénéfice : [...]
B. [option] — coût : [...] — bénéfice : [...]
C. [option] — coût : [...] — bénéfice : [...]

## Décision
[Une phrase. L'option choisie, sans ambiguïté.]

## Justification
[Faits vérifiables séparés des jugements assumés. Pourquoi cette option
plutôt que les autres, dans ce contexte précis.]

## Conséquences
- [Ce qu'on accepte de perdre ou de risquer]
- [Condition observable et chiffrée qui déclencherait une révision]
```

## Gabarit note de conception (à copier-coller)

```text
# Note de conception : [nom]

## Problème
[Ce qui est observé ou anticipé, pour qui, impact si rien n'est fait]

## Contraintes
[Ce qui est non négociable : délai, budget, compétences, existant technique]

## Non-objectifs
- [Ce que cette note ne cherche pas à résoudre, explicitement]
- [...]

## Hypothèses
- [Hypothèse avec seuil chiffré] — vérifiable par : [expérience la moins
  chère, coût et délai estimés]

## Critères de succès
[Comment on saura, dans N semaines/mois, que la décision était la bonne]
```

## Gabarit RFC minimal (à copier-coller)

```text
# RFC : [titre]

## Problème
[...]

## Proposition (brouillon, pas encore décidée)
[...]

## Questions ouvertes sur lesquelles je veux votre avis
1. [question précise, pas fermée]
2. [...]

## Qui doit répondre, et avant quand
[...]
```

## Distinguer fait et jugement, en une ligne

- Un **fait** répond à "qu'est-ce qui a été mesuré ou observé, et par quel moyen vérifiable".
- Un **jugement** répond à "qu'est-ce que j'estime ou je préfère, sur la base de mon
  expérience". Un jugement n'est pas illégitime — il doit juste être nommé comme tel.

## Vocabulaire du niveau

| Terme | Définition opérationnelle |
|---|---|
| Dette volontaire | Raccourci pris consciemment, avec un signal de remboursement explicite |
| Dette subie | Raccourci pris sans évaluation du coût de changement, découvert après coup |
| Valeur d'option | Bénéfice de garder plusieurs futurs possibles ouverts, payé par un coût immédiat plus élevé |
| Hypothèse testable | Affirmation qui peut être fausse, formulée avec un seuil chiffré vérifiable |
| Réduction de risque au moindre coût | Choisir, parmi les façons de réduire une incertitude, celle qui coûte le moins avant d'investir dans la solution complète |
| Pari le moins cher | Parmi plusieurs options incertaines, celle dont l'échec coûte le moins à corriger — pas la plus probable de réussir |
| Asymétrie des erreurs | Le coût de se tromper n'est pas le même selon le sens de l'erreur (construire à tort vs ne pas construire à tort) |
| ADR | Document court qui capture une décision architecturale prise, son contexte, ses alternatives et ses conséquences |
| RFC | Document qui propose une décision avant qu'elle soit prise, pour solliciter la contradiction |
| Note de conception | Document qui cadre un problème, ses contraintes et ses critères de succès, avant de proposer des solutions |

## Auto-check avant de livrer une décision

- [ ] J'ai nommé au moins une hypothèse non vérifiée, avec un seuil chiffré.
- [ ] J'ai listé au moins trois options réelles, chacune avec un coût et un bénéfice.
- [ ] J'ai séparé les faits vérifiables des jugements assumés.
- [ ] J'ai écrit une condition observable qui me ferait changer d'avis.
- [ ] Un tiers qui n'a pas participé à la discussion pourrait reconstituer mon raisonnement
      à la seule lecture du document.
