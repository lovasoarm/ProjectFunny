# Désaccords, escalade, dire non

## Le piège

L'équipe qui construit le système de créneaux du cabinet vétérinaire est en réunion de
préparation d'une release. Farida propose de stocker les créneaux comme des intervalles
horaires ouverts, plus flexibles pour gérer les urgences insérées entre deux rendez-vous.
Tom pense que c'est une erreur : sans créneaux fixes, calculer la charge d'une journée
devient un calcul complexe à chaque affichage plutôt qu'une simple lecture. La discussion
monte en dix minutes. Farida sent que Tom balaie son idée sans l'avoir vraiment écoutée.
Tom sent que Farida ignore le coût de calcul qu'il vient de chiffrer. Personne ne cède,
personne n'argumente plus, la réunion se termine sur "on verra plus tard" : ce qui veut
dire, en pratique, que Tom a gagné par défaut parce que c'est lui qui code la partie
concernée. Trois semaines plus tard, Farida découvre le choix déjà fait dans le code, sans
avoir été reconsultée, et arrête de proposer des idées d'architecture en réunion.

## Ce qui se passe vraiment

Un désaccord technique non résolu ne disparaît jamais : soit il se résout explicitement par
un raisonnement partagé, soit il se résout implicitement par un rapport de force (qui code,
qui a le plus d'ancienneté, qui parle le plus fort) : et la deuxième option laisse une
trace de ressentiment qui s'accumule, invisible, jusqu'à ce qu'une personne compétente
arrête de contribuer par lassitude. La compétence de "gérer un désaccord" n'est pas une
compétence sociale accessoire au travail technique : c'est ce qui décide si les bonnes
idées de l'équipe remontent effectivement à la surface ou restent dans la tête de ceux qui
ont arrêté de les partager.

### Désamorcer un désaccord technique

Un désaccord technique productif suit une structure reconnaissable, différente d'un
désaccord qui dégénère :

```text
Désaccord qui dégénère                    Désaccord productif

"Je pense que ça devrait être X"          "Je propose X, parce que [raison
       │                                  concrète]. Qu'est-ce qui te fait
"Non, c'est une mauvaise idée"            pencher vers Y ?"
       │                                         │
Chacun répète sa position plus fort       "Y, parce que [raison concrète].
       │                                  Est-ce que ton option X tient compte
Один cède par lassitude ou la              de [contrainte que je vois] ?"
décision se prend par défaut                     │
       │                                  Identification du vrai point de
Le perdant garde le ressentiment,          désaccord (souvent une hypothèse
ne le dit pas, se retire des               non partagée, pas les options
prochaines discussions                     elles-mêmes)
                                                  │
                                           Décision prise sur le critère
                                           identifié, ou test rapide si
                                           l'incertitude le permet
```

La technique la plus efficace pour sortir un désaccord de la spirale de répétition : au
lieu de défendre sa position, nommer explicitement la contrainte ou l'hypothèse sur laquelle
elle repose, et demander à l'autre la sienne. Dans l'exemple du cabinet vétérinaire, le vrai
désaccord n'est pas "intervalles ouverts contre créneaux fixes", c'est "à quelle fréquence
une urgence s'insère-t-elle réellement, et quel est le coût de calcul si on doit le vérifier
à chaque affichage ?" : une question vérifiable, pas une opinion.

```text
Question à se poser avant de répondre à un désaccord :
"Est-ce qu'on discute d'une préférence, ou d'un fait qu'on peut vérifier ?"

  Si c'est un fait vérifiable         → proposer de le vérifier avant de trancher
  Si c'est une vraie question de goût → nommer le critère qui doit trancher
                                         (coût, délai, qui maintient le code)
```

Quand le désaccord persiste après avoir identifié le vrai point de friction, la question
suivante n'est pas "qui a raison" mais "qui a le dernier mot sur ce type de décision" : la
réponse doit déjà exister dans le working agreement (leçon 02). Si elle n'existe pas encore,
c'est le signal qu'il faut l'ajouter.

### Dire non sans bloquer la relation

Dire oui à une demande intenable pour éviter un conflit immédiat déplace le conflit dans le
temps et l'aggrave : la personne qui a dit oui livre en retard, ou livre quelque chose de
dégradé, et le désaccord initial revient sous une forme plus coûteuse : la confiance en
moins. Un refus utile n'est pas un refus sec. Il a trois composantes :

```text
1. Reconnaître le besoin réel derrière la demande
   ("je comprends que le conseil syndical veut une date pour être rassuré")

2. Expliquer le mécanisme du coût si on dit oui quand même
   ("si je donne une date maintenant, sans avoir vérifié les tantièmes,
    le risque n'est pas un simple retard : c'est un mauvais montant facturé
    à des copropriétaires réels")

3. Proposer une alternative concrète qui répond au besoin réel autrement
   ("je peux te donner d'ici trois jours une date fiable, et en attendant
    voici ce que tu peux dire au conseil syndical")
```

Un refus qui s'arrête à l'étape 2 ("je ne peux pas, c'est trop risqué") sonne comme un
blocage. Un refus qui inclut l'étape 3 montre qu'on a pris au sérieux le besoin de la
personne en face, pas seulement sa demande littérale.

```text
Refus qui bloque la relation              Refus qui la préserve

"Non, c'est impossible."                  "Je comprends pourquoi tu veux ça
                                           maintenant. Voici pourquoi le faire
"On verra."                               tout de suite coûterait plus cher que
                                           d'attendre. Voici ce que je peux te
"Ce n'est pas mon problème."              donner à la place, et quand."
```

### Escalader au bon moment, au bon niveau

Escalader un problème, c'est le remonter à quelqu'un qui a le pouvoir de le résoudre quand
on ne l'a pas soi-même. Deux erreurs symétriques :

- **Escalader trop tard** : le problème est signalé seulement une fois devenu une crise
  visible, alors qu'un signal existait des jours avant. Le symptôme observable : la personne
  qui reçoit l'escalade dit "pourquoi je l'apprends seulement maintenant ?".
- **Escalader trop tôt ou trop souvent** : chaque friction mineure remonte à la hiérarchie
  sans avoir été tentée à résoudre au niveau où elle est apparue. Le symptôme observable :
  la personne qui reçoit l'escalade arrête de la traiter comme prioritaire, parce qu'elle
  en reçoit trop et ne peut plus distinguer l'urgent du mineur.

```text
Quand escalader : arbre de décision

Le problème peut-il être résolu par les personnes
directement impliquées, avec l'information dont elles
disposent ?
        │
   ┌────┴────┐
  oui        non
   │          │
Résoudre    Le délai pour attendre une décision plus haute
localement  coûte-t-il plus cher que le coût de déranger
            quelqu'un au-dessus ?
                    │
              ┌─────┴─────┐
             oui          non
              │            │
         Escalader     Documenter le problème,
         maintenant,   fixer une date limite propre
         avec les      avant escalade automatique
         faits, pas
         les émotions
```

Une escalade efficace apporte des faits vérifiables et une proposition, pas seulement un
signalement de détresse : "voici ce qui bloque, voici ce que j'ai déjà essayé, voici ce dont
j'ai besoin de toi pour débloquer" : jamais "je n'y arrive pas, débrouille-toi."

## Compromis

| Option                                                         | Coût                                                        | Bénéfice                                                        | Quand choisir                                                                       |
| -------------------------------------------------------------- | ----------------------------------------------------------- | --------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| Éviter le désaccord, dire oui pour préserver la paix immédiate | Aucun coût visible tout de suite                            | Aucun réel, le conflit revient plus tard et plus cher           | Jamais comme stratégie répétée                                                      |
| Nommer l'hypothèse en jeu et proposer de la vérifier           | Demande de ralentir la discussion, d'admettre l'incertitude | Le désaccord se résout sur des faits, la relation reste intacte | Chaque fois qu'un désaccord technique repose sur une hypothèse vérifiable           |
| Escalader chaque friction immédiatement                        | Rapide à faire soi-même                                     | Épuise la crédibilité des escalades suivantes                   | Jamais, sauf urgence réelle (sécurité, donnée perdue, incident en production)       |
| Escalader avec faits et proposition, au bon moment             | Demande de préparer l'escalade avant de la faire            | Résolution rapide, crédibilité préservée pour la prochaine fois | Dès que le problème dépasse le pouvoir de décision local et que le délai coûte cher |

## Pièges classiques

- Confondre "être d'accord" et "avoir compris" : le symptôme est qu'une personne cède en
  réunion puis revient sur la décision en privé, parce qu'elle n'a jamais été vraiment
  convaincue, seulement fatiguée de discuter.
- Dire oui à une demande intenable pour éviter un conflit immédiat : le symptôme est une
  livraison en retard ou dégradée, et un conflit plus dur au moment de la livraison.
- Escalader en accusant une personne plutôt qu'en décrivant un fait : le symptôme est que la
  personne qui reçoit l'escalade doit d'abord gérer la tension avant de pouvoir traiter le
  problème.
- Laisser un désaccord non résolu se transformer en décision par défaut (celui qui code
  décide) : le symptôme est qu'une personne cesse de proposer des idées après avoir vu les
  siennes ignorées sans discussion réelle.
- Ne jamais réviser une décision prise dans le désaccord, même quand les faits changent —
  le symptôme est une règle appliquée par habitude longtemps après que sa justification a
  disparu.

## Ce que tu dois savoir défendre

1. Explique pourquoi un désaccord technique non résolu explicitement ne disparaît pas, mais
   se résout quand même par un mécanisme implicite : lequel, et à quel coût.
2. Donne la structure en trois parties d'un refus qui préserve la relation, avec un exemple
   concret différent de celui du cours.
3. Explique la différence entre escalader trop tôt et escalader trop tard, et le symptôme
   observable qui permet de repérer chacune des deux erreurs après coup.
