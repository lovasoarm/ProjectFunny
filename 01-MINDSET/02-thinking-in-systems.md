# Penser en systèmes

## Le piège

Le système de tournées de livraison d'une PME de messagerie doit "juste" ajouter un champ
`position_gps_du_chauffeur`. Le développeur l'ajoute à la table `chauffeur`. Deux semaines
plus tard, deux tournées lisent la même position au même instant et calculent des ETA
incohérents pour le client, parce que la position est mutée par un flux temps réel pendant
qu'un autre flux la lit pour un calcul de replanification. Le bug n'est pas dans le champ,
il est dans l'absence de modèle des entrées, sorties, état partagé et frontières du système.

## Ce qui se passe vraiment

Tout système logiciel se décrit avec quatre éléments, et la plupart des bugs de conception
viennent de l'un d'eux, mal identifié :

- **Entrées** : ce qui vient de l'extérieur et que le système ne contrôle pas (position GPS
  envoyée par l'app du chauffeur, requête HTTP d'un client).
- **Sorties** : ce que le système produit vers l'extérieur (notification SMS, réponse API).
- **État** : ce que le système retient entre deux appels (position actuelle, statut de
  tournée). L'état est la source de la plupart des bugs de concurrence, parce qu'il est
  partagé et mutable.
- **Effets de bord** : toute action qui modifie quelque chose en dehors du calcul courant
  (écrire en base, envoyer un email, appeler une API tierce). Un effet de bord ne peut pas
  être "annulé" par un simple retour arrière du programme.

```text
              FRONTIÈRE DU SYSTÈME
        ┌───────────────────────────────┐
Entrée →│                               │→ Sortie
(GPS)   │   ┌───────────┐               │  (ETA client)
        │   │   ÉTAT     │◄── lu/écrit ─┤
        │   │ (position, │   par plusieurs
        │   │  tournée)  │   flux concurrents
        │   └───────────┘               │
        │        │                      │
        │        ▼                      │
        │   EFFET DE BORD                │
        │   (notif SMS envoyée)          │
        └───────────────────────────────┘
```

Le vrai travail de modélisation consiste à répondre, pour chaque nouvelle feature, à trois
questions : quel état partagé est touché ? qui d'autre le lit ou l'écrit en même temps ? et
où est la frontière du système — jusqu'où va ma responsabilité, à partir d'où je fais
confiance à un tiers (une API externe, un autre service) ?

Dans l'exemple du chauffeur, la vraie question n'était pas "où stocker le champ" mais "cette
donnée doit-elle être une source de vérité unique et cohérente, ou une valeur qui peut être
légèrement en retard sans conséquence ?". Une position GPS pour affichage sur une carte peut
être en retard de quelques secondes. Une position GPS utilisée pour calculer un ETA facturé
au client ne le peut pas de la même façon — il faut décider explicitement, pas par défaut.

```sql
-- Modèle explicite : séparer l'état "affichage" (tolère l'incohérence)
-- de l'état "calcul métier" (doit être cohérent au moment du calcul)
CREATE TABLE position_affichage (
  chauffeur_id INT,
  lat FLOAT, lng FLOAT,
  maj_at TIMESTAMP  -- peut être vieux de quelques secondes, sans risque
);

CREATE TABLE position_verrouillee_pour_calcul (
  chauffeur_id INT,
  lat FLOAT, lng FLOAT,
  version INT  -- verrou optimiste : le calcul d'ETA doit relire cette version
);
```

## Compromis

| Option | Coût | Bénéfice | Quand choisir |
|---|---|---|---|
| Un seul état partagé pour tout usage | Simple à coder au départ | Risque de conditions de course dès que deux usages ont des exigences différentes | Prototype jetable, faible enjeu de cohérence |
| Séparer l'état par exigence de cohérence | Plus de code, plus de tables/structures | Élimine une classe entière de bugs de concurrence | Dès qu'un même état sert à la fois de l'affichage et du calcul métier facturé |

## Pièges classiques

- Ajouter un champ à une entité existante sans se demander qui d'autre la lit en concurrence
  — symptôme : bug de course qui n'apparaît qu'en charge réelle, jamais en local.
- Confondre "frontière du système" avec "frontière du code" — symptôme : faire confiance
  aveuglément à une API tierce parce qu'elle est "dans le même repo" logique.
- Ignorer les effets de bord d'une fonction qu'on pense "pure" — symptôme : un test qui passe
  en isolation mais casse en série parce qu'un effet de bord persiste entre deux tests.
- Traiter tout état comme devant être parfaitement cohérent — symptôme : sur-ingénierie de
  verrous et de transactions là où une incohérence de quelques secondes serait sans impact.

## Ce que tu dois savoir défendre

1. Pour un système que tu connais, identifie une donnée qui devrait être un état "tolérant à
   l'incohérence" et une autre qui doit être strictement cohérente — justifie la différence.
2. Explique pourquoi ajouter un champ à une table existante n'est jamais un acte neutre du
   point de vue du modèle en système.
3. Donne un exemple d'effet de bord qu'on oublie facilement de considérer comme tel.
