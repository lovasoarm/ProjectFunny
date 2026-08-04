# Grimoire : Niveau 02, Problem Hunt

Mémo à ouvrir en réunion de cadrage, quand la pression pousse à foncer vers une solution avant
d'avoir vérifié le problème. Sert à poser les bonnes questions en direct, pas à les réciter.

| Terme | Définition | Code | Analogies |
| --- | --- | --- | --- |
| Demande | Ce que la personne dit vouloir ("il nous faudrait un tableau de bord"). | `printf "demande_brute: %s\n" "tableau de bord" >> cadrage.md` | commande passée au comptoir sans détail / ordonnance lue sans interroger le patient |
| Besoin réel | Le problème sous-jacent que la demande tente de résoudre, souvent différent de la demande formulée. | `printf "besoin_reel: %s\n" "savoir qui est dispo en urgence" >> cadrage.md` | vraie douleur derrière la plainte du patient aux urgences / vrai manque derrière la commande client |
| Contrainte | Ce qui limite la solution (budget, réglementation, matériel existant, formation des utilisateurs). | `printf "contraintes:\n- budget: 5k\n- reglement: RGPD\n" >> cadrage.md` | matériel dispo en cuisine ce soir-là / matériel dispo au refuge avant l'ascension |
| Signal faible | Indice observable qu'un besoin existe déjà et se débrouille sans solution officielle (tableur fantôme, contournement manuel, ticket récurrent). | `grep -ril "tableur\|fichier partagé" tickets/*.md` | astuce de cuisinier pour compenser un four défaillant / bricolage radio en régie pour pallier un matériel absent |
| Job to be done | Ce que l'utilisateur essaie d'accomplir. Format : "Quand [situation], je veux [action], pour pouvoir [bénéfice réel]". | `printf "jtbd: Quand %s, je veux %s, pour %s\n" "urgence nuit" "trouver un vétérinaire dispo" "sauver l'animal"` | commande formulée par le besoin du client, pas par le nom du plat / itinéraire pensé par le sommet visé, pas par le sentier habituel |
| Persona utile vs décoratif | Utile : influence directement une décision de conception vérifiable. Décoratif : illustre un document sans jamais changer une décision. | `grep -c "persona" decisions/*.md` | fiche de poste réellement suivie en régie / fiche patient réellement consultée aux urgences |
| Non-objectif | Décision explicite de ne jamais faire quelque chose, avec sa raison, écrite avant que la pression n'arrive. | `printf "non_objectifs:\n- pas de paiement en ligne v1: risque conformité\n" >> cadrage.md` | plat volontairement retiré de la carte / voie volontairement écartée du plan de cordée |
| Métrique de vanité vs métrique de succès produit | Vanité : peut monter sans que le problème réel soit résolu. Succès produit : reliée directement au symptôme initial. | `printf "metrique_succes: temps_moyen_reponse_urgence < 5min\n"` | nombre de couverts servis vs clients réellement satisfaits / nombre de spectateurs vs qualité perçue du son en régie |
| Seuil d'échec | Chiffre fixé avant le lancement, en dessous duquel l'équipe reconnaît un échec et décide. | `printf "seuil_echec: adoption < 20%% au 2026-10-01 => pivot\n" >> cadrage.md` | seuil de demi-tour fixé avant le départ en montagne / seuil de rupture de stock avant le coup de feu |

## Défense orale

