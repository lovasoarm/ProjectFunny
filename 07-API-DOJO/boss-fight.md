# Boss Fight : Le partenaire pressé et le token trop large

## La situation

Tu es responsable de l'API de refacturation d'énergie. Un nouveau partenaire commercial,
un comparateur de prix en forte croissance, doit être intégré "avant vendredi" pour une
campagne marketing déjà annoncée publiquement par le service commercial, sans validation
technique préalable de ta part. Le commercial qui a signé le contrat te transmet la demande
du partenaire telle quelle : un accès à `GET /consumptions/{siteId}` pour tous les sites de
tous les clients ayant donné leur accord, "pour aller plus vite on te propose de leur donner
un token comme celui du service de reporting interne, il a déjà tous les accès, ça évite de
te prendre la tête avec les scopes cette semaine". Le partenaire, de son côté, insiste pour
recevoir une réponse par email avec la clé en clair "pour que leur équipe puisse tester tout
de suite depuis Postman". Tu as trois jours. Le service commercial attend un "oui" simple, le
partenaire attend une clé, et tu es seul sur ce sujet dans l'équipe cette semaine-là.

## Les contraintes réelles

- Le contrat commercial signé ne mentionne aucune exigence technique précise sur le
  périmètre d'accès : c'est un flou que le commercial n'a pas identifié comme un risque.
- L'API actuelle n'a pas encore de système de scopes fins par client final ; seul le token
  "admin" global du reporting interne existe aujourd'hui comme modèle réutilisable rapide.
- Une fuite de données de consommation vers un tiers non autorisé constitue, dans ce secteur,
  une violation réglementaire avec sanction financière, pas seulement un problème d'image.
- Le partenaire a un historique connu dans le secteur : un incident public l'an dernier chez
  un autre fournisseur d'énergie, où son intégration avait exposé des données au-delà de ce
  qui était prévu, à cause d'un token mal scope similaire à celui proposé ici.

## Ce qu'on attend de toi

Produis une décision écrite (une page maximum) qui :

1. Refuse explicitement les deux raccourcis proposés (réutiliser le token admin, envoyer la
   clé en clair par email) en expliquant en une phrase pourquoi chacun est un pari perdant,
   avec un mécanisme concret du niveau à l'appui de chaque refus.
2. Propose une solution livrable dans les trois jours qui respecte le principe du moindre
   privilège : un token scope aux seuls sites sous contrat avec ce partenaire, transmis par
   un canal qui ne finit pas dans une boîte mail en clair.
3. Anticipe l'objection "on n'a pas de système de scopes fins, ça va nous retarder" avec une
   solution minimale réalisable en trois jours (par exemple : une table de correspondance
   partenaire → liste de siteIds autorisés, vérifiée à chaque appel, sans construire un
   système de scopes générique complet).
4. Propose un engagement mesurable pour éviter qu'un prochain partenariat signé sans
   validation technique préalable ne recrée la même urgence mal cadrée (par exemple : la
   case "accès API requis" dans le processus de signature commerciale déclenche
   automatiquement une revue technique avant signature, pas après).

## Grille d'évaluation

| Critère                             | Ce qui est évalué                                                                                                                                               |
| ----------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Refus argumenté des deux raccourcis | Le refus s'appuie sur un mécanisme du niveau (scope, moindre privilège, frontière de confiance, transmission de secret), pas sur une prudence générique         |
| Solution livrable en trois jours    | La proposition est réellement réalisable dans le délai avec les moyens actuels, sans exiger un système générique complet non existant                           |
| Réponse au manque d'outillage       | La solution minimale (table de correspondance vérifiée à l'appel) résout le problème réel sans sur-ingénierie disproportionnée au délai                         |
| Mécanisme anti-récidive             | L'engagement proposé s'intègre à un processus existant (signature commerciale) et est vérifiable, pas un vœu pieux                                              |
| Ton                                 | La décision est assumée et défendable simultanément devant le commercial pressé et le partenaire qui attend sa clé, sans céder sur le fond pour gagner du temps |

## Seuil de validation chiffré

| Critère | Points |
| --- | --- |
| Refus argumenté des deux raccourcis | 25 |
| Solution livrable en trois jours | 20 |
| Réponse au manque d'outillage | 20 |
| Mécanisme anti-récidive | 20 |
| Ton | 15 |
| **Total** | **100** |

```text
< 50   --> boss-fight non valide, la scène est a refaire apres relecture de la lecon concernee
50-69  --> valide avec reserve, identifie le critere le plus faible avant de le compter comme acquis
70-89  --> valide, le reflexe est en place
90-100 --> valide avec excellence, ce niveau de justesse est celui attendu en situation reelle
```

Seuil de passage : 70/100. En dessous, le niveau n'est pas considéré comme acquis, même si le
texte rendu est bien écrit.

**Éliminatoire :** Si "Refus argumenté des deux raccourcis" est noté en dessous de 10/25, le total est plafonné à 50/100 : un contrat d'API qui accepte un raccourci dangereux casse des clients réels, ce que ce niveau entier vise à empêcher.
