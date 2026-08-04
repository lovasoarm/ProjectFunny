# Boss Fight : Le pivot à J-10

## La situation

Tu es à dix jours de la présentation finale de ton capstone. Le client fictif (ou ton
formateur, si tu es encadré) t'envoie ce message :

> "En fait on vient d'apprendre qu'une quatrième salle rejoint le réseau la semaine
> prochaine, dans une autre ville, avec un fuseau horaire différent de nos trois salles
> actuelles (elles sont à l'étranger). Est-ce que ça change beaucoup de choses pour vous ?
> On aimerait toujours livrer dans les délais si possible."

Ton modèle de données et ton architecture actuels ont été pensés avec l'hypothèse implicite
(jamais écrite noir sur blanc dans ton cadrage, tu le découvres maintenant) que toutes les
salles partagent le même fuseau horaire pour le calcul des créneaux.

## Ta mission

Rédige, comme un livrable réel :

1. Un diagnostic honnête de l'impact de ce changement sur ton architecture existante,
   distinguant ce qui casse réellement de ce qui semble casser mais ne casse pas.
2. Une proposition de traitement : soit un ajustement rapide compatible avec le délai, soit
   un report explicite avec justification, soit une renégociation du délai avec argument.
3. Une réponse au client, en langage client (pas en jargon technique), qui ne cache ni
   n'exagère la difficulté.

## Partie orale (obligatoire)

Une fois le texte écrit rendu, enregistre-toi en audio ou vidéo pendant 3 minutes maximum,
répondant à voix haute au client sur le pivot à J-10 comme si tu étais réellement en situation :
sans lire ton texte mot à mot, sans notes au-delà d'un post-it de mots-clés. Réécoute
l'enregistrement une seule fois et note toi-même, avant toute relecture externe, un point
où ta réponse orale était plus faible que ta réponse écrite.

## Grille d'évaluation

| Critère                                 | Points | Ce qui est vérifié                                                                                                                                          |
| --------------------------------------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Diagnostic technique correct            | 30     | Tu identifies précisément où le fuseau horaire est stocké ou implicite dans ton système (créneaux, capacité, notifications) et l'impact réel de son absence |
| Distinction panique vs impact réel      | 20     | Tu ne traites pas tout le système comme cassé alors que seule une partie l'est réellement                                                                   |
| Qualité de la proposition de traitement | 25     | La proposition est réaliste par rapport au délai restant, avec un compromis assumé (report, effort, ou délai)                                               |
| Communication client                    | 25     | La réponse au client est honnête, sans jargon, sans minimiser ni dramatiser, et propose une option concrète plutôt qu'un simple constat de problème         |
| Justesse à l'oral sous contrainte de temps, sans texte préparé lu mot à mot | 20     | La réponse orale tient la même logique que le texte écrit, sans relecture mot à mot, dans le temps imparti ; un résumé appauvri ou un dépassement des 3 minutes ne vaut pas le maximum |

Score minimal pour valider : 84/120.
