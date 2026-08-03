# Modifier sans casser

## Le piège

Tu as fini ta carte du système de gestion de tournées de livraison de colis frais. Le ticket
est clair : "le calcul de créneau affiche parfois un horaire impossible, corrige ça". Tu
trouves le bug : une comparaison de fuseau horaire mal faite dans `slotWindow.ts`. Tu changes
une ligne, tu lances le test qui touche cette fonction, il passe, tu ouvres la pull request.
Deux jours plus tard, la facturation de nuit explose : elle appelait la même fonction pour
calculer une fenêtre de remise tarifaire, et ta correction a changé silencieusement son
résultat. Tu n'avais pas cassé le calcul de créneau. Tu avais cassé un appelant que tu ne
savais même pas exister.

Comprendre le code ne suffit pas. Modifier du code que tu n'as pas écrit, dans un système que
d'autres personnes font tourner en production, est un acte différent : tu dois savoir, avant
de taper une seule ligne, jusqu'où ton changement se propage.

## Ce qui se passe vraiment

Une fonction, une table, un endpoint ne vivent jamais seuls. Ils ont des appelants, des
consommateurs de données, des tests qui encodent (parfois mal) un comportement attendu. Le
rayon d'impact d'une modification, c'est l'ensemble de ce qui dépend, directement ou
indirectement, de ce que tu t'apprêtes à changer. Le sous-estimer est la cause la plus
fréquente d'incident en production causé par un changement en apparence mineur.

```text
Ton changement
     │
     ▼
Fonction/table modifiée
     │
     ├──> Appelants directs (autres fonctions, autres services qui l'invoquent)
     ├──> Appelants indirects (tout ce qui appelle un appelant direct)
     ├──> Tests qui verrouillent un comportement (passants ou dormants)
     ├──> Données déjà écrites avec l'ancien comportement (lignes en base, fichiers, caches)
     └──> Consommateurs externes (API publique, export, job planifié, rapport)
```

Chacune de ces cinq branches peut casser d'une façon différente. Un appelant direct casse
tout de suite et bruyamment. Un consommateur de données casse en silence, des semaines plus
tard, quand quelqu'un s'étonne qu'un rapport ne corresponde plus à rien.

### Étape 1 : trouver tous les appelants, pas seulement ceux que tu devines

Une recherche de texte sur le nom de la fonction est le strict minimum, jamais la fin de
l'enquête. Un appel peut passer par une interface, un import renommé, une injection de
dépendance, un appel dynamique construit à partir d'une chaîne.

```bash
# Recherche directe du nom
rg -n "slotWindow" --type ts

# Recherche des imports du fichier, pour trouver qui l'utilise même sans appeler
# directement la fonction visée dans le même fichier
rg -n "from ['\"].*slotWindow" --type ts

# Si le langage a un outil d'analyse statique, utilise-le : il trouve les appels
# qu'une recherche texte rate (ré-export, alias, appel via une interface)
```

Note chaque appelant trouvé avec son chemin exact. Un appelant que tu n'as pas noté est un
appelant que tu ne testeras pas.

### Étape 2 : comprendre ce que chaque appelant attend, pas seulement qu'il existe

Deux appelants de la même fonction peuvent en attendre des choses incompatibles. Dans
l'exemple d'ouverture, `slotWindow.ts` calculait une fenêtre horaire pour deux usages
différents : la disponibilité d'un créneau de livraison, et l'éligibilité à une remise
tarifaire de nuit. Le bug de fuseau horaire n'affectait que le premier usage. Le corriger
sans regarder le second a changé un résultat que personne ne voulait changer.

```text
slotWindow(date, tz)
     │
     ├──> booking/checkAvailability.ts   attend : fenêtre en heure locale du client
     └──> billing/nightDiscount.ts        attend : fenêtre en heure locale de l'entrepôt
                                           (bug caché : même fonction, deux référentiels
                                            temporels différents utilisés par erreur)
```

Ici, le vrai correctif n'est pas de changer `slotWindow`, c'est de séparer les deux usages
qui n'auraient jamais dû partager la même fonction. Tu ne le découvres qu'en cartographiant
les appelants avant de toucher au code.

### Étape 3 : caractériser le comportement actuel avant de le changer

Avant de corriger quoi que ce soit, tu écris un test qui documente ce que le code fait
aujourd'hui, bug inclus si besoin. Ce test s'appelle un test de caractérisation : il ne dit
pas "voici ce qui est correct", il dit "voici ce qui se passe actuellement, et si ça change
sans que je le décide, je veux le savoir".

```ts
// Test de caractérisation : capture le comportement actuel, pas le comportement désiré.
// Il sert de filet avant modification, pas de spécification finale.
test("comportement actuel de slotWindow avec un fuseau UTC+1 en hiver", () => {
  const result = slotWindow(new Date("2026-01-15T10:00:00Z"), "Europe/Paris");
  // Valeur observée aujourd'hui, même si elle est fausse : elle sert de référence
  // pour mesurer l'écart exact que ton correctif va introduire.
  expect(result).toEqual({ start: "10:00", end: "12:00" });
});
```

Si ce test casse après ta modification, ce n'est pas automatiquement une mauvaise nouvelle :
c'est une mesure exacte de ce que tu changes. Tu décides ensuite, appelant par appelant, si
ce changement est voulu ou non.

### Étape 4 : le patch minimal

Une fois le rayon d'impact connu, tu écris le plus petit changement qui corrige le problème
réel sans toucher à ce qui n'a pas besoin de bouger. Un patch minimal a trois qualités :

```text
1. Il touche le nombre de fichiers le plus bas possible pour l'effet recherché.
2. Il ne renomme rien, ne réorganise rien "pendant qu'on y est".
3. Il est relisible en entier par quelqu'un d'autre en moins de dix minutes.
```

