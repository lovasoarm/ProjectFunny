# Écrire pour penser

## Le piège

Une équipe qui gère la refacturation d'énergie d'un immeuble collectif (répartir la facture
globale entre les appartements selon leurs relevés) doit choisir comment recalculer les
factures quand un relevé arrive en retard, après que la facturation du mois a déjà été
envoyée. Deux développeurs en discutent quinze minutes devant un tableau blanc, tombent
d'accord à l'oral, et codent. Deux mois plus tard, un troisième développeur doit corriger un
bug de recalcul et ne comprend pas pourquoi certains recalculs déclenchent un email au
locataire et d'autres non. Il interroge les deux premiers, qui se souviennent différemment
de la décision d'origine : chacun a retenu la moitié du raisonnement, et les deux moitiés ne
sont pas compatibles.

## Ce qui se passe vraiment

Une décision qui n'existe que dans une conversation orale n'existe pas vraiment : elle
existe dans deux ou trois mémoires humaines imparfaites, qui divergent avec le temps. Écrire
une décision force à répondre à des questions qu'on esquive facilement à l'oral : quelles
options ont vraiment été considérées, pourquoi celle-là précisément, qu'est-ce qu'on accepte
de perdre. L'écriture n'est pas de la documentation après coup : c'est l'outil de pensée
lui-même. Beaucoup de décisions bancales à l'oral s'effondrent dès qu'on essaie de les
écrire clairement, parce que l'écriture révèle les trous du raisonnement.

```text
   DÉCISION ORALE                      DÉCISION ÉCRITE

   "on a discuté, on part             "voici le problème exact,
    sur le recalcul immédiat"          voici 3 options considérées,
                                        voici pourquoi B l'emporte,
   → vit dans 2-3 mémoires             voici ce qu'on perd en
     humaines, diverge avec            choisissant B plutôt que C"
     le temps
                                       → vit dans un fichier,
                                         relisable dans 2 ans,
                                         contestable point par point
```

### L'ADR : documenter une décision architecturale

Un **ADR** (Architecture Decision Record) est un document court qui capture une décision
technique significative, au moment où elle est prise, avec son contexte et ses alternatives
rejetées. Un ADR n'est pas un rapport exhaustif : c'est un instantané honnête d'un
raisonnement, y compris de ses limites.

Gabarit minimal d'un ADR :

```text
   # ADR-012 : Stratégie de recalcul des factures en cas de relevé tardif

   ## Statut
   Accepté (2024-03-11)

   ## Contexte
   Environ 4 % des relevés arrivent après l'envoi de la facturation mensuelle.
   Le système doit décider ce qui se passe pour la facture déjà envoyée.

   ## Options considérées
   A. Recalcul immédiat de la facture affectée, email de correction au locataire.
   B. Report de l'écart sur la facture du mois suivant, sans email immédiat.
   C. Blocage de l'envoi tant que tous les relevés ne sont pas arrivés.

   ## Décision
   Option B : report sur le mois suivant.

   ## Justification
   L'option A génère un email de correction dans 4 % des cas chaque mois, ce qui
   a été jugé anxiogène par le comité de copropriété lors du test pilote (retours
   qualitatifs négatifs). L'option C retarderait l'envoi de la facturation de
   3 à 5 jours en moyenne pour 100 % des foyers, un coût jugé trop élevé pour
   corriger un problème qui n'affecte que 4 % des cas.

   ## Conséquences
   - Un locataire peut voir un écart apparaître sur sa facture du mois suivant,
     sans lien évident avec le mois précédent : il faudra un libellé explicite
     ("régularisation relevé de février").
   - Si le taux de relevés tardifs dépasse 15 %, réévaluer : le report deviendrait
     visible et source de confusion à cette fréquence.
```

Deux sections font le travail réel : "Options considérées" prouve qu'on n'a pas pris la
première idée venue, et "Conséquences" force à nommer ce qu'on accepte de perdre : y compris
un seuil qui invaliderait la décision plus tard. Sans cette dernière section, un ADR devient
une justification a posteriori plutôt qu'un raisonnement honnête.

### La RFC : proposer avant de trancher

Une **RFC** (Request For Comments) se distingue de l'ADR par le moment où elle intervient :
elle est écrite _avant_ que la décision soit prise, pour solliciter la contradiction d'autres
personnes avant de s'engager. Une RFC est un brouillon assumé, pas une décision déguisée en
question rhétorique.

```text
   RFC                                  ADR

   écrite avant la décision             écrit au moment de la décision
   objectif : faire émerger             objectif : garder une trace
   les objections manquées                du raisonnement final
   contient des questions ouvertes      ne contient plus de questions
                                          ouvertes, seulement un choix
```

