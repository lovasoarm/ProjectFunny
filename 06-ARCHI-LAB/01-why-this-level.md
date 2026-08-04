# Pourquoi ce niveau existe

## La scène

Le cabinet vétérinaire pour lequel tu as construit l'appli de gestion de créneaux a du succès.
Six mois après la mise en ligne, l'équipe demande une deuxième clinique, un système de
rappels SMS, et la possibilité pour les vétérinaires de voir leur planning depuis une appli
mobile. Ton code : un seul projet Express avec des routes qui parlent directement à la base
de données, un peu de logique métier éparpillée dans les contrôleurs et le reste dans des
triggers SQL : a très bien fonctionné jusqu'ici. Tu ouvres le fichier `routes/appointments.js`
pour ajouter la fonctionnalité de rappel SMS. Il fait 640 lignes. Il gère la création de
rendez-vous, la vérification de disponibilité, le calcul de prix, l'envoi d'email de
confirmation, et la mise à jour du stock de vaccins. Tu ne sais plus par où commencer sans
casser autre chose. Ton estimation initiale de "deux jours" devient une semaine, puis tu
découvres un bug en prod que personne n'avait vu venir parce que le calcul de disponibilité
et le calcul de prix partagent une variable qu'aucun des deux n'était censé modifier.

## Ce qui casse sans ce niveau

Sans compréhension de l'architecture, tu développes une intuition fausse : "le code marche,
donc il est bien structuré". Cette intuition tient tant que le projet est petit et que tu es
seul dessus. Elle s'effondre exactement au moment où le projet devient intéressant : quand il
faut le faire évoluer sous contrainte, avec plusieurs personnes, sans tout réécrire.

Trois symptômes précis apparaissent, toujours dans le même ordre :

1. **La peur de toucher au code.** Tu ajoutes une fonctionnalité en copiant-collant un bloc
   existant plutôt qu'en le réutilisant, parce que tu as peur de casser ce qui marche déjà en
   le modifiant. Le code grossit plus vite que les fonctionnalités qu'il porte.
2. **Les régressions fantômes.** Un changement dans un coin du code casse une fonctionnalité
   sans rapport apparent, parce que les deux étaient couplées par un détail d'implémentation
   que personne n'avait documenté ni même remarqué.
3. **Le mur de l'onboarding.** Une nouvelle personne rejoint l'équipe et met trois semaines à
   pouvoir livrer un changement sans supervision, parce que comprendre "comment ça marche"
   demande de lire tout le code plutôt qu'une carte des responsabilités.

Ces trois symptômes ont un point commun : ils ne viennent jamais d'un manque de compétence en
syntaxe. Ils viennent de dépendances mal placées : du code qui sait des choses qu'il ne
devrait pas savoir, et qui donc casse dès que ces choses changent.

## Ce que l'architecture n'est pas

Avant d'aller plus loin, une clarification qui évite un contresens répandu. L'architecture
logicielle n'est pas :

- un diagramme UML fait avant de coder, jamais mis à jour ensuite ;
- une liste de patterns à appliquer par principe ("j'utilise toujours le Repository Pattern") ;
- un sujet réservé aux "architectes", rôle qui n'écrit plus de code depuis dix ans ;
- une question de taille de fichier ("chaque fichier doit faire moins de 200 lignes").

L'architecture, c'est la réponse concrète à une seule question, posée en permanence pendant
que tu codes : **quand cette chose change, qu'est-ce que je suis obligé de changer avec ?**
Une bonne architecture minimise et rend explicite le rayon de l'explosion. Une mauvaise
architecture le maximise et le cache.

```text
Changement demandé : "ajouter un deuxième mode de paiement pour les cotisations du club"

Mauvaise architecture (couplage caché) :
  routes/cotisation.js ──> modifie directement
       ├── la table `adherents`
       ├── le calcul de tarif dégressif (dupliqué ici ET dans le rapport comptable)
       ├── l'email de confirmation (template inline)
       └── le webhook du prestataire de paiement (mélangé avec la logique métier)
  → toucher au paiement oblige à comprendre et risquer de casser 4 autres choses

Bonne architecture (couplage explicite et limité) :
  UseCase "ProcessCotisation" ──> dépend d'une interface PaymentGateway
       ├── implémentation prestataire actuel
       └── implémentation nouvelle (à ajouter, sans toucher au UseCase)
  → ajouter un mode de paiement = ajouter un fichier, pas modifier les autres
```

## Pourquoi c'est difficile à apprendre seul

Le problème d'architecture ne se voit pas sur un petit projet. Un CRUD de 500 lignes tient
debout avec n'importe quelle structure, bonne ou mauvaise : c'est justement ce qui rend
l'apprentissage piégeux : tu peux passer des années à coder des petits projets et ne jamais
rencontrer la douleur qui justifie ces leçons. Elle apparaît à partir d'un seuil : plusieurs
développeurs, plusieurs mois de vie du projet, ou plusieurs fonctionnalités qui se chevauchent
sur les mêmes données. Ce niveau simule ce seuil artificiellement, par les exemples et le
challenge, pour que tu construises l'intuition avant de la payer en vrai, sur un projet qui
compte.

## Ce que tu dois savoir défendre

- Pourquoi "le code marche" et "le code est bien architecturé" sont deux affirmations
  indépendantes.
- Donne un exemple concret (pas générique) où un couplage caché a transformé un changement
  d'un jour en changement d'une semaine.
- Pourquoi l'architecture ne se voit pas sur un petit projet, et pourquoi c'est dangereux.
