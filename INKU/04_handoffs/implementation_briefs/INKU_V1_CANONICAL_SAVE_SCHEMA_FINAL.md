# INKU_V1_CANONICAL_SAVE_SCHEMA_FINAL

## 1. purpose

Ce document verrouille le **schéma canonique de sauvegarde locale V1** pour INKU sur le périmètre :

- **Phase B — Economic Vertical Slice**
- **Phase C — Possession Vertical Slice**

## 2. schema scope

Ce schéma couvre uniquement :
- méta save locale
- argent local
- timestamp d’activité
- offre incubateur persistée
- 4 slots persistants
- registre canonique des slimes possédés
- propriétés nécessaires à achat, possession, rename, sell, delete, reorder, offline income et badge `isInPrairie`

## 3. canonical save root shape

```ts
type InkuSave = {
  saveVersion: string
  createdAt: number
  updatedAt: number
  lastActiveAt: number

  playerState: PlayerState
  incubatorState: IncubatorState
  slotState: SlotState
  slimeRegistry: Record<string, CanonicalSlimeRecord>
}
```

## 4. field-by-field contract

### `saveVersion: string`
- obligatoire
- non vide
- valeur initiale recommandée : `"inku-v1-phase-bc-1"`

### `createdAt: number`
- obligatoire
- fixé à la création initiale

### `updatedAt: number`
- obligatoire
- mis à jour à chaque mutation persistante

### `lastActiveAt: number`
- obligatoire
- sert au calcul offline income

### `playerState.softCurrency: number`
- obligatoire
- entier recommandé
- minimum `0`
- jamais négatif

### `incubatorState.currentOffer`
- une seule offre active à la fois
- `null` autorisé seulement en transition/recovery avant régénération

## 5. incubator offer shape

```ts
type IncubatorOffer = {
  offerId: string
  seed: number
  generatedAt: number
  price: number
  speciesArchetype?: string
  visualFingerprint?: string
  previewSnapshot: {
    displayNameSuggestion?: string
    rarityHint?: string
    paletteHint?: string
    morphologyHint?: string
  }
}
```

Règles :
- `offerId` unique
- `price` strictement positif
- la preview n’est pas un slime possédé
- aucun `slimeId` ne doit exister avant achat

## 6. slotState

```ts
type SlotState = {
  slots: [SlotRecord | null, SlotRecord | null, SlotRecord | null, SlotRecord | null]
}

type SlotRecord = {
  slotIndex: 0 | 1 | 2 | 3
  slimeId: string
}
```

Règles :
- exactement 4 slots
- `slotIndex` doit matcher la position réelle
- pas de duplication de `slimeId`

## 7. canonical slime record

```ts
type CanonicalSlimeRecord = {
  slimeId: string
  createdAt: number
  updatedAt: number
  customName: string | null
  defaultName: string
  origin: {
    source: "incubator_purchase"
    offerId: string
    acquiredAt: number
    purchasePrice: number
  }
  economy: {
    passiveIncomePerMinute: number
    resaleValue: number
  }
  ownership: {
    owned: boolean
    storageKind: "slot"
    slotIndex: 0 | 1 | 2 | 3
    isInPrairie: boolean
  }
  profile: {
    rarityHint?: string
    paletteHint?: string
    morphologyHint?: string
    temperamentHint?: string
  }
}
```

Règles :
- `slimeId` unique
- `defaultName` non vide
- `customName` trimmé si présent
- `origin.source === "incubator_purchase"`
- `ownership.storageKind === "slot"`
- `ownership.owned === true`
- `ownership.slotIndex` cohérent avec `slotState`

## 8. default value rules

### New save
```ts
const newSave: InkuSave = {
  saveVersion: "inku-v1-phase-bc-1",
  createdAt: now,
  updatedAt: now,
  lastActiveAt: now,
  playerState: { softCurrency: STARTING_SOFT_CURRENCY },
  incubatorState: { currentOffer: generateIncubatorOffer(now) },
  slotState: { slots: [null, null, null, null] },
  slimeRegistry: {},
}
```

### New slime defaults
- `customName = null`
- `owned = true`
- `storageKind = "slot"`
- `slotIndex = assignedSlotIndex`
- `isInPrairie = false`

## 9. canonical mutation rules

### Purchase
- vérifier offre, argent, slot vide
- débiter `softCurrency`
- créer `CanonicalSlimeRecord`
- remplir premier slot vide
- générer nouvelle offre
- sauvegarder

### Rename
- valider le nom
- écrire `customName`
- mettre à jour timestamps
- sauvegarder

### Sell
- créditer `softCurrency`
- nettoyer le slot
- supprimer du registre
- sauvegarder

### Delete
- nettoyer le slot
- supprimer du registre
- aucun crédit
- sauvegarder

### Reorder
- swap/move dans `slotState.slots`
- resynchroniser `ownership.slotIndex`
- sauvegarder

### Offline income
- calculer delta depuis `lastActiveAt`
- cap 8h
- sommer les revenus passifs des slimes stockés
- créditer `softCurrency`
- mettre `lastActiveAt = now`
- sauvegarder

## 10. load/repair rules

- si structure de base irrécupérable : reset save propre
- `softCurrency` invalide -> remplacer par valeur sûre
- `currentOffer` invalide -> régénérer
- `slots` invalides -> reconstruire tableau de 4
- slot vers slime manquant -> nettoyer le slot
- conflit `slotIndex` -> `slotState` est prioritaire
- slime dupliqué dans plusieurs slots -> conserver première occurrence
- slime orphelin hors slots -> supprimer du registre

## 11. source-of-truth hierarchy inside the save

1. `slotState.slots`
2. `slimeRegistry`
3. `ownership.slotIndex`
4. données UI reconstruites ensuite

## 12. offline income schema rules

- cap verrouillé : **8 heures**
- seuls les slimes encore présents dans les slots participent
- l’offre incubateur n’entre jamais dans ce calcul
- `isInPrairie = true` ne modifie pas le revenu passif en Phase B/C

## 13. strict non-persisted data rules

Ne doivent pas être persistés :
- animation
- physique
- positions/vitesses
- humeur runtime volatile
- état de modal/UI
- drag transitoire
- messages/toasts
- input non validé

## 14. invariants

- `slotState.slots.length === 4`
- `softCurrency >= 0`
- une seule offre active
- tout slot occupé référence un slime existant
- tout slime existant est référencé par exactement un slot
- aucun inventaire caché hors slots

## 15. serialization rules

Format recommandé : JSON simple sérialisable.
Interdits : fonctions, classes non sérialisables, références circulaires, objets DOM/runtime.
