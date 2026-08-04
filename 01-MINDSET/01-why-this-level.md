# Pourquoi ce niveau existe

## La scène

Sofia reprend le backend de gestion des créneaux d'un cabinet vétérinaire. Trois praticiens,
des créneaux qui se chevauchent selon la spécialité (chirurgie vs consultation), des
annulations tardives. On lui demande d'ajouter "la possibilité de bloquer un créneau pour
urgence sans notification client". Elle code la feature en une matinée. Elle casse en
silence l'invariant qui garantissait qu'un créneau bloqué libère toujours son historique de
facturation. Personne ne l'a vu venir parce que personne n'avait de modèle explicite de ce
qui, dans ce système, ne devait jamais changer.

Trois semaines plus tard, même cabinet : un stagiaire ajoute un bouton "dupliquer la semaine"
pour gagner du temps à l'accueil. Personne n'a écrit ce que "dupliquer" doit faire des
rendez-vous déjà annulés dans la semaine copiée. Le stagiaire décide seul, sur l'instant, de
les dupliquer aussi. Résultat : 14 rendez-vous fantômes recréés le 2026-01-12, découverts
seulement quand deux clients reçoivent un rappel SMS pour un rendez-vous qu'ils avaient annulé
un mois plus tôt. Coût direct : 6 heures de nettoyage manuel en base et deux excuses par
téléphone à des clients agacés.

## Pourquoi ce niveau existe

Ce niveau existe parce qu'un développeur compétent en syntaxe peut quand même produire des
dégâts si personne ne lui a appris à rendre ses décisions explicites avant de coder. La
compétence technique ne protège pas contre une hypothèse jamais écrite. Ce niveau installe
le réflexe qui manque : nommer ce qu'on suppose, chiffrer ce qu'on choisit, avant d'écrire la
première ligne de code qui compte.

## Ce qui casse concrètement sans lui

Incident 1, cabinet vétérinaire, 2025-11-04 : la feature "bloquer un créneau pour urgence"
livrée en une matinée casse l'invariant de facturation. Le bug reste invisible 11 jours,
jusqu'à ce que la comptable remarque que 9 créneaux bloqués n'apparaissent nulle part dans le
relevé mensuel. Temps de diagnostic et correction : 3 jours-personne, plus une reprise
manuelle de 9 dossiers de facturation.

Incident 2, même cabinet, 2026-01-12 : le bouton "dupliquer la semaine" recrée 14 rendez-vous
annulés. Deux clients rappelés pour un rendez-vous fantôme, un troisième arrive physiquement
au cabinet pour un animal qui n'a pas de rendez-vous réel. Coût : 6 heures de nettoyage, une
crédibilité entamée auprès de la praticienne qui a dû gérer l'appel du client mécontent.

## Qui souffre en premier

Le premier à souffrir n'est jamais le développeur. C'est la personne la plus proche du
terrain : l'accueil qui gère l'appel du client furieux, la comptable qui découvre l'écart de
facturation trois semaines après coup. Le développeur, lui, ne voit le problème que lorsqu'on
le lui remonte, souvent trop tard pour corriger vite et proprement.

## À quel moment du projet ça se manifeste

Ce risque est maximal à deux moments précis : juste après une demande formulée "vite fait"
par un client non technique (la feature semble petite, donc négligeable), et juste après
qu'une première version a "bien marché" (la confiance remplace la vérification). Un projet
tout neuf, encore sur données de test, ne révèle presque jamais ce problème : c'est ce qui le
rend dangereux, il se cache jusqu'à la production réelle.

## Ce que tu sais faire à la sortie

- Modéliser un système en frontières et états explicites, sur un cas réel, pas sur un schéma
  abstrait.
- Chiffrer le coût d'un choix technique avant de le prendre, avec des ordres de grandeur, pas
  des impressions.
- Formuler une incertitude comme une hypothèse à tester, plutôt que la traiter comme une
  vérité ou l'ignorer.
- Écrire une note de décision courte qui force la clarté avant l'implémentation.
- Repérer, dans une demande de feature, l'invariant caché qu'elle risque de casser.

## Ce que ce niveau ne couvre pas

Ce niveau ne couvre pas la découverte du besoin réel derrière une demande : c'est le rôle du
[niveau 02](../02-PROBLEM-HUNT/README.md). Il ne couvre pas non plus le découpage d'un projet
en tranches livrables, traité au [niveau 03](../03-MVP-SPLIT/README.md), ni la modélisation
fine des données, traitée au [niveau 05](../05-DATA-SPELLS/README.md).

## Prérequis réels

