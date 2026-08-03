# Grimoire : Niveau 01 Mindset

Mémo dense à garder ouvert pendant le challenge et le boss-fight.

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| Dette volontaire | Raccourci pris consciemment, avec un signal de remboursement explicite | Le raccourci devient permanent faute d'échéance nommée, personne ne sait qu'il faut y revenir | Quel est le signal chiffré qui déclenche le remboursement de cette dette ? |
| Dette subie | Raccourci pris sans évaluation du coût de changement, découvert après coup | L'équipe découvre le coût au pire moment, en production, sans plan de sortie | Comment distingues-tu, sur ce projet, une dette subie d'une dette volontaire ? |
| Valeur d'option | Bénéfice de garder plusieurs futurs possibles ouverts, payé par un coût immédiat plus élevé | Tu sur-investis dans une flexibilité que personne n'activera jamais | Quel scénario concret, dans les 12 prochains mois, activerait cette option ? |
| Hypothèse testable | Affirmation qui peut être fausse, formulée avec un seuil chiffré vérifiable | Tu avances sur des convictions jamais confrontées au réel, invérifiables | Quelle expérience, la moins chère possible, vérifierait cette hypothèse ? |
| Réduction de risque au moindre coût | Choisir, parmi les façons de réduire une incertitude, celle qui coûte le moins avant d'investir dans la solution complète | Tu construis la solution complète avant de savoir si le problème existe vraiment | Quelle est l'expérience la moins chère qui réduirait cette incertitude ? |
| Pari le moins cher | Parmi plusieurs options incertaines, celle dont l'échec coûte le moins à corriger | Tu choisis l'option la plus séduisante plutôt que celle dont l'échec est le moins cher | Si cette option échoue, combien ça coûte à corriger, comparé aux autres ? |
| Asymétrie des erreurs | Le coût de se tromper n'est pas le même selon le sens de l'erreur (construire à tort vs ne pas construire à tort) | Tu traites toutes les erreurs comme équivalentes et arbitres mal sous pression | Quel est le coût si tu te trompes dans un sens, et dans l'autre ? |
| ADR | Document court qui capture une décision architecturale prise, son contexte, ses alternatives et ses conséquences | La décision se reperd, se redébat, ou se réinterprète différemment selon qui la raconte | Un tiers pourrait-il reconstituer ton raisonnement à la seule lecture du document ? |
| RFC | Document qui propose une décision avant qu'elle soit prise, pour solliciter la contradiction | La décision se prend en aveugle, sans la contradiction qui aurait pu révéler un angle mort | Qui doit répondre à cette RFC, et avant quand ? |
| Note de conception | Document qui cadre un problème, ses contraintes et ses critères de succès, avant de proposer des solutions | On saute directement à la solution sans avoir vérifié qu'elle répond au bon problème | Comment saura-t-on, dans N semaines, que la décision était la bonne ? |

## Comportements évalués en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'échec |
| --- | --- | --- |
| Reconnaissance du point valide | Le texte identifie précisément ce que le lead a raison de signaler (perte d'information si un jour un historique fin est nécessaire) | Réponse défensive qui ignore l'objection ou la caricature |
| Distinction contexte / généralité | Le texte explique pourquoi l'expérience du lead sur un autre projet ne se transpose pas automatiquement (volume différent, besoin métier différent, délai différent) | Le texte traite l'analogie comme automatiquement valide ou automatiquement invalide sans l'examiner |
| Fondation sur des faits, pas des préférences (justification par un mécanisme) | Le texte cite au moins un élément vérifiable du projet réel (volume de tournées/jour, fréquence des demandes du service client, délai de livraison de la fonctionnalité) | Le texte reste au niveau des principes généraux ("la simplicité c'est important") sans jamais toucher au projet concret |
| Réversibilité explicite (compromis nommé et assumé) | Le texte propose un seuil ou un signal observable qui déclencherait la migration vers une table d'événements, si le choix actuel est maintenu | Le choix est présenté comme définitif et fermé, sans condition de révision |
| Absence de céder par confort social (honnêteté sur ce qu'on ne sait pas) | Si la décision initiale reste justifiée compte tenu du contexte, le texte le dit clairement sans se rétracter juste parce que le lead est catégorique | Le texte change de position uniquement à cause du ton assuré de l'interlocuteur, sans nouvel argument |
| Longueur et densité | Le texte tient dans environ 200 mots, sans détour | Réponse diluée qui noie l'argument dans des formules de politesse |

## Heuristiques de coût de changement

- **Portée = coût.** Une décision qui touche un nom de variable locale coûte des minutes à
  changer. Une décision qui touche un format échangé entre services coûte des heures. Une
  décision qui touche un schéma de données partagé coûte des semaines et de la coordination
  humaine. Avant de trancher, situe la portée réelle de ta décision sur cette échelle.
- **Le coût de changement n'est pas symétrique.** Ajouter une contrainte plus tard est presque
  toujours plus facile que retirer une contrainte que d'autres ont commencé à exploiter.
  En cas de doute, commence permissif, resserre ensuite : pas l'inverse.
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
5. Cette décision est-elle réversible en heures, en jours, ou en semaines : et cette
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
A. [option] : coût : [...] : bénéfice : [...]
B. [option] : coût : [...] : bénéfice : [...]
C. [option] : coût : [...] : bénéfice : [...]

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
- [Hypothèse avec seuil chiffré] : vérifiable par : [expérience la moins
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
  expérience". Un jugement n'est pas illégitime : il doit juste être nommé comme tel.

## Auto-check avant de livrer une décision

- [ ] J'ai nommé au moins une hypothèse non vérifiée, avec un seuil chiffré.
- [ ] J'ai listé au moins trois options réelles, chacune avec un coût et un bénéfice.
- [ ] J'ai séparé les faits vérifiables des jugements assumés.
- [ ] J'ai écrit une condition observable qui me ferait changer d'avis.
- [ ] Un tiers qui n'a pas participé à la discussion pourrait reconstituer mon raisonnement
      à la seule lecture du document.
