# Grimoire — Archi-Lab

## Vocabulaire

- **Couplage** : à quel point un module doit connaître l'intérieur d'un autre pour
  fonctionner. Plus c'est fort, plus un changement interne se propage en cassures.
- **Cohésion** : à quel point les éléments d'un module ont une raison commune d'être
  ensemble (une seule raison de changer par module = forte cohésion).
- **Dépendance dirigée** : une flèche "A dépend de B" qui doit toujours pouvoir se dessiner
  sans jamais former de cycle dans le graphe global du système.
- **Inversion de dépendance** : le domaine définit une interface, l'infra l'implémente — la
  flèche de dépendance va de l'infra vers le domaine, pas l'inverse.
- **Source de vérité** : la copie d'une donnée désignée comme faisant foi en cas de
  désaccord ; tout le reste est un dérivé reconstructible.
- **Cohérence éventuelle** : un décalage temporaire et assumé entre une copie et sa source
  de vérité, acceptable uniquement si aucune décision irréversible ne s'appuie dessus.
- **Monolithe modulaire** : une seule unité de déploiement, organisée en modules internes à
  frontières claires, sans appel réseau entre les parties.

## Le gradient de couplage (du pire au meilleur)

```text
Contenu > Commun > Contrôle > Donnée > Message
(le pire)                              (le mieux, réaliste = donnée le plus souvent)
```

## Les 4 couches et leur règle de dépendance

```text
UI ──> Cas d'usage ──> Domaine <── Infra (implémente une interface du Domaine)
```

Le Domaine ne connaît ni HTTP, ni SQL, ni JSON, ni aucun framework.

## Checklist avant de committer un module

- [ ] Je peux décrire ce module en une phrase sans "et".
- [ ] Si je le supprime et le remplace par une autre implémentation du même contrat, je sais
      exactement combien de fichiers je dois toucher ailleurs (idéalement zéro).
- [ ] Aucun import ne remonte d'une couche "basse" (infra) vers une couche "haute" (domaine,
      cas d'usage, UI).
- [ ] La règle métier qu'il porte se teste sans base de données ni serveur démarré.
- [ ] Je sais nommer sa source de vérité si ce module manipule une donnée dupliquée ailleurs.

## Les 4 critères de décision monolithe vs services

1. Autonomie organisationnelle réelle entre équipes.
2. Isolation de charge/scalabilité radicalement différente.
3. Isolation de risque ou de conformité.
4. Maturité opérationnelle de l'équipe pour absorber le coût réseau/déploiement.

Zéro critère vrai → monolithe modulaire, sans hésitation.

## Heuristique de secours

Quand tu doutes du découpage : demande-toi "quand cette chose change, qu'est-ce que je suis
obligé de changer avec ?" Si la réponse est "beaucoup de choses sans rapport apparent",
le découpage est mauvais, indépendamment de ce que dit le nom des dossiers.