Aucun prérequis technique. Le seul prérequis réel est d'avoir déjà livré, une fois dans sa
vie, quelque chose qui a cassé sans qu'on comprenne pourquoi sur le moment. Si ce n'est jamais
arrivé, ce niveau demandera un effort d'imagination supplémentaire, pas moins de rigueur.

## Erreurs de débutant les plus coûteuses

- Confondre "je n'ai pas eu de bug pendant le développement" avec "ce choix est sûr" : le
  bug d'invariant caché n'apparaît presque jamais sur les données de test.
- Traiter une petite feature comme automatiquement à faible risque, alors que le risque
  dépend de ce qu'elle touche, pas de sa taille apparente.
- Écrire du code avant d'avoir formulé, même en une phrase, ce que ce code ne doit jamais
  faire.
- Considérer "coder vite" et "penser avant de coder" comme opposés, alors que la vitesse
  n'est un problème que lorsqu'elle remplace la clarification, pas quand elle la suit.

## Le mécanisme sous-jacent

Le mécanisme n'est pas "réfléchir plus". C'est rendre visible, avant l'écriture du code, la
liste des choses qui ne doivent jamais changer (les invariants) et celle des choses qu'on
suppose vraies sans preuve (les hypothèses). Une fois cette liste écrite, même en trois
lignes, un développeur qui ajoute une feature peut se poser une question vérifiable : "est-ce
que ce que je fais touche à un invariant de cette liste ?" Sans liste explicite, cette
question ne se pose jamais, parce qu'elle suppose une mémoire parfaite du système entier, ce
qu'aucun cerveau humain ne possède au-delà de quelques semaines de travail sur un projet.

```text
   sans mindset                          avec mindset
   [ intuition ] --> code                [ hypothese ] --> experience
                                            chiffree        ciblee
          |                                     |
          v                                     v
   regression invisible                  decision defendable
   decouverte en prod                    et reversible a cout connu
```

## Contre-exemple : quand appliquer ce niveau serait une erreur

Un prototype jetable, montré une seule fois en réunion pour valider une direction visuelle,
n'a pas besoin d'une note de décision ni d'une liste d'invariants. Passer une heure à formaliser
les hypothèses d'un code qui sera supprimé dans la semaine est un coût sans bénéfice. Le
niveau s'applique à ce qui va vivre et évoluer, pas à ce qui est explicitement jetable et
annoncé comme tel.

## Le coût de l'apprentissage

Compter environ 4 à 6 heures de lecture attentive pour les quatre leçons, plus 3 à 5 heures
de challenge appliqué sur un cas concret. Le vrai coût n'est pas dans le temps de lecture : il
est dans le changement d'habitude, qui prend plusieurs semaines de pratique avant de devenir
un réflexe et non un effort conscient supplémentaire à chaque feature.

## Comment savoir que tu maîtrises

Le signal observable : avant de coder une feature qui touche à des données existantes, tu
écris spontanément, sans qu'on te le demande, une ou deux phrases sur ce qui ne doit jamais
changer. Si tu dois y penser consciemment et te forcer, tu es en cours d'apprentissage. Si tu
codes une feature "petite" sans jamais te poser la question, tu n'as pas encore le réflexe.

## Ce que l'IA fait à ta place, et ce qu'elle ne fait pas

Un assistant IA peut générer du code pour une feature en quelques secondes, avec des tests,
un style propre, une syntaxe correcte. Ce qu'il ne fait pas : deviner quels invariants du
système existant sont fragiles, parce qu'il n'a pas le contexte de la matinée de Sofia ni des
14 rendez-vous fantômes. L'IA répond à la question posée ; elle ne pose pas la question "est-ce
que ceci casse quelque chose d'invisible ?" à ta place. C'est ton rôle, avant et après avoir
demandé du code à une IA.

## Comment ce niveau est réutilisé plus tard

Le réflexe de rendre les invariants explicites sert directement à modéliser un domaine au
[niveau 05](../05-DATA-SPELLS/README.md), à choisir une architecture qui limite le rayon de
casse au [niveau 06](../06-ARCHI-LAB/README.md), et à écrire un contrat d'API qui n'autorise
pas de comportement ambigu au [niveau 07](../07-API-DOJO/README.md). Le chiffrage de coût de
décision revient tel quel dans l'estimation honnête du [niveau 03](../03-MVP-SPLIT/README.md).

## Ce que tu dois savoir défendre

1. Donne un exemple où une feature "petite" a cassé un invariant caché faute de modèle
   explicite du système.
2. Pourquoi "coder vite" n'est pas en contradiction avec ce niveau : précise à quel moment
   la vitesse redevient un problème.
3. Quel est le lien entre les quatre leçons de ce niveau : pourquoi ne peux-tu pas en sauter
   une.
