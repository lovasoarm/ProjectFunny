# Boss-fight : Tool Cave

## La scène

3h12 du matin. Le job de refacturation mensuelle d'énergie de l'immeuble collectif tourne
depuis 22h. Il doit produire une facture par appartement avant l'ouverture du service client
à 8h. A 3h12, une alerte tombe : le job a planté après avoir traité 340 des 512 appartements
du portefeuille. Le relancer tel quel risque de refacturer deux fois les 340 déjà traités.
Ne rien faire garantit que 172 locataires n'ont aucune facture à 8h. Tu es seul, en astreinte,
sans accès à un assistant IA (panne réseau du fournisseur cette nuit-là, pure coïncidence).
Tu as une seule tentative de correction avant la fenêtre de facturation : le job ne peut pas
être relancé une deuxième fois sans validation manuelle d'un responsable, injoignable avant
7h.

## Contrainte de l'exercice

- Une seule tentative de correction et de relance. Pas d'essai-erreur.
- Aucun accès IA, aucune recherche externe : uniquement les logs fournis, le code du job (que
  tu es supposé connaître, tu l'as écrit il y a six mois) et ta méthode.
- Tu dois rendre une copie écrite avant de proposer ton action : hypothèse, preuve, décision.
  Une action non justifiée par écrit avant exécution ne compte pas, même si elle est bonne.

## Logs partiels fournis

```text
02:58:41 INFO  billing-job start batch_id=2024-11-monthly total_units=512
02:58:41 INFO  billing-job tariff_table loaded rows=6 last_updated=2024-11-01
03:04:12 INFO  billing-job unit=118 computed amount_cents=8420 status=ok
03:04:13 INFO  billing-job unit=119 computed amount_cents=8420 status=ok
03:04:13 WARN  billing-job unit=120 surface_m2=null fallback=default_surface(45)
03:04:14 INFO  billing-job unit=120 computed amount_cents=6210 status=ok
...
03:41:02 INFO  billing-job unit=339 computed amount_cents=9130 status=ok
03:41:03 INFO  billing-job unit=340 computed amount_cents=9130 status=ok
03:41:03 ERROR billing-job unit=341 tariff_lookup failed reason="tranche_horaire not found for unit 341"
03:41:03 ERROR billing-job uncaught exception in computeInvoice(unit=341)
03:41:03 ERROR billing-job process exiting, batch marked FAILED, no rollback executed
03:41:04 INFO  billing-job invoices_committed=340 invoices_pending=172
```

```text
$ grep unit=341 -r ./data/units
data/units/2024-11.csv:341,Dupont,3,58.0,tranche_nuit_reduite

$ grep tranche_nuit_reduite ./config/tariffs.json
(aucun resultat)

$ git log --oneline -3 -- config/tariffs.json
a91f3c2 retrait de la tranche nuit reduite, plan tarifaire 2024 simplifie
5b6e001 ajout tranche_weekend
2c9a410 tarifs initiaux
```

```text
$ psql -c "select count(*) from invoices where batch_id='2024-11-monthly';"
 count
-------
   340

$ psql -c "select unit_id, committed_at from invoices where batch_id='2024-11-monthly' order by committed_at desc limit 2;"
 unit_id |        committed_at
---------+-----------------------------
     340 | 2024-11-03 03:41:03.812+00
     339 | 2024-11-03 03:41:02.401+00
```

Le code du job (extrait pertinent, que tu es censé connaître) :

```ts
async function runMonthlyBilling(batchId: string, units: Unit[]): Promise<void> {
  for (const unit of units) {
    const invoice = await computeInvoice(unit); // leve une exception si tranche absente
    await db.commitInvoice(batchId, unit.id, invoice); // commit unitaire, pas de transaction globale
  }
}
```

## Ce que tu dois rendre

Une copie écrite, avant toute exécution, contenant dans l'ordre :

1. L'hypothèse falsifiable sur la cause racine (pas le symptôme "ca a plante sur unit=341").
2. Le mécanisme précis qui explique pourquoi l'unité 341 casse et pas les 340 précédentes.
3. Le protocole que tu suis pour vérifier cette hypothèse avec les seules données fournies,
   sans relancer le job.
4. La décision d'action pour tenir la fenêtre de 8h : ce que tu fais des 340 déjà facturées,
   ce que tu fais des 172 restantes, ce que tu fais de l'unité 341 en particulier.
5. Le compromis que cette décision assume, nommé explicitement : ce que tu sacrifies pour
   tenir l'heure, et ce que tu refuses de sacrifier même sous pression.
6. Ce que tu ne sais pas avec certitude au moment de trancher, et ce que tu vérifieras a
   posteriori dans la matinée.

## Grille d'évaluation

| Critere | Points | Ce qui est note |
|---|---|---|
| Justification par un mecanisme | 25 | La cause racine identifiee (tranche_nuit_reduite retiree du referentiel tarifaire le a91f3c2, encore referencee par certaines unites) est nommee et reliee au code, pas seulement au message d'erreur |
| Protocole de verification sans nouvelle execution | 20 | Le protocole utilise uniquement les logs, git log et les requetes SQL fournies pour confirmer l'hypothese avant d'agir |
| Compromis nomme et assume | 20 | La decision dit explicitement ce qui est sacrifie (ex: retarder les 172 factures restantes plutot que risquer un double commit sur les 340 deja passees) et pourquoi ce sacrifice est le bon sens de priorite |
| Honnetete sur ce que tu ne sais pas | 15 | La copie liste au moins un point non verifiable cette nuit (ex: combien d'autres unites referencent une tranche retiree) et prevoit sa verification ulterieure, sans l'affirmer comme certain |
| Coherence de la decision avec la contrainte "une seule tentative" | 20 | La decision ne relance pas aveuglement le batch complet ; elle traite separement les 340 commitees, les 172 en attente, et le cas 341 |

Total : 100 points. Seuil de passage : 70 points.

## Ce qui est eliminatoire (note globale ramenee a 0 quel que soit le score)

- Relancer ou proposer de relancer `runMonthlyBilling` sur les 512 unites sans traiter le
  risque de double commit sur les 340 deja committees.
- Affirmer une cause racine comme certaine sans la relier a une preuve presente dans les logs
  ou le `git log` fournis (une hypothese non verifiee presentee comme un fait).
- Agir avant d'avoir ecrit l'hypothese et le protocole : une bonne action non justifiee par
  ecrit au prealable ne compte pas.
- Ignorer l'unite 341 (ni facturee, ni signalee explicitement comme exclue avec sa raison).
