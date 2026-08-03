# Pourquoi ce niveau existe

## La scène

Sofia reprend le backend de gestion des créneaux d'un cabinet vétérinaire. Trois praticiens,
des créneaux qui se chevauchent selon la spécialité (chirurgie vs consultation), des
annulations tardives. On lui demande d'ajouter "la possibilité de bloquer un créneau pour
urgence sans notification client". Elle code la feature en une matinée. Elle casse en
silence l'invariant qui garantissait qu'un créneau bloqué libère toujours son historique de
facturation. Personne ne l'a vu venir parce que personne n'avait de modèle explicite de ce
qui, dans ce système, ne devait jamais changer.

## Ce qui se passe vraiment

Sans ce niveau, chaque décision technique se prend sur l'intuition du moment. Avec lui, tu
disposes de quatre leviers qui transforment une intuition en raisonnement vérifiable :
- voir les frontières et les états d'un système (leçon 02),
- chiffrer le coût réel d'un choix (leçon 03),
- traiter l'incertitude comme une donnée à réduire, pas à ignorer (leçon 04),
- écrire pour forcer la clarté avant l'implémentation (leçon 05).

```text
   sans mindset                          avec mindset
   ┌──────────────┐                     ┌──────────────┐
   │ intuition ───►│ code               │ hypothèse ───►│ expérience
   │               │                     │  chiffrée     │  ciblée
   └──────┬───────┘                     └──────┬───────┘
          ▼                                    ▼
   régression invisible                 décision défendable
   découverte en prod                   et réversible à coût connu
```

## Ce que tu dois savoir défendre

1. Donne un exemple où une feature "petite" a cassé un invariant caché faute de modèle
   explicite du système.
2. Pourquoi "coder vite" n'est pas en contradiction avec ce niveau — précise à quel moment
   la vitesse redevient un problème.
3. Quel est le lien entre les 4 leçons de ce niveau — pourquoi ne peux-tu pas en sauter une.
