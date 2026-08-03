# Attaquer le risque le plus cher en premier

## Le piège

Une bibliothèque de club d'escalade veut numériser son catalogue et son système de prêt de
matériel (cordes, baudriers, chaussons). Le bénévole responsable du projet commence par
l'écran le plus simple : la liste des adhérents, formulaire classique, CRUD basique. Il le
finit en trois jours, se sent productif, enchaîne sur l'écran de catalogue. Six semaines
plus tard, il attaque enfin le vrai problème : une corde ne peut être prêtée que si elle n'a
pas dépassé son nombre de chutes maximum (une règle de sécurité, pas une fonctionnalité
secondaire), et cette donnée n'existe dans aucun système actuel : elle est notée à la main
sur des étiquettes physiques attachées aux cordes. Le projet entier dépend de la capacité à
digitaliser cette donnée, et personne ne l'a vérifié avant de coder six semaines d'écrans
annexes. Le bénévole a commencé par ce qui était facile, pas par ce qui était incertain.

## Ce qui se passe vraiment

Tout projet a un risque qui, s'il se réalise, coûte plus cher que tous les autres réunis —
souvent parce qu'il remet en cause une hypothèse sur laquelle tout le reste repose. Ce
risque a trois caractéristiques qui permettent de le repérer avant de planifier :

1. **Il est difficile à inverser** : si l'hypothèse est fausse, il faut défaire du travail
   déjà fait, pas juste continuer différemment.
2. **Il est mal connu** : personne dans l'équipe ne peut dire avec certitude "oui, ça marche"
   sans l'avoir testé en conditions réelles.
3. **Il conditionne le reste** : d'autres décisions (modèle de données, choix technique,
   organisation des écrans) dépendent de la réponse.

```text
Matrice risque : incertitude × coût si l'hypothèse est fausse

              coût faible si fausse        coût élevé si fausse
incertitude   ┌──────────────────────┬──────────────────────┐
faible        │ à faire, sans stress │ à vérifier, mais      │
              │ (ex: libellé d'écran)│ rapidement (ex: format│
              │                      │ d'export connu)       │
              ├──────────────────────┼──────────────────────┤
incertitude   │ à explorer vite,     │ RISQUE LE PLUS CHER   │
élevée        │ coût limité si faux  │ → à attaquer en       │
              │ (ex: libellé exact   │   PREMIER, avant tout │
              │  d'un futur écran)   │   le reste du plan    │
              └──────────────────────┴──────────────────────┘
```

Le risque le plus cher n'est presque jamais la tâche qui semble techniquement la plus
difficile. Dans l'exemple du club d'escalade, l'écran de catalogue est techniquement plus
complexe (recherche, filtres) que la règle de sécurité sur les cordes, qui tient en une
condition. Mais la règle de sécurité est le risque le plus cher, parce que la donnée dont
elle dépend n'existe peut-être pas dans un format exploitable : et si elle n'existe pas,
tout le projet change de forme (il faut peut-être numériser les étiquettes en premier, ou
changer le processus terrain avant le logiciel).

### Comment nommer le risque le plus cher

Une question suffit, posée honnêtement pour chaque hypothèse du projet :

> "Si cette hypothèse est fausse, qu'est-ce qu'on doit défaire, et est-ce qu'on le saurait
> avant la fin du projet ou seulement à la fin ?"

```text
Hypothèses du projet bibliothèque d'escalade :
1. "Les adhérents ont un email valide"           → faux coûte peu, facile à corriger
2. "Le catalogue tient dans un modèle simple"    → faux coûte moyen, refactor local
3. "L'historique de chutes par corde est         → faux coûte ÉNORME, remet en cause
    numérisable et fiable"                          le processus terrain entier
```

L'hypothèse 3 devient la première phase du plan, avant même d'écrire un écran. Pas en
"faisant confiance" au bénévole qui gère le matériel : en allant vérifier physiquement,
avec cinq cordes réelles, si l'information est exploitable telle quelle.

### Attaquer le risque, pas le documenter

Attaquer un risque ne veut pas dire écrire un document d'analyse de risque. Ça veut dire
produire, le plus vite possible et avec le moins d'effort possible, une preuve empirique qui
confirme ou infirme l'hypothèse.

```text
Documenter le risque                    Attaquer le risque
"On pense que les étiquettes            On prend 5 cordes réelles, on lit
de cordes contiennent le nombre         les étiquettes, on note ce qu'on
de chutes de façon fiable"              trouve vraiment : dans les deux
                                         premiers jours du projet
→ zéro information nouvelle             → la vérité, avant d'avoir investi
                                           six semaines dessus
```

Analogie : Analogie : planifier par les risques, c'est le tri des urgences hospitalières qui traite
d'abord ce qui peut tuer avant ce qui fait mal, et un capitaine en navigation maritime qui
étudie d'abord les zones de tempête avant de tracer sa route la plus courte.
Où l'analogie casse : un soignant ou un capitaine observent souvent directement la gravité ou
la météo. Un risque projet reste largement invisible tant qu'on n'a pas construit la partie
qui le révèle, on planifie contre une incertitude qu'aucun instrument ne mesure d'avance.

## Compromis

| Option                                                   | Coût                                                       | Bénéfice                                                        | Quand choisir                                                                |
| -------------------------------------------------------- | ---------------------------------------------------------- | --------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| Commencer par le facile (quick win)                      | Sensation de progrès rapide, motivant pour l'équipe        | Bon pour le moral en tout début de projet très long             | Uniquement en complément, jamais à la place de l'attaque du risque principal |
| Risk-first (le plus cher, le plus incertain, en premier) | Démarrage plus lent, moins "montrable" en première semaine | Si le projet doit échouer ou pivoter, ça arrive tôt et pas cher | Systématiquement, dès qu'une hypothèse difficile à inverser existe           |

## Pièges classiques

- Confondre "difficile techniquement" et "risqué" : le symptôme est un plan qui attaque en
  premier la fonctionnalité la plus complexe à coder au lieu de l'hypothèse la plus fragile.
- Repousser le risque parce qu'il fait peur ou parce que personne ne sait par où commencer
  : le symptôme est un planning où le risque principal apparaît toujours "en phase 3 ou 4".
- Analyser le risque sur papier au lieu de le tester empiriquement : le symptôme est un
  document d'analyse de risque très complet et zéro donnée réelle collectée.
- Traiter tous les risques comme égaux et les traiter dans l'ordre où ils viennent à
  l'esprit : le symptôme est un plan sans priorité visible entre les risques.

## Ce que tu dois savoir défendre

1. Donne un exemple où la tâche techniquement la plus complexe d'un projet n'est pas le
   risque le plus cher, et explique pourquoi.
2. Formule la question qui permet d'identifier le risque le plus cher d'un projet que tu
   connais, et applique-la à trois hypothèses concrètes de ce projet.
3. Pourquoi "attaquer" un risque veut dire produire une preuve empirique et pas écrire un
   document d'analyse : donne un exemple de preuve empirique rapide à produire.