Le réflexe "pendant que je suis dans ce fichier, je nettoie aussi ça" est celui qui
transforme un correctif d'une ligne en revue de quarante fichiers, où le vrai changement se
noie dans du bruit et où le risque de régression grimpe sans bénéfice mesuré. Le nettoyage
mérite sa propre pull request, séparée, sans urgence de correction accolée.

```text
Analogie : un patch minimal, c'est l'intervention du chirurgien qui n'ouvre que la zone
nécessaire, et le réglage fin du régisseur qui ne touche qu'un seul projecteur en plein
spectacle sans couper l'éclairage de la scène entière.
Où l'analogie casse : le chirurgien et le régisseur travaillent sur un système qui ne se
répare pas tout seul entre deux interventions. Un patch de code, lui, peut être relu,
git-blamé et annulé six mois plus tard par quelqu'un qui n'a aucune idée du contexte que tu
avais en tête : ta discipline de patch minimal doit donc aussi se lire seule, sans toi.
```

### Étape 5 : la non-régression, pas seulement le test qui prouve que ça marche

Un test qui prouve que ton correctif fonctionne ne prouve pas que tu n'as rien cassé
ailleurs. Il te faut deux catégories de preuves, pas une seule :

```text
Preuve positive   → le nouveau comportement attendu est vérifié par un test qui échouait
                     avant ton patch et qui passe après.
Preuve négative    → l'ensemble des tests existants sur les appelants identifiés à l'étape 1
                     passe toujours, sans modification de leurs assertions.
```

Si tu dois modifier l'assertion d'un test existant pour le faire passer, arrête-toi : soit ce
test protégeait un comportement que ton patch casse réellement (et il faut alors reposer la
question à l'équipe ou au produit), soit ce test était déjà faux et tu dois documenter
pourquoi tu le corriges, séparément de ton patch principal.

### Étape 6 : les données déjà écrites, l'angle mort le plus fréquent

Le code n'est pas le seul endroit où un ancien comportement vit. Une base de données contient
des lignes écrites sous l'ancienne règle. Si tu changes la définition d'un état, d'un calcul
ou d'un format, les lignes déjà présentes ne se corrigent pas toutes seules.

```text
Analogie : ignorer les données déjà écrites, c'est changer la recette d'un plat au menu du
jour sans prévenir la salle que les assiettes déjà servies suivent encore l'ancienne recette,
et c'est modifier la route d'un bateau sans mettre à jour le journal de bord qui documente
les positions déjà enregistrées selon l'ancien cap.
Où l'analogie casse : une assiette servie ou une ligne de journal de bord ne se relit jamais
automatiquement par un programme. Une ligne en base, elle, peut être relue par le code que tu
viens de changer à tout moment, et produire un résultat incohérent avec les lignes créées
après ton patch, sans qu'aucune alerte ne se déclenche.
```

Avant de livrer, pose-toi systématiquement la question : mon changement invalide-t-il
l'interprétation de données déjà stockées ? Si oui, il faut soit une migration de données,
soit un code qui sait lire les deux formats en transition, jamais un silence sur ce point.

## Compromis

| Option                                                              | Coût                                                        | Bénéfice                                                             | Quand choisir                                              |
| -------------------------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------------------------- | -------------------------------------------------------------- |
| Corriger directement sans cartographier les appelants                | Rapide en apparence, risque élevé d'incident caché            | Aucun, sauf gain de temps immédiat                                       | Jamais sur du code partagé en production                       |
| Cartographier le rayon d'impact avant de coder                       | Temps d'enquête avant la première ligne de patch              | Élimine la classe d'incident la plus fréquente (appelant non identifié) | Systématique dès que le code est partagé par plus d'un appelant |
| Corriger et nettoyer en même temps ("pendant que j'y suis")          | Revue plus longue, diff illisible, risque mélangé au correctif | Impression de gain de temps                                              | Jamais pour un correctif urgent, réservé à une PR séparée       |
| Patch minimal, séparé de tout refactor                                | Discipline à tenir, parfois frustrant                        | Revue rapide, rayon d'impact limité, rollback trivial si besoin          | Par défaut sur toute modification de code étranger              |

## Pièges classiques

- **Tu corriges une fonction partagée sans savoir qu'elle est partagée.** Symptôme : un
  service sans lien apparent avec ton ticket casse quelques jours après ton déploiement.
- **Tu fais confiance à une suite de tests verte sans savoir ce qu'elle couvre vraiment.**
  Symptôme : les tests passent, la production casse quand même, sur un cas que personne
  n'avait jamais écrit en test.
- **Tu modifies l'assertion d'un test qui gênait ton patch, sans te demander pourquoi elle
  était là.** Symptôme : un comportement volontaire, protégé depuis des années, disparaît
  sans que personne ne s'en aperçoive avant un incident client.
- **Tu oublies les données déjà écrites sous l'ancien comportement.** Symptôme : un rapport
  ou un calcul redevient incohérent uniquement sur les enregistrements antérieurs à ton
  déploiement, ce qui rend le bug difficile à reproduire.
- **Tu mélanges correction et nettoyage dans le même patch.** Symptôme : la revue de code
  prend trois fois plus de temps que nécessaire, et un reviewer pressé approuve sans avoir
  vraiment isolé le changement de comportement réel.

## Ce que tu dois savoir défendre

- Pourquoi un test vert ne prouve jamais l'absence de régression, seulement l'absence de
  régression sur ce qu'il teste explicitement.
- Comment tu établis le rayon d'impact d'une modification avant d'écrire le patch, avec les
  outils concrets que tu utiliserais.
- Pourquoi séparer un correctif d'un nettoyage n'est pas une question de style, mais une
  question de rayon d'impact mesurable.
