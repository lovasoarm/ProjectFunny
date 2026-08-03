# Grimoire : Problem Hunt

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| Demande | Ce que la personne dit vouloir ("il nous faudrait un tableau de bord") | Tu construis exactement ce qui a été dit, sans jamais interroger le problème sous-jacent | Que ferait cette personne aujourd'hui si tu ne construisais rien ? |
| Besoin réel | Le problème sous-jacent que la demande tente de résoudre, souvent différent de la demande formulée | Tu livres une solution qui ne change aucun comportement observable | Quelle friction concrète, vécue aujourd'hui, ce besoin décrit-il ? |
| Contrainte | Ce qui limite la solution (budget, réglementation, matériel existant, formation des utilisateurs) | Tu conçois une solution irréalisable ou inutilisable dans le contexte réel | Quelle contrainte du projet rend certaines solutions impossibles d'emblée ? |
| Signal faible | Indice observable qu'un besoin existe déjà et se débrouille sans solution officielle (tableur fantôme, contournement manuel, ticket récurrent, groupe de messagerie parallèle) | Tu cadres sur une intuition invérifiable au lieu d'une preuve terrain | Quel signal faible concret, pas hypothétique, appuie ce besoin ? |
| Job to be done | Ce que l'utilisateur essaie d'accomplir, indépendamment de qui il est. Format : "Quand [situation], je veux [action], pour pouvoir [bénéfice réel]" | Tu conçois pour un profil de personne plutôt que pour une situation réelle qu'elle traverse | Peux-tu formuler ce besoin au format job to be done, sans nommer de persona ? |
| Persona utile vs décoratif | Utile : influence directement une décision de conception vérifiable. Décoratif : illustre un document sans jamais changer une décision | Le document se remplit de fiches jamais reconsultées, aucune décision n'en dépend | Quelle décision de conception cette fiche persona a-t-elle réellement changée ? |
| Non-objectif | Décision explicite de ne jamais faire quelque chose, avec sa raison (risque, coût, complexité), écrite avant que la pression n'arrive | Le périmètre s'étend sous pression, sans refus assumé, faute d'avoir été posé à l'avance | Si tu supprimais ce non-objectif, quelle décision de conception changerait ? |
| Métrique de vanité vs métrique de succès produit | Vanité : peut monter sans que le problème réel soit résolu. Succès produit : reliée directement au symptôme initial, ne peut pas monter artificiellement | Tu pilotes le projet sur un chiffre qui rassure sans jamais prouver que le problème est résolu | Cette métrique peut-elle monter si le vrai problème n'est pas résolu ? |
| Seuil d'échec | Chiffre fixé avant le lancement, en dessous duquel l'équipe s'engage à reconnaître un échec et à décider (pivoter, arrêter, réitérer) | Le jugement du succès se fait après coup, ajusté pour coller au résultat obtenu | Dans six semaines, quel chiffre te ferait dire honnêtement que ça n'a pas marché ? |

## Comportements évalués en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'échec |
| --- | --- | --- |
| Respect du besoin de l'interlocuteur | La réponse reconnaît l'enjeu business réel du directeur | La réponse le traite comme une distraction sans valeur |
| Usage du cadrage comme outil, pas comme dogme (compromis nommé et assumé) | Le cadrage sert à éclairer la décision, pas à la bloquer par principe | La réponse se limite à "on a déjà décidé, on ne change pas" |
| Ancrage dans le réel (justification par un mécanisme) | Au moins un signal faible ou une donnée concrète est cité | La réponse reste abstraite, sans preuve terrain |
| Proposition constructive | Une troisième voie est proposée, avec un chemin concret | La réponse est un simple oui ou un simple non |
| Tenue de la relation (honnêteté sans mépris ni capitulation) | Le ton reste collaboratif, pas défensif ni condescendant | Le ton laisse penser que le directeur a tort d'avoir une opinion |

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
