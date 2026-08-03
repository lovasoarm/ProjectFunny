# Grimoire : Tool Cave

## Méthode de debug, en une page

```text
1. Reproduire à volonté (sinon : construire le scénario minimal, quitte à le rendre laid)
2. Formuler une hypothèse falsifiable (prédit un résultat observable différent si vraie/fausse)
3. Diviser l'espace de recherche en deux (bissection : historique, code, données, config)
4. Tester la moitié suspecte, répéter jusqu'à convergence (O(log n), jamais O(n))
5. Confirmer la cause racine avec un log ciblé ou un test isolé, pas une intuition
6. Écrire un test de non-régression AVANT de considérer le bug clos
7. Retirer les logs de debug ajoutés en cours de route
```

## Checklist de vérification avant de coller du code généré par IA

- [ ] Je peux expliquer cette logique à voix haute sans relire le code.
- [ ] J'ai identifié l'hypothèse implicite sur les données d'entrée (nulls, formats, ordres
      de grandeur).
- [ ] J'ai vérifié que les cas limites de mon domaine précis sont couverts, pas ceux d'un
      domaine générique.
- [ ] J'ai testé sur au moins un cas dont je connais la réponse correcte à la main, avant
      d'écrire le code.

## Prompt de décision : patron réutilisable

```text
"Je dois [tâche]. Liste-moi les approches possibles, avec pour chacune :
- l'hypothèse métier qu'elle suppose implicitement,
- un cas où elle serait fausse ou injuste,
- son coût de mise en œuvre.
Ne génère pas encore de code : j'ai besoin de choisir en connaissance de cause d'abord."
```

## Signaux d'alarme : code généré à relire en priorité absolue

- Le code "a l'air fini" en trois secondes sur un problème qui te prendrait normalement
  vingt minutes à réfléchir : la vitesse cache probablement une hypothèse non vérifiée.
- Aucune gestion de cas limite ou d'erreur visible dans la première lecture.
- Des noms de variables génériques (`data`, `value`, `result`) sur une logique métier
  précise : signe que le modèle a généralisé au lieu de traiter ton cas réel.
- Un commentaire qui explique "ce que fait le code" sans jamais expliquer "pourquoi ce choix
  plutôt qu'un autre" sur un point ambigu du domaine.

## Trois sources de référence pour sortir d'un doute d'outillage, du plus fort au plus faible

1. Un résultat mesuré en conditions réalistes (profiler en environnement représentatif,
   test de non-régression qui échoue puis passe).
2. Une revue par quelqu'un de plus expérimenté que toi sur le sujet précis.
3. Ta propre relecture à froid, une fois la pression du moment retombée.

## Formule à retenir

```text
Vitesse de livraison utile ≈ Compréhension du problème × Maîtrise des outils

Un facteur proche de zéro effondre le produit, quelle que soit la qualité de l'autre.
```
