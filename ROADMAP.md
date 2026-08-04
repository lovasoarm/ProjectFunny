# ROADMAP : trois rythmes

Choisis un rythme et tiens-le. Un parcours irrégulier coûte plus cher qu'un parcours lent :
tu paies un droit d'entrée à chaque reprise.

## Rythme SPRINT : 8 semaines, 12 h/semaine

Pour quelqu'un entre deux postes ou en fin d'études.

```text
S1  00 PROLOGUE + 01 MINDSET            -> note de conception
S2  02 PROBLEM-HUNT                     -> cahier de problème
S3  03 MVP-SPLIT + 04 USER-WIZARD       -> backlog tranché + flux
S4  05 DATA-SPELLS                      -> schéma + migrations
S5  06 ARCHI-LAB + 07 API-DOJO          -> ADR + spec d'API
S6  08 ROADMAP-RUN + 09 QUALITY-SHIELD  -> roadmap risque + tests
S7  10 TEAM-QUEST + 11 BIG-APP-SNOOP    -> rapport d'exploration
S8  12 CAPSTONE-ARENA                   -> projet livré
```

14 et 15 se lisent en fil rouge, quand le besoin apparaît. 13 démarre le jour où tu finis 12.

## Rythme SAISON : 4 mois, 6 h/semaine

Un niveau tous les 7 à 10 jours. Le capstone occupe le dernier mois entier.
C'est le rythme recommandé si tu travailles à côté : la digestion compte autant que la lecture.

## Rythme MARATHON : 6 mois, 4 h/semaine

Une séance de 2 h pour lire et réfléchir, une séance de 2 h pour produire le livrable.
Ne lis jamais deux leçons sans produire quelque chose entre les deux.

## Jalons de contrôle

À la fin de chaque bloc, tu dois pouvoir tenir 10 minutes à l'oral devant quelqu'un :

```text
Bloc CADRAGE   (00-03) : "voici le problème, voici ce que je ne fais pas, voici ma tranche 1"
Bloc BUILD     (04-07) : "voici mes frontières, mon schéma, mon contrat, et pourquoi"
Bloc CONDUITE  (08-10) : "voici mes risques, ma stratégie de test, mon flux d'équipe"
Bloc EPREUVE   (11-12) : "voici le projet, ses limites connues, et ce que je changerais"
```

Si tu n'y arrives pas, ne passe pas au bloc suivant. Refais le boss-fight du dernier niveau, en
suivant la section `## Si tu échoues` de son fichier `boss-fight.md` : chaque boss-fight porte
désormais son propre protocole de reprise, au niveau du fichier et pas seulement du bloc.

## Rétrospectives de bloc (obligatoires)

À la fin de chaque bloc, avant d'ouvrir le suivant, tu rouvres tes livrables passés et tu écris
ce qui est devenu faux. C'est le seul moment du parcours où tu rejoues ton propre travail.

| Fin de bloc | Rétrospective à faire | Durée |
| --- | --- | --- |
| après le niveau 03 | [RETRO-BLOC-1-CADRAGE.md](RETRO-BLOC-1-CADRAGE.md) | 60 à 90 min |
| après le niveau 07 | [RETRO-BLOC-2-BUILD.md](RETRO-BLOC-2-BUILD.md) | 60 à 90 min |
| après le niveau 10 | [RETRO-BLOC-3-CONDUITE.md](RETRO-BLOC-3-CONDUITE.md) | 60 à 90 min |
| après le niveau 12 | [RETRO-BLOC-4-EPREUVE.md](RETRO-BLOC-4-EPREUVE.md) | 60 à 90 min |

## Que faire quand tu rates un boss-fight

```text
echec 1 --> relis les lecons citees dans la grille, refais le challenge,
            attends 48 h, retente avec la variante proposee
echec 2 --> retourne au niveau amont nomme dans le boss-fight,
            refais son boss-fight, puis reviens
echec 3 --> reduis le perimetre de ton projet fil rouge : le blocage vient
            presque toujours d'un projet trop ambitieux
```

Un échec de boss-fight n'est pas un verdict sur toi. C'est un signal de position : il te dit
quel niveau amont n'a pas pris.

## Règle d'abandon saine

Si un niveau te bloque plus de deux semaines, réduis le périmètre de ton projet fil rouge plutôt
que d'abandonner le niveau. Le blocage vient presque toujours d'un projet trop ambitieux, pas d'un
manque de capacité.
