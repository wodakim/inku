# INKU_V1_IMPLEMENTATION_BRIEF_PHASE_BC

## 1. mission

Ce document verrouille le brief d’implémentation pour les deux prochains blocs exécutablement sûrs de INKU V1 :

- **Phase B — Economic Vertical Slice**
- **Phase C — Possession Vertical Slice**

Objectif produit :

prouver la **vérité locale V1** de INKU avant toute extension de présence vivante avancée, backend, social, ou complexité systémique supplémentaire.

Objectif technique :

obtenir un runtime mobile-first capable de :

- charger une session locale propre
- afficher une offre d’incubateur cohérente
- permettre l’achat d’un slime si le joueur a assez d’argent
- stocker automatiquement le slime acheté dans un des **4 slots persistants**
- faire générer un revenu passif par les slimes stockés
- permettre l’inspection, le renommage, la vente et la suppression
- persister tout cela localement sans ambiguïté ni dérive de vérité

Ce brief doit être traité comme **contrat d’exécution**, pas comme brainstorming.

## 2. scope

## Phase B — Economic Vertical Slice

La Phase B inclut exclusivement :

- argent local persistant
- offre courante d’incubateur (`currentIncubatorOffer`)
- achat d’un slime depuis l’incubateur
- refus d’achat si argent insuffisant
- refus d’achat si les 4 slots sont pleins
- création du slime canonique acheté
- auto-placement dans le premier slot vide
- génération de revenu passif par les slimes stockés
- calcul d’offline income
- sauvegarde et chargement local cohérents

## Phase C — Possession Vertical Slice

La Phase C inclut exclusivement :

- visualisation des 4 slots persistants
- sélection d’un slime stocké
- ouverture de sa profile sheet
- renommage
- vente
- suppression
- réorganisation manuelle des slots
- persistance de ces actions
- affichage minimal de l’état `isInPrairie` dans les slots, sans prairie complète

## 3 ambiguïtés verrouillées avant rédaction

### 1. Cap du offline income pour le premier build
**Décision verrouillée : oui, cap présent en premier build.**

Règle :
- le offline income est plafonné à **8 heures** de temps écoulé.

### 2. Persistance exacte du `currentIncubatorOffer`
**Décision verrouillée : l’offre courante est persistée telle quelle.**

Règle :
- un seul `currentIncubatorOffer` actif à la fois
- il est sauvegardé localement
- il survit au redémarrage de l’application
- il est remplacé uniquement quand :
  - le joueur achète l’offre courante, ou
  - le joueur effectue explicitement une action de reroll si cette action existe plus tard
- en Phase B/C, **pas de rotation temporelle automatique**

### 3. Représentation minimale du flag `isInPrairie` dans les slots
**Décision verrouillée : simple badge visuel minimal, non interactif.**

Règle :
- si `isInPrairie = true`, le slot affiche un petit marqueur visuel discret du type :
  - badge “Prairie”
  - ou icône feuille/herbe
- ce badge n’altère pas la structure du slot
- il n’introduit aucune logique de prairie en Phase B/C
- il est purement informatif

## 3. non-goals

Ce brief **n’inclut pas** :

- backend
- auth
- cloud sync
- multi-device persistence
- social
- gifting
- trade
- anti-duplication serveur
- combat
- système relationnel profond
- prairie complète
- simulation comportementale multi-slimes avancée
- économie multi-ressources
- inventaire complexe
- évolution mentale complète
- UI dashboard lourde
- analytics
- live ops

Tout ce qui dépasse la vérité locale achat → possession → revenu passif → gestion des 4 slots est hors périmètre.

## 4. canonical data contract

Le runtime doit distinguer strictement :

- **save canonique**
- **état UI/runtime transitoire**

La save contient seulement la vérité persistante nécessaire.

## Root save shape

```ts
type InkuSave = {
  saveVersion: string
  createdAt: number
  updatedAt: number
  lastActiveAt: number

  playerState: {
    softCurrency: number
  }

  incubatorState: {
    currentOffer: IncubatorOffer | null
  }

  slotState: {
    slots: [SlotRecord | null, SlotRecord | null, SlotRecord | null, SlotRecord | null]
  }

  slimeRegistry: Record<string, CanonicalSlimeRecord>
}
```

## Incubator offer

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
- `currentOffer` est une proposition d’achat persistée
- ce n’est pas encore un slime possédé
- aucun `slimeId` canonique ne doit exister avant achat réussi

## Slot record

```ts
type SlotRecord = {
  slotIndex: 0 | 1 | 2 | 3
  slimeId: string
}
```

Règles :
- un slot contient au plus un `slimeId`
- un slime stocké ne peut être référencé que par un seul slot
- l’ordre visible provient du tableau de slots, pas du registre des slimes

