# Grimoire : Data Spells

| Terme | Ce que c'est | Ce qui casse sans ça | Ce que tu dois savoir défendre |
| --- | --- | --- | --- |
| Entité | Chose du domaine avec une identité stable, indépendante de ses attributs | Tu confonds identité et attributs, deux lignes différentes se retrouvent fusionnées par erreur | Peut-elle changer d'attribut sans changer d'identité ? |
| Clé naturelle / clé technique | Donnée du monde réel censée être unique, contre un identifiant inventé par le système sans signification métier | Une clé naturelle qui change (email, numéro) casse toutes les références qui s'appuient dessus | Pourquoi cette table utilise une clé technique et pas la clé naturelle en identifiant primaire ? |
| Invariant | Règle qui doit rester vraie en toutes circonstances, quel que soit le chemin de code emprunté | Un chemin de code oublié viole la règle silencieusement, incident découvert bien plus tard | Cet invariant est-il défendu par une contrainte de base, ou seulement par du code applicatif ? |
| Normalisation | Éliminer la duplication d'un même fait pour qu'il ne puisse jamais se contredire lui-même entre deux copies | Deux copies du même fait divergent, plus personne ne sait laquelle est vraie | Ce fait est-il dupliqué quelque part, et si oui pourquoi assumé ? |
| Dénormalisation en snapshot | Recopier volontairement une donnée pour figer un fait historique (facture), nommée explicitement | Un historique se met à jour rétroactivement, une facture déjà émise change de montant sans qu'on le veuille | Pourquoi cette copie est un snapshot volontaire et pas un oubli de normalisation ? |
| Index partiel | Index restreint aux lignes qui vérifient une condition, seul moyen fiable d'imposer "au plus une ligne de ce type" quand NULL est impliqué | Une contrainte UNIQUE classique laisse passer plusieurs lignes NULL, l'invariant métier n'est jamais réellement posé | Pourquoi une contrainte UNIQUE simple ne suffit pas ici ? |
| Contrainte d'exclusion | Garantit qu'aucune paire de lignes ne viole une relation donnée (ex : aucune période qui chevauche une autre) | Deux périodes se chevauchent silencieusement, la base ne le refuse jamais | Comment la base empêche-t-elle, elle-même, deux périodes qui se chevauchent ? |
| Expand/contract | Découper un changement de schéma en étapes (ajouter, remplir, basculer, retirer) qui restent chacune sûres isolément, sans downtime | Un déploiement interrompu au milieu laisse le schéma dans un état incohérent, avec coupure de service | Si le déploiement s'arrête à cette étape précise, le système reste-t-il cohérent ? |
| NOT VALID / VALIDATE CONSTRAINT | Poser une contrainte immédiatement pour les nouvelles écritures, valider les lignes existantes séparément, sans verrou exclusif prolongé | Ajouter une contrainte NOT NULL en une seule instruction verrouille toute la table en production | Comment évites-tu un verrou exclusif prolongé en ajoutant cette contrainte ? |
| Soft delete / audit trail | Soft delete : marquer une ligne comme supprimée sans l'effacer. Audit trail : trace de qui a changé quoi et quand, indépendante du soft delete | Une donnée référencée disparaît et casse les lectures qui en dépendent, ou personne ne peut reconstituer une décision passée | As-tu un filtre systématique côté lecture pour les lignes soft-deleted ? |
| N+1 | Une requête par élément d'une liste dans une boucle applicative, coût qui croît linéairement avec le volume | Le temps de réponse explose dès que le volume de données grossit, invisible en développement | As-tu vérifié le nombre de requêtes exécutées par un compteur, ou seulement à l'œil ? |
| Pagination par curseur | Navigation basée sur une clé stable et ordonnée en totalité, stable même sous insertions/suppressions concurrentes | La pagination par offset saute ou répète des lignes quand des insertions/suppressions arrivent en cours de navigation | Ta clé de curseur garantit-elle un ordre total sans égalité possible entre deux lignes ? |
| Lost update | Deux transactions concurrentes lisent la même valeur, écrivent chacune une version corrigée, l'une écrase silencieusement l'autre | Une modification concurrente disparaît sans erreur visible, sous READ COMMITTED | Comment détectes-tu, ou empêches-tu, ce lost update sur cette table ? |
| Verrou pessimiste (FOR UPDATE) vs optimiste (colonne version) | Pessimiste : empêche le conflit en bloquant les lecteurs concurrents. Optimiste : détecte le conflit au moment d'écrire, sans jamais bloquer | Un mauvais choix de verrou crée soit des blocages inutiles, soit des conflits non détectés | Pourquoi ce verrou-là plutôt que l'autre, sur cette table précise ? |

