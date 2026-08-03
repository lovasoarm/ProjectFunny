# Pyramide vs trophée, quoi tester selon le coût de la panne

## Le piège

L'équipe du système de tournées de livraison frigorifique a un objectif affiché : "80 % de
couverture de tests". Ils l'atteignent en six semaines en écrivant des centaines de tests
unitaires sur des fonctions pures (formatage de dates, calcul d'un total simple) : faciles à
tester, faible risque si elles cassent. Pendant ce temps, la fonction qui décide si une
tournée respecte la chaîne du froid (température mesurée, durée de trajet, seuils
réglementaires) n'a aucun test, parce qu'elle dépend de plusieurs services externes et
qu'elle est "compliquée à tester". Six mois plus tard, un changement de format de données du
capteur de température casse silencieusement cette fonction pendant deux semaines avant
d'être détecté par un client qui reçoit des produits périmés. Le chiffre "80 %" n'a rien
protégé de ce qui comptait.

## Ce qui se passe vraiment

La couverture de tests est une mesure de _quantité de code exécuté par des tests_, pas de
_probabilité de détecter un bug coûteux_. Une équipe qui optimise la couverture optimise
mécaniquement pour tester ce qui est facile à tester, pas ce qui coûte cher si ça casse —
parce que ce qui est facile à tester tire la métrique vers le haut plus vite.

```text
Coût réel d'une panne × probabilité qu'un bug s'y glisse = priorité de test

Fonction de formatage de date        Fonction de conformité chaîne du froid
coût si fausse : très faible          coût si fausse : produits perdus,
(un affichage moche)                  client perdu, potentiel litige sanitaire

priorité de test : basse              priorité de test : très haute
                                       (même si "compliquée à tester")
```

### Pyramide de tests, et pourquoi elle ne suffit pas seule

La pyramide de tests classique préconise beaucoup de tests unitaires (rapides, isolés),
moins de tests d'intégration, encore moins de tests bout-en-bout (lents, fragiles) :

```text
        ▲
       /E2E\          peu nombreux, lents, coûteux à maintenir
      /-----\
     /Integ. \        nombre moyen, vérifient les frontières entre modules
    /---------\
   /   Unit    \      nombreux, rapides, isolent une seule règle
  /-------------\
```

Cette forme est un bon défaut _technique_ : elle optimise pour la vitesse d'exécution et la
facilité de diagnostic (un test unitaire qui échoue pointe précisément la fonction en cause).
Mais elle ne dit rien sur _quoi_ tester à chaque niveau. Une pyramide remplie de tests
unitaires sur du code sans risque business est une pyramide creuse.

### Trophée de tests : une autre répartition, un autre pari

Le "trophée de tests" (popularisé pour les applications à forte logique d'intégration)
inverse une partie du pari : il mise gros sur les tests d'intégration, parce que c'est là
que se cachent la majorité des vrais bugs de production : aux frontières entre modules, pas
à l'intérieur d'une fonction isolée.

```text
        ▲
       / \    E2E : très peu, juste les parcours critiques
      /---\
     /     \  Integration : gros du budget de test : c'est ici que les
    /-------\  vrais bugs de frontière se révèlent (base de données réelle,
   /  Unit   \  appel réseau simulé fidèlement, format de message réel)
  /-----------\
     (statique : linter, typage : filet gratuit en continu)
```

Ni la pyramide ni le trophée n'est "la bonne réponse" universelle. Le bon choix dépend d'où
se trouve, dans _ton_ système, le point où les bugs coûteux se produisent réellement.

```text
Question à se poser pour chaque zone du système :
"Si un bug se glisse ici, quel est le coût réel, et à quel niveau (unité,
frontière entre modules, parcours complet) ce bug serait-il visible avant
la production ?"
```

## Exemple concret : où mettre l'effort sur le système de tournées

```typescript
// Fonction pure, coût de panne faible : test unitaire suffisant, rapide à écrire.
function formatDuration(minutes: number): string {
  const h = Math.floor(minutes / 60);
  const m = minutes % 60;
  return `${h}h${String(m).padStart(2, "0")}`;
}

// Règle métier à fort coût de panne : mérite un test d'intégration avec de
// vraies données de capteur (rejouées depuis un enregistrement réel), pas
// un mock trop simplifié qui masquerait les cas limites du capteur réel.
async function checkColdChainCompliance(
  tourId: string,
  sensorReadings: SensorReading[], // relevés réels, pas une valeur unique
  thresholds: ComplianceThresholds,
): Promise<ComplianceResult> {
  // logique de vérification : chaque relevé, sa durée hors seuil cumulée,
  // le seuil réglementaire par type de produit transporté.
  // ...
  return evaluateReadings(sensorReadings, thresholds);
}
```

Le test unitaire de `formatDuration` protège contre un affichage cassé : coût faible, effort
faible, cohérent. Le test de `checkColdChainCompliance` doit être un test d'intégration
utilisant un jeu de données de capteur réel rejoué (pas une valeur inventée à la main), parce
que le bug qui compte n'est pas dans la logique de calcul isolée, mais dans la façon dont
elle réagit à des données réelles bruitées, en retard, ou incomplètes.

## Compromis

| Option                                    | Coût                                                                | Bénéfice                                                           | Quand choisir                                                                       |
| ----------------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------ | ----------------------------------------------------------------------------------- |
| Optimiser pour la couverture globale      | Facile à mesurer, facile à afficher en réunion                      | Donne une impression objective de sérieux                          | Jamais comme objectif en soi : utile seulement comme indicateur secondaire          |
| Prioriser par coût de panne × probabilité | Demande une analyse préalable, moins facile à résumer en un chiffre | Concentre l'effort là où un bug coûterait vraiment cher            | Systématiquement, sur tout système avec des zones de risque inégal                  |
| Pyramide (beaucoup d'unitaires)           | Rapide à exécuter, diagnostic précis                                | Bon defaut pour de la logique pure et isolée                       | Système avec beaucoup de règles calculables indépendamment                          |
| Trophée (accent sur l'intégration)        | Tests plus lents, plus proches de la réalité                        | Détecte les vrais bugs de frontière (réseau, base, format externe) | Système dont le risque vient des interactions entre composants ou services externes |

## Pièges classiques

- Fixer un objectif de pourcentage de couverture sans lien avec le coût de panne : le
  symptôme est une suite de tests énorme qui n'a jamais attrapé le bug qui a fait mal.
- Tester une règle métier critique avec un mock trop simplifié : le symptôme est un test
  vert qui ne détecte pas un vrai bug parce que le mock ne reproduit pas les cas limites
  réels des données externes.
- Écrire des tests end-to-end pour chaque variation mineure : le symptôme est une CI si
  lente que l'équipe arrête de la faire tourner avant de merger.
- Considérer l'absence de test comme acceptable "parce que c'est compliqué à tester" : le
  symptôme récurrent : le code compliqué à tester est justement celui qui casse le plus
  silencieusement, parce que sa complexité cache aussi la complexité de ses bugs.

## Ce que tu dois savoir défendre

1. Explique pourquoi un pourcentage de couverture de tests peut monter alors que le risque
   réel de production, lui, ne baisse pas.
2. Pour un système que tu connais, identifie une zone à faible coût de panne (bon candidat
   pour un test unitaire simple) et une zone à fort coût de panne (candidate à un test
   d'intégration avec données réelles).
3. Explique la différence entre pyramide et trophée de tests, et quel facteur du système
   fait pencher le choix vers l'un ou l'autre.
