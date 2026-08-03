# Checklists : mise en prod, revue, incident, onboarding

## Le piège

Une mise en prod du système de tournées de livraison se fait un jeudi après-midi sans
checklist, "parce qu'on connaît la procédure par cœur". Le job de migration de base tourne,
mais personne n'a vérifié que le job précédent (nettoyage de vieilles tournées) était bien
terminé : les deux se chevauchent, la table est verrouillée en plein import, trois chauffeurs
reçoivent des tournées vides le vendredi matin. Rien de nouveau ne s'est passé : c'est un
oubli déjà commis deux fois auparavant, jamais transformé en vérification systématique.

## Ce qui se passe vraiment

Une checklist n'existe pas pour compenser un manque de compétence. Elle existe parce que la
mémoire humaine sous pression ou sous routine oublie systématiquement les mêmes étapes,
indépendamment du niveau d'expertise : c'est un phénomène documenté dans tous les métiers à
forte charge cognitive (aviation, chirurgie), pas une spécificité du développement logiciel.

```text
Sans checklist                          Avec checklist
──────────────────────────              ──────────────────────────
Chaque exécution redécouvre        →    Chaque exécution vérifie les mêmes
les mêmes pièges, au hasard             points, dans le même ordre, sans
                                         dépendre de la mémoire du moment
```

### Mise en prod

- [ ] Le changement a été testé sur un environnement représentatif du volume de production.
- [ ] Un plan de rollback existe et a été testé, pas seulement écrit.
- [ ] Aucun autre déploiement ou job planifié ne chevauche cette fenêtre.
- [ ] Les métriques à surveiller après déploiement sont identifiées à l'avance (pas
      découvertes après coup en cherchant quoi regarder).
- [ ] Une personne est explicitement responsable de surveiller les 30 minutes suivant le
      déploiement : pas "l'équipe" en général.
- [ ] Le déploiement a lieu à un horaire où une intervention rapide est possible en cas de
      problème (jamais un vendredi 17h sur un système critique).

### Revue de code

- [ ] Je peux résumer l'intention du changement en une phrase avant de lire le diff ligne
      par ligne.
- [ ] Les cas limites du domaine métier précis sont couverts, pas seulement le chemin
      heureux.
- [ ] Les tests ajoutés échoueraient sur l'ancien code et passent sur le nouveau : vérifié,
      pas supposé.
- [ ] Aucune règle métier n'est dupliquée silencieusement à un autre endroit du code déjà
      existant.
- [ ] Je signale au moins un point précis, positif ou négatif : une revue qui approuve sans
      commentaire n'a souvent pas vraiment eu lieu.

### Gestion d'incident

- [ ] L'impact utilisateur réel est mesuré avant de chercher la cause (qui est affecté,
      combien, depuis quand).
- [ ] Une communication de statut est envoyée aux parties prenantes avant d'avoir la
      solution complète : le silence pendant un incident coûte plus cher que l'incident lui-
      même en confiance.
- [ ] La priorité est donnée à limiter l'impact (rollback, feature flag) avant de comprendre
      la cause racine complète.
- [ ] Un post-mortem est planifié dans les 48h, pendant que les détails sont encore frais.
- [ ] Le post-mortem cherche des causes systémiques, pas un responsable individuel.

### Onboarding

- [ ] La personne a accès à tous les outils nécessaires avant son premier jour, pas
      découvert au fil de la première semaine.
- [ ] Une première tâche réelle mais à faible risque est identifiée à l'avance, livrable en
      2-3 jours, pour créer un premier cycle de feedback rapide.
- [ ] Un point de contact unique est désigné pour les questions, pas "toute l'équipe" par
      défaut : la diffusion de responsabilité réduit la probabilité qu'une question trouve
      une réponse.
- [ ] La documentation d'architecture existante est vérifiée à jour avant d'être partagée —
      une documentation obsolète est pire que l'absence de documentation.

## Compromis

| Option                                              | Coût                               | Bénéfice                                                          | Quand choisir                                                                            |
| --------------------------------------------------- | ---------------------------------- | ----------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Pas de checklist, confiance dans l'expérience       | Zéro coût de maintenance           | Rapide sur les cas triviaux, mais répète les mêmes oublis         | Jamais sur un moment à fort enjeu ou irréversible                                        |
| Checklist rigide, jamais mise à jour                | Coût de rédaction initial          | Fausse sécurité si elle ne reflète plus la réalité du système     | À éviter : une checklist doit être révisée après chaque incident qu'elle n'a pas prévenu |
| Checklist courte et vivante, révisée après incident | Discipline de mise à jour continue | Vérification systématique des vrais points de rupture historiques | Approche par défaut recommandée pour les quatre moments listés ici                       |

## Pièges classiques

- **La checklist qu'on suit de mémoire sans la relire.** Symptôme : les mêmes deux ou trois
  points sont systématiquement oubliés, toujours les mêmes.
- **Une checklist trop longue pour être suivie sous pression.** Symptôme : elle est ignorée
  entièrement dès qu'un incident réel survient, parce que personne n'a le temps de la lire
  en entier.
- **Une checklist jamais mise à jour après un incident qu'elle n'a pas empêché.** Symptôme :
  le même type d'incident se reproduit à l'identique un an plus tard.
- **Confondre checklist et documentation d'architecture.** Symptôme : la checklist devient un
  document de 15 pages que plus personne ne lit avant une mise en prod urgente.

## Ce que tu dois savoir défendre

- Explique pourquoi une checklist compense un phénomène cognitif universel et non un manque
  de compétence individuelle.
- Donne un exemple d'incident réel (vécu ou documenté) que l'une de ces checklists aurait
  empêché, et le point précis qui aurait été vérifié.
- Explique pourquoi une checklist non mise à jour après un incident devient une fausse
  sécurité.