## Canonical slime record

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

## Data contract rules

- `softCurrency` ne doit jamais être négatif
- `currentOffer` peut être `null` seulement pendant une courte transition de remplacement, ou à l’initialisation avant génération
- tout slime possédé par le joueur dans cette phase doit avoir :
  - `owned = true`
  - `storageKind = "slot"`
  - un `slotIndex` valide
- la source d’acquisition Phase B/C est uniquement `incubator_purchase`
- `isInPrairie` peut exister dans la save dès maintenant, mais aucune logique prairie complète ne s’appuie encore dessus
- aucune donnée runtime de physique, animation ou humeur volatile ne doit être sauvegardée ici

## 5. exact user flows

## Phase B — Economic Vertical Slice

### Flow B1 — First boot / local session bootstrap

1. application démarre
2. le système tente de charger la save locale
3. si aucune save :
   - création d’une nouvelle save valide
   - initialisation du solde de départ
   - génération d’un `currentIncubatorOffer`
   - slots initialement vides
4. si save existante :
   - chargement
   - calcul du offline income
   - mise à jour du solde
   - conservation du `currentIncubatorOffer`
5. affichage de l’UI incubateur et des slots

### Flow B2 — View incubator offer

1. le joueur arrive sur l’incubateur
2. l’offre courante est lue depuis `incubatorState.currentOffer`
3. l’UI affiche :
   - preview minimale
   - prix
   - affordance d’achat
4. aucune regénération automatique ne doit se produire juste parce que l’écran est rouvert

### Flow B3 — Successful purchase

1. le joueur appuie sur acheter
2. système vérifie :
   - offre courante non nulle
   - argent suffisant
   - au moins un slot vide
3. si valide :
   - décrément du solde
   - création d’un `CanonicalSlimeRecord`
   - insertion du slime dans le premier slot vide
   - calcul initial de sa valeur de revente
   - génération d’une nouvelle `currentIncubatorOffer`
   - save immédiate
4. l’UI reflète :
   - argent mis à jour
   - slot rempli
   - nouvelle offre

### Flow B4 — Purchase refused: insufficient money

1. le joueur tente un achat
2. système détecte `softCurrency < price`
3. achat refusé
4. aucun changement de save
5. message UX minimal :
   - “Argent insuffisant”

### Flow B5 — Purchase refused: full slots

1. le joueur tente un achat
2. système détecte absence de slot vide
3. achat refusé
4. aucun changement de save
5. message UX minimal :
   - “Slots pleins”

## Phase C — Possession Vertical Slice

### Flow C1 — Open slot and inspect slime

1. le joueur tape un slot occupé
2. le système résout le `slimeId`
3. lit le `CanonicalSlimeRecord`
4. ouvre la profile sheet
5. affiche :
   - nom affiché
   - hints visuels/profil
   - revenu passif
   - valeur de revente
   - marqueur `Prairie` si `isInPrairie = true`

### Flow C2 — Rename slime

1. profile sheet ouverte
2. le joueur modifie le nom
3. validation minimale :
   - trim
   - non vide après trim
   - longueur max définie, par exemple 20 caractères
4. save du `customName`
5. refresh UI slot + profile sheet

### Flow C3 — Sell slime

1. profile sheet ouverte
2. le joueur choisit vendre
3. système :
   - crédite `softCurrency` de `resaleValue`
   - vide le slot associé
   - supprime le slime du `slimeRegistry`
   - sauvegarde
4. profile sheet se ferme
5. l’UI met à jour argent + slot + revenu passif global

### Flow C4 — Delete slime

1. profile sheet ouverte
2. le joueur choisit supprimer
3. système :
   - vide le slot associé
   - supprime le slime du `slimeRegistry`
   - aucun crédit d’argent
   - sauvegarde
4. profile sheet se ferme
5. l’UI met à jour le slot

### Flow C5 — Reorder slots

1. le joueur déclenche la réorganisation
2. il déplace un slime d’un slot source vers un slot cible
3. système échange ou déplace les références de slots
4. met à jour `ownership.slotIndex` dans chaque slime impacté
5. sauvegarde immédiatement
6. l’ordre persiste au redémarrage

## 6. save/load rules

## Save rules

La save doit se produire immédiatement après toute mutation canonique :

- création initiale de save
- achat réussi
- vente
- suppression
- renommage
- reorder de slot
- application du offline income au chargement
- génération/remplacement de `currentIncubatorOffer`

## Load rules

Au chargement :

1. lire la save brute
2. valider sa structure minimale
3. si invalide ou corrompue :
   - fallback vers nouvelle save propre
   - idéalement avec log de récupération
4. appliquer offline income avant affichage final
5. mettre à jour `lastActiveAt`
6. persister la save mise à jour

