# Deviner les contraintes derrière un design

## Le piège

Tu ouvres le code de facturation d'un cabinet vétérinaire multi-sites. Tu trouves une table
`invoice_lines_v2` à côté d'une table `invoice_lines` jamais supprimée, deux systèmes de
calcul de TVA qui coexistent selon la date de création de la facture, et une fonction
`computeTotal()` qui contient un `if (clinicId === 'CLINIQUE_LYON_CENTRE')` en dur. Ton
premier réflexe : "c'est du code sale, il faut tout réécrire". Ce réflexe, non vérifié, va te
faire perdre une semaine à réécrire un système qui gère en réalité une contrainte fiscale
réelle et non négociable que tu n'as pas encore identifiée.

## Ce qui se passe vraiment

Un design de code est une réponse figée à des contraintes qui, elles, ont bougé ou disparu.
Pour comprendre pourquoi un choix a été fait, tu dois répondre à quatre questions dans
l'ordre, parce que chacune élimine des hypothèses pour la suivante.

```text
1. Quand ce code a-t-il été écrit ?          → contexte technique et produit de l'époque
2. Quel problème résolvait-il alors ?         → pas le problème d'aujourd'hui, celui d'hier
3. Quelle contrainte externe l'a façonné ?    → légale, contractuelle, performance, équipe
4. Cette contrainte existe-t-elle encore ?    → décide si le code est une dette ou un besoin
```

### Question 1 — Quand ce code a-t-il été écrit

L'historique git donne une date. Croise-la avec ce que tu sais du produit à cette période
(taille de l'équipe, version de la stack, présence ou non de tel client majeur). Un design
"bizarre" écrit trois mois après la signature d'un gros client a de fortes chances de
répondre à une exigence spécifique de ce client.

```bash
git log --diff-filter=A --follow -- server/billing/computeTotal.ts
# date de création → recoupe avec le changelog produit ou les tickets de la période
```

### Question 2 — Quel problème résolvait-il alors

Le code que tu lis aujourd'hui répond peut-être à un problème qui n'existe plus. La table
`invoice_lines_v2` a peut-être été créée pour supporter un changement de règle de TVA à une
date donnée, sans que l'ancienne table ni les factures historiques ne puissent être migrées
rétroactivement — la loi interdit parfois de recalculer une facture déjà émise. Le doublon
n'est pas de la paresse, c'est une frontière légale figée dans le schéma.

### Question 3 — Quelle contrainte externe l'a façonné

Classe la contrainte probable dans une des familles suivantes, parce que chacune a une
signature différente dans le code :

```text
Contrainte légale/fiscale   → dates de bascule en dur, versions parallèles qui ne fusionnent
                               jamais, commentaires citant un article de loi ou un audit
Contrainte contractuelle    → identifiants de client en dur, comportements spéciaux
                               isolés dans des branches conditionnelles nommées par client
Contrainte de performance   → dénormalisation, tables de cache, champs dupliqués,
                               commentaires mentionnant un incident de charge
Contrainte d'équipe/temps   → duplication entre deux zones qui font presque la même chose
                               parce que deux équipes ne se sont jamais synchronisées
Contrainte de migration     → tables ou champs "v2" à côté de l'ancien, jamais nettoyés
                               parce que la bascule complète n'a jamais été priorisée
```

Le cas `if (clinicId === 'CLINIQUE_LYON_CENTRE')` sent la contrainte contractuelle ou
réglementaire locale (peut-être un régime de TVA différent en fonction d'une convention
spécifique à ce site). Avant de le supprimer, tu cherches le ticket ou le commit associé.

### Question 4 — Cette contrainte existe-t-elle encore

C'est la question qui transforme l'enquête en décision. Trois issues possibles :

```text
Contrainte encore active    → le design "moche" est en fait nécessaire, tu dois le garder
                               et au mieux l'isoler proprement, pas le supprimer
Contrainte disparue         → c'est de la vraie dette, tu peux proposer une suppression,
                               mais avec une preuve écrite que la contrainte a disparu
Contrainte inconnaissable   → tu ne peux pas vérifier (personne ne se souvient, aucun
                               document), tu dois traiter le code comme actif par défaut
                               et le protéger avec un test avant d'y toucher
```

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Supposer que tout code "moche" est de la dette à supprimer | Risque de casser une contrainte encore active, souvent découvert en production | Rapide à décider | Jamais sans vérification préalable |
| Poser la question aux personnes encore présentes | Rapide si la mémoire existe | Fiable si la personne était là et se souvient bien | Toujours en première option si disponible |
| Reconstruire la contrainte par l'enquête (git, tickets, commentaires) | Plus long, demande de la méthode | Fonctionne même si personne ne se souvient | Cas général, surtout sur du code ancien sans mémoire vivante |
| Écrire un test de caractérisation avant de toucher au code douteux | Coût d'écriture du test | Filet de sécurité qui révèle immédiatement si tu casses un comportement caché | Systématique dès que la contrainte reste incertaine |

## Pièges classiques

- **Tu attribues à de l'incompétence ce qui est en fait une contrainte que tu ignores.**
  Symptôme : tu proposes une "simplification" en réunion et un ancien de l'équipe t'arrête
  en trente secondes avec une phrase que tu ne pouvais pas deviner sans enquêter.
- **Tu supprimes un `if` en dur sans vérifier s'il correspond à un client encore actif.**
  Symptôme : une facturation casse silencieusement pour un seul client, découverte des
  semaines plus tard à la clôture comptable.
- **Tu prends un commentaire ancien pour une vérité actuelle.** Symptôme : le commentaire dit
  "temporaire, à corriger après la migration" mais la migration a eu lieu il y a quatre ans
  et personne ne l'a jamais retiré — le commentaire ment par obsolescence, pas par malice.
- **Tu ne distingues pas contrainte légale et contrainte de confort d'équipe.** Symptôme :
  tu traites une préférence d'ancien développeur avec la même rigidité qu'une obligation
  fiscale, ce qui te fait rater des simplifications sûres.
- **Tu enquêtes seul indéfiniment au lieu de demander.** Symptôme : tu passes deux jours à
  reconstruire une contrainte qu'une question de trente secondes à la bonne personne aurait
  clarifiée immédiatement.

## Ce que tu dois savoir défendre

- Pourquoi un design qui semble mauvais peut être la trace d'une contrainte légitime
  disparue, et comment vérifier laquelle des deux situations tu as devant toi.
- Les quatre familles de contraintes externes et leur signature typique dans le code.
- Pourquoi un test de caractérisation est le bon réflexe quand tu ne peux pas trancher avec
  certitude si une contrainte est encore active.