| Terme | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- |
| Demande | Tu construis exactement ce qui a été dit, sans jamais interroger le problème sous-jacent | Que ferait cette personne aujourd'hui si tu ne construisais rien ? |
| Besoin réel | Tu livres une solution qui ne change aucun comportement observable | Quelle friction concrète, vécue aujourd'hui, ce besoin décrit-il ? |
| Contrainte | Tu conçois une solution irréalisable ou inutilisable dans le contexte réel | Quelle contrainte du projet rend certaines solutions impossibles d'emblée ? |
| Signal faible | Tu cadres sur une intuition invérifiable au lieu d'une preuve terrain | Quel signal faible concret, pas hypothétique, appuie ce besoin ? |
| Job to be done | Tu conçois pour un profil de personne plutôt que pour une situation réelle qu'elle traverse | Peux-tu formuler ce besoin au format job to be done, sans nommer de persona ? |
| Persona utile vs décoratif | Le document se remplit de fiches jamais reconsultées, aucune décision n'en dépend | Quelle décision de conception cette fiche persona a-t-elle réellement changée ? |
| Non-objectif | Le périmètre s'étend sous pression, sans refus assumé, faute d'avoir été posé à l'avance | Si tu supprimais ce non-objectif, quelle décision de conception changerait ? |
| Métrique de vanité vs métrique de succès produit | Tu pilotes le projet sur un chiffre qui rassure sans jamais prouver que le problème est résolu | Cette métrique peut-elle monter si le vrai problème n'est pas résolu ? |
| Seuil d'échec | Le jugement du succès se fait après coup, ajusté pour coller au résultat obtenu | Dans six semaines, quel chiffre te ferait dire honnêtement que ça n'a pas marché ? |

Grille détaillée : voir [boss-fight.md](./boss-fight.md).

## Checklist avant de coder quoi que ce soit

- [ ] La demande initiale a été reformulée en besoin(s) réel(s), avec au moins deux
      interprétations envisagées et rejetées explicitement.
- [ ] Au moins un signal faible concret (pas hypothétique) appuie le besoin identifié.
- [ ] Les utilisateurs concernés sont décrits par leurs jobs to be done, pas par des
      personas décoratifs.
- [ ] Les non-objectifs sont écrits, chacun avec sa raison, et testés ("si on le supprime,
      une décision de conception change-t-elle ?").
- [ ] Une métrique de succès produit est définie, reliée au symptôme initial.
- [ ] Un seuil d'échec chiffré est fixé, avec une date d'évaluation.

## Questions à se poser en réunion de cadrage

- "Si cette fonctionnalité disparaissait, quel comportement observable des utilisateurs
  changerait ?"
- "Quel contournement les gens utilisent-ils aujourd'hui pour survivre sans cette solution ?"
- "Est-ce que cette métrique peut monter si le vrai problème n'est pas résolu ?"
- "Qu'est-ce qu'on refuse explicitement de faire, et pourquoi ?"
- "Dans six semaines, quel chiffre nous ferait dire honnêtement que ça n'a pas marché ?"

## Heuristique rapide : demande vs besoin

```text
Une demande commence souvent par une solution ("il nous faudrait une appli").
Un besoin réel se formule en termes de situation et de friction observable
("les secrétaires perdent cinq minutes par urgence à appeler les deux autres
cabinets pour savoir qui est disponible").

Règle pratique : si tu peux répondre à la demande sans savoir ce que la personne
fait concrètement aujourd'hui pour s'en sortir, tu n'as pas encore le besoin réel.
```

## Pièges à répéter à voix haute avant chaque cadrage

- Une demande formulée comme une solution cache presque toujours un besoin plus simple.
- Un persona qu'on ne recite jamais après le cadrage était décoratif.
- Un non-objectif qui ne referme aucune porte concrète n'a servi à rien.
- Une métrique facile à obtenir dès le premier jour est presque toujours une métrique de
  vanité.
- Un seuil d'échec écrit après avoir vu les résultats n'est plus un seuil, c'est une
  justification.

## Si tu rates le boss-fight

Relis en premier le critère "usage du cadrage comme outil, pas comme dogme" : c'est celui qui
plafonne la note. Rejoue la scène en écrivant d'abord, à la main, ce que le directeur a de
légitime dans sa demande avant d'écrire ta réponse. Relis ensuite la section "Pièges à répéter
à voix haute" ci-dessus. Attends 48 h avant de retenter le boss-fight : la réaction à chaud
ressemble toujours trop à la première version. Si le deuxième essai échoue sur le même critère,
redescends au niveau 01 relire "asymétrie des erreurs" : le blocage est souvent là, pas ici.