## Save discipline rules

- pas de sauvegarde partielle incohérente
- toutes les mutations métier doivent être transactionnelles à l’échelle locale
- ne jamais mettre à jour un slot sans mettre à jour le slime correspondant
- ne jamais supprimer un slime sans nettoyer le slot qui le référence
- `updatedAt` et `lastActiveAt` doivent être réécrits à chaque mutation persistante importante

## 7. offline income rules

## Source of truth

Le offline income vient uniquement des slimes actuellement stockés dans les 4 slots.

## Formula

- calculer `elapsedMs = now - lastActiveAt`
- convertir en minutes
- appliquer cap :
  - `effectiveElapsedMs = min(elapsedMs, 8h)`
- sommer le `passiveIncomePerMinute` de tous les slimes stockés
- gain = `floor(totalRatePerMinute * effectiveMinutes)`

## Additional rules

- si aucun slime stocké, gain = 0
- si `elapsedMs <= 0`, gain = 0
- ne jamais générer de gain à partir d’un slime supprimé ou vendu
- `isInPrairie` n’exclut pas le slime du revenu passif en Phase B/C tant qu’il reste stocké en slot
- le offline income est appliqué une seule fois par cycle de chargement
- après application, `lastActiveAt = now`

## 8. slot rules

## Core slot rules

- exactement **4 slots persistants**
- indices fixes : `0, 1, 2, 3`
- un seul slime par slot
- un slime ne peut exister dans plusieurs slots
- l’ordre des slots fait partie de la vérité produit

## Auto-fill rule

Lors d’un achat réussi :
- le slime est assigné au **premier slot vide**, dans l’ordre croissant des indices

## Full state rule

Si les 4 slots sont occupés :
- achat impossible
- aucune exception silencieuse
- aucune création de slime orphelin

## Reorder rule

- la réorganisation modifie la position persistante
- après réorganisation, `slotIndex` du ou des slimes concernés doit être synchronisé

## `isInPrairie` slot-display rule

Représentation minimale verrouillée :
- badge discret sur le slot occupé
- pas d’animation complexe
- pas de sous-état supplémentaire
- aucun effet sur la logique d’achat, vente, suppression ou revenu passif en Phase B/C

## 9. profile sheet rules

## Minimum required content

La profile sheet doit afficher au minimum :

- nom affiché du slime
- fallback sur `defaultName` si `customName` nul
- valeur de revenu passif
- valeur de revente
- hints visuels/profil minimaux si disponibles
- badge `Prairie` si `isInPrairie = true`

## Allowed actions

- rename
- sell
- delete
- close

## Rename rules

- trim obligatoire
- non vide
- longueur max stricte
- aucun besoin de filtre sémantique complexe en V1 local

## Sell rules

- donne l’argent prévu
- retire définitivement le slime de la possession
- libère le slot
- ferme la profile sheet
- sauvegarde immédiatement

## Delete rules

- ne donne aucun argent
- retire définitivement le slime
- libère le slot
- ferme la profile sheet
- sauvegarde immédiatement

## 10. error cases

## Data / save errors

### E1 — Save absente
- créer une save neuve valide

### E2 — Save corrompue
- fallback vers save neuve
- ne pas tenter de continuer avec un état incohérent

### E3 — Slot référence un slimeId introuvable
- nettoyer le slot
- sauvegarder après nettoyage

### E4 — Slime indique un `slotIndex` incompatible avec les slots
- la vérité doit être réparée à partir de `slotState`
- ou nettoyer l’entrée incohérente selon stratégie de recovery
- ne jamais laisser l’incohérence survivre silencieusement

## Purchase errors

### E5 — `currentOffer = null`
- achat impossible
- régénération d’offre si nécessaire
- ne pas crasher

### E6 — Argent insuffisant
- achat refusé proprement

### E7 — Slots pleins
- achat refusé proprement

## Profile errors

### E8 — Tentative d’ouvrir un slot vide
- aucune profile sheet
- pas d’erreur fatale

### E9 — Rename invalide
- refus local
- pas de save

### E10 — Sell/Delete sur slime déjà absent
- no-op sécurisé
- pas de crash

## Reorder errors

### E11 — Source ou cible invalide
- annulation propre
- aucune mutation canonique

## 11. invariants and anti-regression rules

Ces invariants sont non négociables.

## Ownership invariants

- tout slime possédé en Phase B/C doit être présent exactement une fois dans `slotState.slots`
- tout slot occupé doit référencer un slime existant
- aucune duplication de `slimeId`
- aucun slime orphelin marqué `owned = true` sans slot valide

## Economy invariants