## Comportements évalués en boss-fight

| Comportement | Preuve attendue dans ta copie | Signal d'échec |
| --- | --- | --- |
| Refus argumenté de l'UPDATE en place (justification par un mécanisme) | Le refus s'appuie sur le mécanisme précis (perte d'historique, application rétroactive fausse d'un tarif), pas une prudence générique | Un refus flou, non relié au mécanisme réel de perte de données |
| Schéma minimal livrable en un jour (compromis nommé et assumé) | La proposition (colonne de période, contrainte d'exclusion ou vérification applicative) est réellement réalisable dans le délai, sans refonte disproportionnée | Une refonte complète du système de facturation proposée dans l'urgence |
| Cohérence de la lecture du tarif applicable | La solution garantit que chaque facture lit le tarif en vigueur à la date de la consommation qu'elle facture, pas à la date du calcul | Le calcul lit le tarif au moment de l'exécution du batch, pas à la date réelle de consommation |
| Robustesse face à l'incertitude réglementaire (honnêteté sur ce qu'on ne sait pas) | La décision reste correcte que le recalcul rétroactif soit finalement exigé ou non, sans pari risqué sur l'une ou l'autre issue | Un pari implicite sur une seule issue réglementaire, qui casse si l'autre se produit |
| Vérifiabilité | La vérification proposée est concrète et exécutable, pas un vœu pieux ("on testera bien") | Une vérification vague, non exécutable avant dimanche minuit |

## Arbre de décision : normaliser ou dénormaliser une colonne

```text
Cette donnée change-t-elle indépendamment de la ligne qui la porte ?
  OUI -> normaliser (table séparée, référencée par clé étrangère).
  NON v

Cette donnée doit-elle rester figée pour une raison métier ou légale (facture, contrat) ?
  OUI -> dénormaliser en snapshot, NOMMÉ explicitement (_snapshot, _au_moment_de).
  NON -> laisser normalisé, mesurer avant d'optimiser.
```

## Arbre de décision : quel verrou de concurrence

```text
Les conflits d'écriture sur cette ligne sont-ils fréquents ?
  OUI, et la transaction est courte -> verrou pessimiste (SELECT ... FOR UPDATE).
  NON, conflits rares, forte concurrence de lecture -> verrou optimiste (colonne version).
  Garantie absolue requise, section critique rare et identifiée -> SERIALIZABLE, avec retry.
```

## Checklist avant toute migration sur une table de production vivante

- [ ] Le volume réel de la table a été pris en compte (pas seulement testé sur un jeu de trois
      lignes).
- [ ] Aucune contrainte `NOT NULL` + valeur par défaut n'est ajoutée en une seule instruction
      sur une table déjà pleine : passer par `NOT VALID` + `VALIDATE` ou un backfill par lots.
- [ ] Un `DROP COLUMN`/`DROP TABLE` n'est jamais dans le même déploiement que le code qui arrête
      de la lire : un délai d'observation sépare les deux.
- [ ] Chaque étape d'une migration expand/contract reste sûre isolément si le déploiement
      s'arrête au milieu.
- [ ] Le temps de verrouillage estimé de chaque instruction a été vérifié (`EXPLAIN`, doc du
      moteur), pas supposé.

## Checklist avant d'exposer une requête de liste à fort volume

- [ ] Aucune boucle applicative ne déclenche une requête par élément d'une liste précédente
      (N+1) : vérifié par un compteur de requêtes en test, pas seulement à l'oeil.
- [ ] La pagination est par curseur si le volume est non borné ou en forte croissance, avec un
      index couvrant exactement l'ordre du curseur.
- [ ] La clé de curseur garantit un ordre total (aucune égalité possible entre deux lignes
      distinctes).
- [ ] Chaque `WHERE`/`JOIN`/`ORDER BY` réellement exécuté souvent a un index correspondant,
      jamais "au cas où".

## Heuristique de secours

Avant d'écrire une contrainte "dans le code applicatif seulement", demande-toi : "si quelqu'un
modifie cette table directement en SQL un dimanche soir d'urgence, cet invariant tient-il
encore ?" Si la réponse est non, l'invariant doit descendre dans la base : contrainte `CHECK`,
index unique partiel, ou contrainte d'exclusion : indépendamment de la confiance qu'on a dans
le code applicatif.
