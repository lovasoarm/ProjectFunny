# Grimoire : Niveau 01, Mindset

Mémo dense à garder ouvert pendant le challenge et le boss-fight. Sert à trancher vite, pas à
réciter une théorie devant un lead qui attend une réponse dans la minute.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Dette volontaire | Raccourci pris consciemment, avec un signal de remboursement explicite et daté. | `printf "dette: colonne_unique; remboursement_si: volume > 10k/j\n" >> dette.yml` | ardoise assumée en cuisine de service / dette de sommeil acceptée avant un sommet |
| Dette subie | Raccourci pris sans évaluation du coût de changement, découvert après coup. | `git log --grep="TODO temporaire" --oneline` | improvisation forcée par une panne en régie / voie de repli non prévue en montagne |
| Valeur d'option | Bénéfice de garder plusieurs futurs possibles ouverts, payé par un coût immédiat plus élevé. | `printf "option_gardee: event_store; activee_si: besoin_audit_legal\n"` | garder deux voiles à bord au cas où le vent tourne / garder un plat de secours en cuisine |
| Hypothèse testable | Affirmation qui peut être fausse, formulée avec un seuil chiffré vérifiable. | `const hypothese = { texte: "80% des tournées < 2h", seuilFaux: "< 0.6" };` | prévision météo vérifiée en mer / test d'un menu du jour avant service complet |
| Réduction de risque au moindre coût | Choisir, parmi les façons de réduire une incertitude, celle qui coûte le moins avant d'investir dans la solution complète. | `printf "experience_low_cost: sondage_5_clients avant build complet\n"` | reconnaissance de la voie avant la cordée complète / mise en place légère avant le coup de feu |
| Pari le moins cher | Parmi plusieurs options incertaines, celle dont l'échec coûte le moins à corriger. | `printf "option_choisie: A; cout_echec_A: 2j; cout_echec_B: 3sem\n"` | test d'un plat en petite série avant carte complète / test d'ancrage avant d'engager le mât |
| Asymétrie des erreurs | Le coût de se tromper n'est pas le même selon le sens de l'erreur (construire à tort vs ne pas construire à tort). | `printf "cout_faux_positif: 2j; cout_faux_negatif: 3sem\n"` | fausse alerte incendie vs incendie non détecté aux urgences / rebrousser à tort vs continuer à tort en montagne |
| ADR | Document court qui capture une décision architecturale, son contexte, ses alternatives, ses conséquences. | `cp gabarit-adr.md docs/adr/ADR-012-historique-statuts.md` | fiche de sécurité en régie technique / journal de bord du navigateur |
| RFC | Document qui propose une décision avant qu'elle soit prise, pour solliciter la contradiction. | `printf "RFC: qui doit répondre = %s; avant = %s\n" "lead-back" "vendredi"` | briefing d'équipe avant le service / réunion météo avant de larguer les amarres |
| Note de conception | Document qui cadre un problème, ses contraintes et ses critères de succès avant de proposer des solutions. | `printf "criteres_succes: latence < 200ms d'ici 6 semaines\n" >> note.md` | fiche technique avant montage du décor / plan de course avant le départ |

## Défense orale

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Dette volontaire | Le raccourci devient permanent faute d'échéance nommée, personne ne sait qu'il faut y revenir | Quel est le signal chiffré qui déclenche le remboursement de cette dette ? |
| Dette subie | L'équipe découvre le coût au pire moment, en production, sans plan de sortie | Comment distingues-tu, sur ce projet, une dette subie d'une dette volontaire ? |
| Valeur d'option | Tu sur-investis dans une flexibilité que personne n'activera jamais | Quel scénario concret, dans les 12 prochains mois, activerait cette option ? |
| Hypothèse testable | Tu avances sur des convictions jamais confrontées au réel, invérifiables | Quelle expérience, la moins chère possible, vérifierait cette hypothèse ? |
| Réduction de risque au moindre coût | Tu construis la solution complète avant de savoir si le problème existe vraiment | Quelle est l'expérience la moins chère qui réduirait cette incertitude ? |
| Pari le moins cher | Tu choisis l'option la plus séduisante plutôt que celle dont l'échec est le moins cher | Si cette option échoue, combien ça coûte à corriger, comparé aux autres ? |
| Asymétrie des erreurs | Tu traites toutes les erreurs comme équivalentes et arbitres mal sous pression | Quel est le coût si tu te trompes dans un sens, et dans l'autre ? |
| ADR | La décision se reperd, se redébat, ou se réinterprète différemment selon qui la raconte | Un tiers pourrait-il reconstituer ton raisonnement à la seule lecture du document ? |
| RFC | La décision se prend en aveugle, sans la contradiction qui aurait pu révéler un angle mort | Qui doit répondre à cette RFC, et avant quand ? |
| Note de conception | On saute directement à la solution sans avoir vérifié qu'elle répond au bon problème | Comment saura-t-on, dans N semaines, que la décision était la bonne ? |

Grille détaillée : voir [boss-fight.md](./boss-fight.md).

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

## Si tu rates le boss-fight

Relis d'abord ta réponse au regard du critère "absence de céder par confort social" : c'est
lui qui plafonne la note s'il est faible. Relis ensuite la section "Heuristiques de coût de
changement" ci-dessus, puis réécris ta réponse en changeant uniquement le paragraphe où tu
cèdes ou refuses sans mécanisme. Attends 48 h avant de retenter la scène : le but est de la
rejouer avec du recul, pas de la corriger à chaud. Si tu rates une deuxième fois sur le même
critère, redescends au niveau 00 relire la règle "Défendre" : le problème n'est pas ce
niveau-ci, c'est l'habitude de défendre une décision à l'oral sans notes.
