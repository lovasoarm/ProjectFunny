# Challenge : MVP Split

## Contexte

Reprends le cadrage produit dans le niveau 02 (cabinets vétérinaires, gestion des urgences
inter-cabinets) : ou le tien si tu as fait le challenge précédent sur un autre cas. Le besoin
retenu est : permettre à une secrétaire d'un cabinet de signaler une urgence qui déborde de sa
capacité, et de voir en temps réel quel autre cabinet du réseau peut la prendre en charge, sans
passer par le groupe WhatsApp actuel. La direction veut un premier résultat livrable et
utilisable dans quatre semaines.

## Ce que tu dois produire

Un plan de découpage en tranches, en markdown, structuré comme suit :

1. **Découpage en tranches verticales** : au moins trois tranches, chacune livrant un scénario
   complet et utilisable seule (pas une couche technique isolée). Pour chaque tranche, précise
   ce qu'un utilisateur peut réellement faire à la fin de cette tranche, sans rien d'autre.
2. **Lignes de coupe** : pour la tranche la plus complexe des trois, identifie l'invariant à
   préserver (la règle dont la violation rendrait la fonctionnalité dangereuse ou trompeuse,
   pas juste limitée), et montre au moins une mauvaise coupe possible et la bonne coupe
   retenue à la place.
3. **Estimation honnête** : pour chacune des trois tranches, donne une fourchette (pas un
   chiffre unique) avec au moins une source d'incertitude explicite par tranche.
4. **Anticipation d'une demande de périmètre supplémentaire** : imagine une demande plausible
   d'ajout de périmètre qui arriverait pendant ces quatre semaines, et rédige la réponse que tu
   donnerais, en utilisant le raisonnement de coût d'opportunité (pas un simple refus).

## Contraintes

- Chaque tranche doit être vérifiable par un tiers : "un utilisateur peut faire X" doit être
  une phrase testable, pas une intention vague.
- Aucune tranche ne doit être une couche horizontale déguisée (par exemple "mettre en place la
  base de données" n'est pas une tranche recevable).
- Longueur cible : 80 à 150 lignes de markdown dense.

## Critères de réussite mesurables

- Chaque tranche, prise isolément, livre une valeur utilisable sans dépendre d'une tranche
  future non livrée.
- L'invariant identifié pour la tranche complexe est formulé comme une règle vérifiable, pas
  comme un vœu ("le système doit être fiable" n'est pas un invariant).
- Chaque fourchette d'estimation cite au moins une source d'incertitude concrète et vérifiable
  (pas "on ne sait jamais avec le dev").
- La réponse à la demande de périmètre supplémentaire mentionne explicitement ce qui serait
  sacrifié si elle était acceptée maintenant.

## Livrable

Un fichier markdown unique, nommé `decoupage-urgences-veto.md`, respectant la structure
ci-dessus.