- `softCurrency >= 0`
- achat réussi = débit exact du prix
- vente = crédit exact de `resaleValue`
- suppression = crédit zéro
- aucun achat ne doit créer de slime si le débit n’a pas eu lieu
- aucun débit ne doit avoir lieu si le slime n’a pas été créé et assigné correctement

## Offer invariants

- une seule offre courante persistante
- une offre n’est remplacée qu’après achat réussi ou remplacement explicite futur
- réouvrir l’écran ne doit pas changer l’offre

## Slot invariants

- exactement 4 slots
- auto-fill = premier slot vide
- reorder persistant
- slot full = blocage achat

## Save invariants

- toute action métier modifiante déclenche une save
- le chargement doit pouvoir reconstruire l’état sans dépendance à l’état UI précédent
- aucune donnée d’animation ou physique volatile ne doit polluer la save canonique

## Anti-regression rules

- ne pas introduire de logique prairie complète dans Phase B/C
- ne pas introduire de backend abstraction prématurée
- ne pas faire dériver la profile sheet vers une fiche surchargée
- ne pas transformer les slots en inventaire complexe
- ne pas rendre le revenu passif opaque ou incompréhensible
- ne pas faire tourner automatiquement les offres au hasard
- ne pas faire dépendre la possession d’un runtime slime vivant

## 12. runtime validation checklist

## Phase B validation

- lancement sans save crée une save valide
- un `currentIncubatorOffer` existe après bootstrap
- l’offre reste identique après fermeture/réouverture de l’app
- achat impossible si argent insuffisant
- achat impossible si slots pleins
- achat réussi débite le bon montant
- achat réussi remplit le premier slot vide
- achat réussi crée un slime canonique unique
- achat réussi remplace l’offre par une nouvelle offre
- revenu passif calculable à partir des slimes stockés
- offline income appliqué au redémarrage
- offline income plafonné à 8 heures
- aucune duplication de slime après plusieurs achats

## Phase C validation

- taper un slot occupé ouvre la profile sheet correcte
- rename persiste après redémarrage
- vente crédite la bonne somme
- vente vide le slot
- suppression ne crédite rien
- suppression vide le slot
- reorder persiste après redémarrage
- un slot avec `isInPrairie = true` affiche le badge minimal
- badge `Prairie` n’altère ni revenu passif ni logique de possession
- aucun slot n’affiche un slime supprimé
- aucun slime vendu/supprimé ne subsiste dans le registre

## Recovery validation

- save corrompue ne fait pas crasher l’app
- référence cassée slot/slime est nettoyée
- achat interrompu ne laisse ni débit sans slime, ni slime sans slot

## 13. ready-for-Codex execution plan

Codex devra exécuter ce brief comme une intégration incrémentale stricte.

## Step 1 — Audit and map current local architecture

Identifier sans supposition :

- module de save local actuel
- structures de state existantes
- implémentation actuelle de l’incubateur
- représentation actuelle des slots
- profile UI existante ou absente
- points d’entrée où brancher :
  - load
  - save
  - achat
  - sélection slot
  - rename
  - sell
  - delete
  - reorder

## Step 2 — Implement canonical local save model

Introduire ou corriger :

- `InkuSave`
- `playerState`
- `incubatorState.currentOffer`
- `slotState.slots[4]`
- `slimeRegistry`

## Step 3 — Implement Phase B transaction flow

Créer la transaction locale d’achat :

- vérifier l’offre
- vérifier l’argent
- vérifier le slot vide
- débiter
- créer le slime canonique
- assigner premier slot vide
- générer nouvelle offre
- sauvegarder

## Step 4 — Implement offline income

Ajouter :

- lecture `lastActiveAt`
- calcul du delta
- cap 8 heures
- somme des revenus passifs stockés
- application au solde
- mise à jour du timestamp
- sauvegarde

## Step 5 — Implement Phase C profile sheet contract

Créer ou adapter la profile sheet pour supporter :

- lecture du slime depuis slot
- affichage nom
- revenu passif
- valeur de revente
- hints
- badge `Prairie`
- actions :
  - rename
  - sell
  - delete
  - close

## Step 6 — Implement slot reorder contract

Ajouter une logique de reorder persistante qui :

- échange ou déplace les références de slots
- met à jour les `slotIndex` canoniques
- sauvegarde immédiatement

## Step 7 — Add integrity guards and repair logic

Implémenter des garde-fous :

- nettoyage des `slimeId` introuvables
- réparation des divergences slot/slime
- refus propre des actions invalides
- logs de recovery si nécessaire

## Constraints for Codex execution

- ne pas toucher au backend
- ne pas implémenter la prairie complète
- ne pas introduire de sur-architecture
- ne pas réinventer le scope
- ne pas modifier les contrats verrouillés ci-dessus
- ne pas confondre preview incubateur et slime possédé
- ne pas persister des données runtime volatiles
