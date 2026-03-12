# INKU_V1_UI_GAMEFLOW_EXECUTION_BRIEF_PHASE_BC

## 1. purpose

Ce document verrouille le **gameflow d’exécution UI/UX** pour :

- **Phase B — Economic Vertical Slice**
- **Phase C — Possession Vertical Slice**

Il ne redéfinit pas le produit.
Il traduit les décisions déjà verrouillées en **comportements d’interface, états d’écran, CTA, transitions, feedbacks et règles de lisibilité mobile-first**.

## 2. product reading rule

La lecture produit doit rester immédiatement compréhensible :

1. **je vois une offre**
2. **je vois combien j’ai**
3. **je vois mes 4 slots**
4. **j’achète si je peux**
5. **le slime m’appartient**
6. **je peux le gérer**
7. **mes slimes rapportent passivement**

## 3. screen architecture in scope

### A. Entry / bootstrap result state
Pas un vrai écran complexe dans ce brief.

### B. Incubator main scene
Doit contenir :
- argent visible
- offre courante visible
- CTA achat
- 4 slots persistants visibles
- éventuel feedback offline income
- accès à la profile sheet en tapant un slot occupé

### C. Slime profile sheet
Doit contenir :
- identité du slime
- infos minimales utiles
- actions de possession

### D. Slot reorder mode
Mode léger, borné, non envahissant.

## 4. global UI principles

- toutes les actions critiques doivent être réalisables au pouce
- la hiérarchie visuelle doit être lisible sans micro-texte
- pas de densité type tableur
- pas de sous-menus inutiles
- pas de “feature fog”

## 5. exact incubator main scene contract

### 5.1 required visible blocks

#### Block 1 — Currency header
Affiche le montant actuel de `softCurrency`.

#### Block 2 — Current incubator offer
Affiche :
- représentation visuelle du slime proposé
- prix
- quelques hints lisibles si présents
- CTA d’achat

#### Block 3 — Four persistent slots
Affiche :
- 4 slots fixes
- vides ou occupés
- état immédiatement lisible

#### Block 4 — Context feedback area
Zone légère pour :
- offline income message
- refus d’achat
- retour action rename/sell/delete
- message de slot plein

### 5.2 offer button states

- **Enabled** : argent suffisant, slot vide, offre non nulle
- **Disabled by money** : feedback “Argent insuffisant”
- **Disabled by full slots** : feedback “Slots pleins”
- **Missing offer fallback** : placeholder neutre + régénération contrôlée

### 5.3 slot area exact contract

- exactement 4 slots visibles
- ordre stable
- pas de pagination
- pas de scroll horizontal

États :
- slot vide : placeholder discret, aucune profile sheet
- slot occupé : preview, nom affiché, indicateur de valeur, badge `Prairie` si applicable
- tap sur slot occupé : ouvre profile sheet

## 6. profile sheet contract

La profile sheet n’est pas une encyclopédie.
Elle doit afficher :
- nom affiché
- aperçu du slime
- slot actuel
- badge `Prairie` si applicable
- revenu passif
- valeur de revente
- hints légers optionnels

CTA autorisés :
- Renommer
- Vendre
- Supprimer
- Fermer

CTA interdits :
- prairie complète
- relations
- fusion
- combat
- mutation complexe
- social

## 7. rename flow UI contract

- entrée depuis la profile sheet
- champ simple dans la sheet ou mini sous-état
- validation : trim, non vide, longueur max stricte
- succès : save, refresh slot + sheet, feedback léger

## 8. sell flow UI contract

- montrer clairement la valeur reçue
- confirmation légère recommandée
- succès : fermeture sheet, slot vide, argent mis à jour, feedback “Vendu +X”

## 9. delete flow UI contract

- clairement distingué de la vente
- aucun argent rendu
- confirmation recommandée
- succès : fermeture sheet, slot vide, feedback “Supprimé”

## 10. reorder mode contract

- entrée via bouton/icône légère
- interaction : drag simple ou tap source puis cible
- déplacer vers vide = move
- déplacer vers occupé = swap
- ordre final persistant immédiatement

## 11. offline income feedback contract

- feedback bref
- pas de popup envahissante
- message type : “Revenus hors-ligne : +X”
- aucun message obligatoire si gain nul

## 12. exact error-state UX contract

- achat sans argent : “Argent insuffisant”
- achat slots pleins : “Slots pleins”
- rename invalide : “Nom invalide”
- slime manquant : pas de crash, cleanup si nécessaire
- save corrompue : récupération silencieuse ou quasi silencieuse

## 13. visibility rules for `isInPrairie`

- badge discret sur slot + profile sheet
- purement informatif
- aucun effet métier en Phase B/C

## 14. anti-dashboard rules

L’UI Phase B/C ne doit pas ressembler à :
- une grille de gestion sèche
- une table de stats
- un inventaire MMORPG
- une feuille Excel de créatures

## 15. exact minimum deliverable per phase

### Phase B minimum UI deliverable
- affichage argent
- affichage offre persistée
- bouton acheter
- feedback argent insuffisant
- feedback slots pleins
- affichage des 4 slots
- apparition du slime acheté dans premier slot vide
- feedback offline income minimal possible

### Phase C minimum UI deliverable
- tap slot occupé -> profile sheet
- rename
- sell
- delete
- reorder
- badge `Prairie`
- persistance de tous ces changements