Une RFC mal écrite pose des questions fermées ("êtes-vous d'accord ?") qui n'invitent à rien.
Une RFC utile expose les points où l'auteur lui-même hésite encore, et nomme explicitement
qui doit répondre à quoi avant une date donnée.

### La note de conception : cadrer avant de designer

Une **note de conception** (design doc) précède souvent une RFC ou plusieurs ADR : elle pose
le problème, le contexte, les contraintes, et les critères de succès, avant même de proposer
des solutions. Elle répond à une question différente de l'ADR : pas "qu'est-ce qu'on choisit"
mais "qu'est-ce qu'on essaie réellement de résoudre, et comment saura-t-on qu'on a réussi".

```text
   NOTE DE CONCEPTION
   ├── Problème : qu'observe-t-on, pour qui, avec quel impact mesuré
   ├── Contraintes : ce qui est non négociable (délai, budget, compatibilité)
   ├── Non-objectifs : ce que cette note ne cherche pas à résoudre
   ├── Critères de succès : comment on saura, après coup, que c'était la bonne décision
   └── Options + recommandation (souvent développée ensuite en ADR séparé)
```

Le champ le plus souvent oublié, et le plus utile, est "Non-objectifs" : sans lui, chaque
relecteur ajoute son propre problème préféré à la liste, et la note de conception explose en
portée avant même d'avoir été validée.

### Défendre un choix à l'écrit

Écrire une décision ne suffit pas si elle ne résiste pas à la première objection sérieuse.
Un texte qui défend correctement un choix a trois propriétés :

1. Il nomme les options rejetées avec leurs mérites réels, pas des versions caricaturées
   faciles à battre.
2. Il indique la condition qui ferait revenir sur la décision : un choix qui ne peut jamais
   être remis en cause n'est pas défendu, il est figé.
3. Il sépare les faits (ce qui a été mesuré ou observé) des jugements (ce qui a été estimé
   ou préféré), pour qu'un désaccord porte sur le bon terrain.

```text
   FAIT                                  JUGEMENT
   "4 % des relevés arrivent             "un email de correction
    après la facturation"                 mensuel serait anxiogène"

   → vérifiable dans les logs            → une estimation raisonnable,
                                            mais contestable avec
                                            d'autres données (sondage,
                                            test utilisateur)
```

Confondre les deux est la source la plus fréquente de désaccords stériles : on discute d'un
jugement comme s'il était un fait, ou on rejette un fait parce qu'on n'aime pas la conclusion
qu'il implique.

## Compromis

| Option                                          | Coût                                                | Bénéfice                                                                        | Quand choisir                                                            |
| ----------------------------------------------- | --------------------------------------------------- | ------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| Écrire un ADR pour chaque décision structurante | Temps de rédaction, discipline d'équipe à maintenir | Traçabilité, onboarding plus rapide, désaccords futurs argumentés sur des faits | Décision difficile à défaire, ou qui affecte plusieurs personnes/équipes |
| Trancher à l'oral et avancer                    | Rapidité immédiate                                  | Pas de friction sur les décisions mineures et réversibles                       | Décision locale, réversible en quelques minutes, sans impact externe     |

## Pièges classiques

- Écrire un ADR après coup, uniquement pour justifier une décision déjà prise et non
  négociable : symptôme : la section "Options considérées" contient des options ridicules,
  jamais sérieusement envisagées.
- Confondre une note de conception avec une liste de tâches techniques : symptôme : la note
  saute directement à l'implémentation sans jamais nommer le problème ni le critère de
  succès.
- Poser des questions fermées dans une RFC : symptôme : personne ne répond, ou tout le monde
  répond "oui" sans avoir vraiment challengé le contenu.
- Omettre la condition de révision d'un ADR : symptôme : la décision devient un dogme
  qu'il est politiquement délicat de remettre en cause, même quand le contexte a changé.
- Mélanger faits et jugements dans le même paragraphe sans les distinguer : symptôme : les
  désaccords en réunion tournent en rond parce que personne ne sait sur quoi il discute.

## Ce que tu dois savoir défendre

1. Explique la différence d'objectif entre une RFC et un ADR, et donne un exemple de moment
   où utiliser l'un plutôt que l'autre serait une erreur.
2. Pourquoi la section "Conséquences" (avec sa condition de révision) est la partie la plus
   importante d'un ADR, plus que la section "Décision" elle-même.
3. Prends un jugement ("cette architecture est plus simple") et transforme-le soit en fait
   vérifiable, soit en jugement explicitement assumé comme tel.
