# Grimoire : Quality Shield

Ouvre ce mémo en plein incident ou juste avant une revue tendue. Il rappelle les réflexes du
bouclier qualité, pas la théorie complète des tests.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Les quatre couches du bouclier | Tests avant prod, observabilité en prod, revue + CI avant partage, postmortem après incident. Une seule couche ne suffit jamais. | `npm test && npm run lint && npm run build` | urgences d'hôpital / régie technique de spectacle |
| Priorisation des tests | Coût réel de la panne x probabilité qu'un bug s'y glisse, jamais un pourcentage de couverture visé. | `# priorite = impact_business * proba_bug, pas "facile a tester"\nvitest run tests/paiement.spec.ts --coverage=false` | navigation maritime / cuisine de restaurant en service |
| Pyramide vs trophée | Pyramide : beaucoup d'unitaires pour la logique pure. Trophée : accent sur l'intégration quand le risque vient des frontières. | `vitest run tests/integration/**/*.spec.ts` | atelier de menuiserie / urgences d'hôpital |
| Observabilité (logs, métriques, traces) | Logs structurés pour "quoi précisément", métriques pour "combien depuis quand", traces pour "où est passé le temps". | `logger.info({ event: "facture_generee", unitId, montantCents }, "facture generee")` | régie technique de spectacle / navigation maritime |
| Revue de code : machine vs humain | La machine traite le style, l'humain traite la logique métier, les cas limites et le risque de sécurité. | `eslint . --fix && echo "reste: logique metier, cas limites, securite"` | atelier de menuiserie / cuisine de restaurant en service |
| Test flaky | Test dont le résultat varie sans changement de code, souvent à cause du temps réel ou d'un état partagé. | `vitest run --retry=0 tests/flaky.spec.ts # jamais de retry qui masque le bug` | course en montagne / urgences d'hôpital |
| Incident commander | Personne qui centralise les décisions pendant l'incident, distincte de l'expert technique. | `# une seule personne decide pendant l'incident\necho "IC: @toi, decisions passent par toi jusqu'a resolution"` | régie technique de spectacle / navigation maritime |
| Postmortem sans blâme | Analyse centrée sur le système : chronologie factuelle, cause immédiate et structurelle, actions datées assignées. | `git log --since="2026-03-01" --until="2026-03-02" --oneline -- services/facturation/` | urgences d'hôpital / course en montagne |

## Défense orale

Pour la grille complète et chiffrée, va voir [./boss-fight.md](./boss-fight.md). Voici la matière
reformulée pour t'entraîner à l'oral.

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Trancher sous incertitude | Sans critère explicite, tu paralyses la décision ou tu paries au hasard pendant que l'impact grandit. | Quel critère lié à l'impact utilisateur réel utilises-tu pour trancher en pleine incertitude ? |
| Centraliser la coordination | Sans rôle clair, deux personnes agissent en parallèle et aggravent la panne. | Comment empêches-tu deux personnes d'agir en même temps sans se concerter pendant un incident ? |
| Séparer réparer et comprendre | Chercher la cause exacte avant d'agir retarde la réduction de l'impact réel. | Pourquoi répares-tu avant de comprendre en détail, et quand rattrapes-tu l'analyse ? |
| Communiquer pendant, pas seulement après | Sans nouvelles régulières, les parties prenantes imaginent le pire ou perdent confiance. | Que dis-tu à ton équipe pendant l'incident si tu ne sais pas encore la cause ? |

## Prioriser les tests : la seule formule qui compte

```text
cout reel de la panne x probabilite qu'un bug s'y glisse = priorite de test
```

Jamais : "c'est facile a tester" ou "pourcentage de couverture vise".

## Pyramide vs trophée

```text
Pyramide : beaucoup d'unitaires   --> bon pour la logique pure, isolee, calculable
Trophee   : accent sur l'integration --> bon quand le risque vient des frontieres
            (base reelle, reseau, format de donnees externe)
```

## Observabilité : trois piliers, trois questions

- **Logs structurés** (champs nommés, jamais du texte libre) --> "que s'est-il passé,
  précisément, ici".
- **Métriques** (compteur, jauge, histogramme) --> "combien, et depuis quand ça dérive".
- **Traces** (suivi à travers les services) --> "où est passé le temps, où ça a cassé".

Alerter sur un symptôme utilisateur visible, jamais sur une cause interne isolée (CPU,
mémoire) sans lien démontré avec un impact réel.

## Revue de code : ce qui revient à la machine, ce qui revient à l'humain

```text
Machine (linter, formateur) : style, indentation, imports, nommage mecanique
Humain (revue) : logique metier, cas limites, risque de securite, dette d'architecture
```

## CI : ordre des étapes, et la règle du test flaky

```text
push --> lint + typage (rapide) --> tests unitaires --> tests d'integration --> build + securite
```

Un test flaky se corrige ou se désactive avec un ticket de suivi : jamais relancé
indéfiniment "jusqu'à ce qu'il passe".

## Incident : deux temps qui ne se mélangent jamais

```text
Pendant  : reduire l'impact vite, une seule personne decide (incident commander),
           rollback avant comprehension complete si besoin.
Apres    : comprendre la cause structurelle, sans urgence de temps, actions assignees et datees.
```

## Postmortem sans blâme : structure en quatre temps

1. Chronologie factuelle, horodatée, basée sur logs et métriques réels.
2. Cause immédiate (le bug précis) ET cause structurelle (pourquoi le système l'a laissé
   passer sans alerte).
3. Ce qui a bien fonctionné, à préserver.
4. Actions concrètes, assignées à une personne, avec une date : jamais "faire plus attention".

## Commandes prêtes à copier

```bash
# 1. Profilage rapide d'un endpoint suspect en Node 20 LTS (verifie le 2026-08-03)
node --prof server.js &
curl -s http://localhost:3000/api/tournees > /dev/null
kill %1 && node --prof-process isolate-*.log > profil.txt
```

```bash
# 2. Bisect git pour trouver le commit qui a introduit une regression
git bisect start
git bisect bad HEAD
git bisect good v1.4.0
git bisect run npm test -- tests/facturation.spec.ts
```

```bash
# 3. Logs structures en JSON pour une question precise pendant un incident
echo '{"ts":"2026-03-01T09:12:00Z","event":"echec_confirmation","unitId":341,"reason":"tranche_horaire not found"}' \
  | tee -a incident.log
```

```bash
# 4. Test de charge minimal avec autocannon pour verifier un seuil avant mise en prod
npx autocannon -c 20 -d 30 https://staging.exemple.fr/api/tournees
```

```sql
-- 5. Requete de diagnostic Postgres 16 (verifie le 2026-08-03) : trouver les commits partiels d'un batch
SELECT unit_id, committed_at
FROM invoices
WHERE batch_id = '2026-03-monthly'
ORDER BY committed_at DESC
LIMIT 5;
```

## Si tu rates le boss-fight

Relis la leçon sur les deux temps de l'incident et celle sur la priorisation des tests avant
de retenter. Refais l'exercice en écrivant d'abord ton critère de décision par écrit, puis
seulement ta réponse. Donne-toi 48 heures avant la deuxième tentative, pas plus, pour garder
la pression réaliste de la scène. Si le score reste sous 50/100, remonte au niveau amont sur
les compromis nommés et assumés avant de revenir affronter ce boss-fight.
