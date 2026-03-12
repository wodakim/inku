# INKU_V1_MODULE_AND_FILE_IMPLEMENTATION_MAP_PHASE_BC

## 1. mission

Ce document traduit les briefs précédents en **plan d’attaque d’implémentation concret** pour :

- **Phase B — Economic Vertical Slice**
- **Phase C — Possession Vertical Slice**

## 2. implementation mapping principle

L’exécution future devra chercher à brancher la Phase B/C autour de **6 zones logiques seulement** :

1. save/local persistence
2. incubator offer generation and purchase
3. slot ownership state
4. profile sheet actions
5. offline income application
6. scene/UI binding

## 3. expected runtime architecture targets

### A. Core save domain
- créer une save neuve
- charger une save
- valider/réparer
- sauvegarder

### B. Incubator domain
- générer une offre
- lire l’offre courante
- acheter l’offre courante

### C. Slot domain
- lire les 4 slots
- trouver premier slot vide
- reorder
- maintenir cohérence slot ↔ slime

### D. Slime ownership domain
- créer le slime canonique à l’achat
- rename
- sell
- delete
- résoudre le slime d’un slot

### E. Income domain
- appliquer l’offline income
- calculer le revenu passif courant

### F. UI binding layer
- afficher argent
- afficher offre
- afficher slots
- ouvrir profile sheet
- déclencher actions métier
- rafraîchir l’UI après mutation

## 4. concrete file/module categories to identify in the real repo

### 4.1 bootstrap / app entry files
Exemples probables : `main.ts`, `index.ts`, `app.ts`, `boot.ts`, `index.html`.

### 4.2 save / storage files
Exemples probables : `save.ts`, `storage.ts`, `localSave.ts`, `persistence.ts`, `saveManager.ts`.

À y brancher :
- `InkuSave`
- `createNewSave()`
- `loadSave()`
- `validateAndRepairSave()`
- `saveToStorage()`

### 4.3 incubator / generation files
Exemples probables : `incubator.ts`, `shop.ts`, `offer.ts`, `generation.ts`, `slimeGenerator.ts`.

À y brancher :
- `currentOffer`
- `generateIncubatorOffer()`
- `purchaseCurrentOffer()`

### 4.4 slot UI / collection files
Exemples probables : `slots.ts`, `inventory.ts`, `storage.ts`, `ownedSlimes.ts`, `labPanel.ts`.

À y brancher :
- `slotState.slots`
- résolution du slime par `slimeId`
- badge `isInPrairie`

### 4.5 profile sheet / modal files
Exemples probables : `profileSheet.ts`, `slimeModal.ts`, `detailPanel.ts`, `bottomSheet.ts`.

À y brancher :
- `getDisplayName()`
- `renameSlime()`
- `sellSlime()`
- `deleteSlime()`

### 4.6 economy / currency files
Exemples probables : `economy.ts`, `currency.ts`, `money.ts`, `wallet.ts`.

### 4.7 scene renderer / DOM binding files
Exemples probables : `labScene.ts`, `incubatorScene.ts`, `ui.ts`, `dom.ts`.

## 5. exact implementation targets by phase

### Phase B targets
- save/storage
- incubator offer
- economy/currency
- slots rendering
- app bootstrap

Résultat attendu :
- save locale stable
- offre persistée
- achat fonctionnel
- auto-fill premier slot vide
- offline income capé 8h

### Phase C targets
- slot interaction layer
- profile sheet
- slime ownership actions
- reorder logic
- save synchronization

Résultat attendu :
- slot tap -> profile sheet
- rename/sell/delete persistants
- reorder persistant
- badge `isInPrairie`

## 6. recommended module responsibilities

### Save module
- `createNewSave(now)`
- `loadSave()`
- `validateAndRepairSave(rawSave, now)`
- `saveToStorage(save)`
- `commitSave(save)`

### Incubator module
- `generateIncubatorOffer(now, seed?)`
- `ensureCurrentOffer(save, now)`
- `purchaseCurrentOffer(save, now)`

### Slot module
- `findFirstEmptySlot(slots)`
- `getOccupiedSlots(save)`
- `resolveSlimeFromSlot(save, slotIndex)`
- `reorderSlots(save, sourceIndex, targetIndex)`

### Slime ownership module
- `createCanonicalSlimeFromOffer(offer, slotIndex, now)`
- `renameSlime(save, slimeId, newName, now)`
- `sellSlime(save, slimeId, now)`
- `deleteSlime(save, slimeId, now)`
- `getDisplayName(slime)`

### Income module
- `computePassiveIncomePerMinute(save)`
- `applyOfflineIncome(save, now)`
- `computeOfflineIncomeSummary(save, elapsedMs)`

### UI binding
- `renderCurrency(save)`
- `renderCurrentOffer(save)`
- `renderSlots(save)`
- `openProfileSheet(slimeId)`
- `closeProfileSheet()`
- `showFeedback(message)`

## 7. safest patch sequence

1. locate real entry point and current state ownership
2. install canonical save skeleton
3. connect incubator offer to canonical save
4. implement purchase transaction
5. implement offline income on bootstrap
6. connect 4 slots to canonical save
7. implement profile sheet actions
8. implement reorder
9. add repair guards and anti-regression checks

## 8. anti-refactor rules

Interdits :
- refactor global inutile
- nouveau state management massif sans besoin
- réécriture complète du générateur slime
- backend abstrait prématuré
- inventaire caché hors slots

## 9. integration risks to watch

- confusion preview / slime possédé
- multiples sources de vérité pour l’argent
- slots non canoniques
- actions destructrices qui nettoient mal
- reorder purement visuel
- offre régénérée à la réouverture

## 10. recommended implementation granularity

- patch set 1 : save skeleton + repair + bootstrap
- patch set 2 : offer persistence + purchase transaction
- patch set 3 : offline income
- patch set 4 : slots binding
- patch set 5 : profile sheet rename/sell/delete
- patch set 6 : reorder + integrity guards
