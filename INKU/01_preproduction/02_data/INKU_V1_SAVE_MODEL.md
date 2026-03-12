# INKU V1 — Save Model

## Save philosophy
Save little, but save the canonical truth.

## Saved exactly
- player money
- slot state and order
- slime canonical records
- current incubator offer and expiry
- timestamps
- save version

## Recalculated
- synthetic profile labels
- descriptive profile text
- transient prairie behavior state
- temporary UI presentation state

## Save triggers
Immediate save on:
- purchase
- sale
- delete
- rename
- slot reorder
- send to prairie
- remove from prairie
- major money update

## Load order
1. load raw save
2. validate version
3. rebuild player / slots / slime registry
4. apply time delta for offline income and lightweight state settling
5. rebuild scenes
