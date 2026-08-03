# Grimoire : Niveau 08 : Roadmap Run

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| Jalon (milestone) | Point de verification binaire, date, rattache a un artefact, jamais un pourcentage. | Un "80% fait" ne dit rien de verifiable, et le retard se decouvre trop tard. | Ton prochain jalon est-il binaire, date, et rattache a un artefact concret ? |
| Tranche verticale (vertical slice) | Decoupage qui traverse toutes les couches du systeme pour un scenario reduit mais complet, a l'oppose d'un decoupage par couche technique. | On finit "toute la base de donnees" puis "tout le back" sans jamais avoir un scenario complet demontrable avant la toute fin. | Ta phase 1 traverse-t-elle toutes les couches pour un scenario complet, ou seulement une couche technique ? |
| Risque le plus cher | Hypothese a la fois incertaine et couteuse a corriger si fausse, souvent difficile a inverser une fois du travail construit dessus. | On construit des semaines sur une hypothese fausse, et le cout de correction explose parce que tout est deja batti dessus. | Quel est le risque le plus cher de ton projet actuel, et l'as-tu deja verifie ? |
| Derive silencieuse | Ecart entre avancement reel et avancement declare qui grandit sans etre signale, jusqu'a devenir un retard officiel soudain. | Le retard n'est annonce que quand il est trop tard pour l'absorber sans casse. | Comment detectes-tu une derive silencieuse avant qu'elle devienne un retard officiel ? |
| Matrice de risque (incertitude x cout) | Outil qui classe chaque hypothese selon son incertitude et son cout si elle est fausse, pour designer ce qu'il faut verifier en premier. | On traite les risques dans l'ordre ou ils sont pratiques a traiter, pas dans l'ordre ou ils coutent cher. | Peux-tu placer tes cinq hypotheses principales dans la matrice incertitude x cout ? |
| Signal de derive : pourcentage repete | Le meme pourcentage d'avancement annonce deux fois de suite signale un blocage cache. | On croit que "ca avance" alors que rien de verifiable n'a change depuis la derniere fois. | As-tu deja vu le meme pourcentage annonce deux semaines de suite sans creuser ? |
| Signal de derive : questions plus vagues | Des questions qui deviennent plus vagues au lieu de plus precises indiquent un blocage. | Une equipe bloquee arrete de poser des questions precises, et personne ne le remarque a temps. | Les questions de ton equipe deviennent-elles plus precises ou plus vagues au fil du projet ? |
| Formule de la demo comme signal fiable | Ne jamais demander "ou en es-tu ?", toujours demander "montre-moi ce qui marche maintenant, avec des donnees reelles". | Une reponse invérifiable ("ca avance bien") remplace un vrai signal et masque un retard. | Que fais-tu si on te repond "ca avance bien" au lieu de te montrer quelque chose qui marche ? |

## Comportements evalues en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'échec |
| --- | --- | --- |
| Gestion de la pression | Tu proposes une alternative concrete et utilisable dans les deux heures | Tu cedes et donnes une date pour faire plaisir, ou tu refuses sechement sans alternative |
| Communication du risque | Tu formules le risque en consequence business comprehensible ("si les tantiemes sont faux, certains coproprietaires seront sur- ou sous-factures, ce qui est pire qu'un retard") | Tu utilises du jargon de planning ("on n'a pas fini le risk assessment") |
| Engagement | Tu fixes une date precise de retour avec un livrable nomme | Tu restes vague sur la suite ("je te recontacte") |
| Coherence avec le niveau (justification par un mecanisme) | Tu expliques que la date depend d'abord de la verification du risque le plus cher | Tu donnes une date basee sur une simple somme de taches connues |

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
élevée        limité si faux           --> phase 1, avant tout le reste
```

## Trois signaux de dérive à surveiller chaque semaine

1. Le même pourcentage d'avancement se répète deux fois de suite --> creuser immédiatement.
2. Les questions posées deviennent plus vagues au lieu de plus précises --> signal de blocage.
3. Le périmètre d'une tâche grossit sans que sa date bouge --> risque absorbé en silence.

## Formule de la démo comme signal fiable

Ne jamais demander "où en es-tu ?". Toujours demander : "montre-moi ce qui marche maintenant,
avec des données réelles." Une réponse invérifiable n'est pas un signal, c'est du bruit.

## Phrase à ressortir en réunion de planning

"Avant de donner une date, je veux d'abord savoir ce qu'on ne sait pas : et vérifier la
partie qui coûterait le plus cher si on se trompait."
