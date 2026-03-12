# INKU V1 — Implementation Roadmap

## Guiding rule

Implement what proves the real loop first, not what looks most ambitious.

## Phase A — Documentation lock
Deliverables:
- master overview
- locked GDD
- scope/horscope
- roadmap
- data taxonomy
- validation documents

Validation:
- no major ambiguity remains

## Phase B — Economic vertical slice
Includes:
- title → offline local → incubator
- money
- incubator offer
- buy flow
- slot auto-placement
- local save/load
- passive income
- offline income

Validation:
- player can buy a slime and find it in a slot after reopening the game

## Phase C — Possession vertical slice
Includes:
- long press profile sheet
- rename
- resale
- delete
- slot reorder

Validation:
- slime feels like an owned specimen, not just a row of data

## Phase D — Prairie vertical slice
Includes:
- prairie scene
- active presence for up to 3 slimes
- tactile interaction
- depth
- minimal behavior

Validation:
- slime presence feels alive enough to justify the scene

## Phase E — Mental model integration
Includes:
- 7 stable traits
- 5 dynamic states
- 8 synthetic states
- behavior modulation

Validation:
- two slimes feel behaviorally different

## Phase F — Minimal inter-slime co-presence
Includes:
- soft approach
- soft avoidance
- proximity tolerance

Validation:
- several slimes no longer feel blind to each other

## Phase G — Offline V1 polish
Includes:
- onboarding
- error handling
- UI cleanup
- mobile readability review
- save robustness

Validation:
- flow is understandable without a heavy tutorial

## Likely future technical handoff targets
- slime core refactor into reusable multi-instance entity
- incubator ↔ slot ↔ local persistence connections
- offline income delta-time logic
- prairie active-presence integration
- future migration path from local save to backend authority
