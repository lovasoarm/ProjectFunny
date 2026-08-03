# Boss Fight — La réunion de cadrage dans deux heures

## La situation

Tu viens d'être affecté à l'équipe qui maintient le logiciel de gestion d'un cabinet
vétérinaire multi-sites (plannings, dossiers animaux, facturation, stock de médicaments).
Le dépôt fait 90 000 lignes, six ans d'historique, deux développeurs sont partis sans
documentation laissée. Ton manager t'écrit ce message à 9h :

> "Le client veut savoir si on peut ajouter la possibilité de bloquer automatiquement un
> créneau si le vétérinaire n'a pas encore validé le dossier médical de l'animal précédent.
> Réunion de cadrage avec le client à 11h. J'ai besoin de ton avis technique avant, même
> partiel. Tu es le seul dispo, les deux autres sont en congés."

Tu n'as jamais ouvert ce dépôt avant ce matin. Tu as deux heures, pas trois. Le client
attend une réponse crédible, pas un "je ne sais pas encore".

## Les contraintes qui pèsent sur toi

- Budget de temps divisé par 1,5 par rapport à ta méthode habituelle.
- Aucun ancien de l'équipe disponible pour répondre à tes questions.
- Le domaine (dossiers médicaux vétérinaires) a des implications légales que tu ne connais
  pas encore (traçabilité, responsabilité en cas d'erreur de dossier).
- Tu dois produire quelque chose d'utilisable en réunion, pas juste pour toi.

## Ta mission

Rédige, comme si tu la vivais réellement, la façon dont tu répartirais ces deux heures et le
message que tu enverrais à ton manager avant la réunion de 11h. Sois concret : quelles
commandes tu lancerais, quels fichiers tu chercherais en premier, ce que tu accepterais de
dire "je ne sais pas encore" et ce que tu refuserais d'affirmer sans preuve.

Ton message final à ton manager doit contenir :

- Ce que tu as pu vérifier avec certitude en deux heures.
- Ce qui reste une hypothèse non vérifiée, explicitement marquée comme telle.
- Un avis de faisabilité (pas un chiffrage précis) avec les risques identifiés.
- Ce que tu recommandes de dire au client en réunion sur les points encore incertains.

## Grille d'évaluation

| Critère | Points | Ce qui est vérifié |
|---|---|---|
| Répartition du temps réaliste et justifiée | 20 | Tu expliques pourquoi tu donnes plus de temps à telle phase qu'à telle autre vu le contexte (deadline courte, domaine légal sensible) |
| Distinction claire entre vérifié et supposé | 25 | Chaque affirmation de ton message est étiquetée : preuve trouvée, ou hypothèse à confirmer |
| Identification du risque légal/métier | 20 | Tu identifies que "dossier médical non validé" touche potentiellement une exigence de traçabilité, sans l'avoir confirmée, et tu le signales comme point à vérifier avant tout engagement de délai |
| Honnêteté de l'avis de faisabilité | 20 | Tu ne donnes pas un chiffre de délai précis sans base solide ; tu proposes une fourchette ou un délai d'investigation supplémentaire clairement justifié |
| Communication utilisable en réunion | 15 | Le message est écrit pour un manager pressé : court, structuré, actionnable, sans jargon d'enquête inutile |

Score minimal pour valider le niveau : 75/100, avec au moins 20/25 sur le critère de
distinction vérifié/supposé — c'est le cœur de la compétence testée par ce niveau.
