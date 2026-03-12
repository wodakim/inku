# INKU V1 — GDD Locked

## 1. Vision

INKU V1 is a mobile-first laboratory collection game focused on buying procedural slimes, storing them in 4 persistent slots, earning passive income, and observing them in a prairie scene where they display readable living behavior.

The V1 must avoid:
- generic idle-game emptiness
- spreadsheet-like management overload
- fake AI depth with no visible payoff
- premature backend and production complexity

## 2. Core loop

1. Game title
2. Offline local entry
3. Incubator scene
4. Slime offer appears
5. Player buys if money and slot conditions are met
6. Slime auto-fills first empty slot
7. Stored slimes generate passive income
8. Player can inspect, rename, sell, delete, reorder
9. Player can send slimes to prairie
10. Player returns later and receives offline income

## 3. Core scenes

### Title
Entry and tone only.

### Incubator
Primary economic scene.
Shows:
- current slime offer
- rarity
- price
- buy action
- player money
- 4 slots
- game menu

### Prairie
Primary living-presence scene.
Shows:
- up to 3 active slimes
- depth
- tactile interaction
- minimal behavior
- light co-presence

### Slime profile sheet
Deep management surface opened by long press on slot.

## 4. Slot system

- Exactly 4 persistent slots in V1
- 1 slot = 1 slime
- Bought slime goes to first empty slot
- If all slots are full, purchase is refused
- Drag reorders slots
- Long press opens slime sheet

### Slot visible data
- miniature
- name
- rarity
- income/min
- single synthetic mood state

## 5. Economy V1

### Rarity tiers
- Common
- Uncommon
- Rare
- Epic
- Mythic

### Distribution
- Common 52%
- Uncommon 27%
- Rare 14%
- Epic 5%
- Mythic 2%

### Starting money
- 70

### Price bands
- Common: 20–45
- Uncommon: 55–110
- Rare: 140–260
- Epic: 320–520
- Mythic: 700–1100

### Passive income per minute
- Common: 0.20–0.45
- Uncommon: 0.55–1.00
- Rare: 1.20–2.20
- Epic: 2.60–4.20
- Mythic: 5.00–8.00

### Resale
- 60% of purchase price in V1

### Deletion
- 0 gain

### Incubator rotation
- refresh every 3 minutes if not purchased
- refresh immediately after purchase

## 6. Slime profile sheet

Opened by long press on a slot.

Contains:
- portrait
- name
- rarity
- synthetic mood state
- income/min
- resale value
- purchase price
- dynamic state summary
- 2 dominant displayed traits
- 1 short descriptive sentence
- actions:
  - Rename
  - Put in prairie / Remove from prairie
  - Sell
  - Delete

## 7. Prairie V1

### Purpose
Prove that slimes are more than economic assets.

### Limits
- max 3 active slimes

### Behavior library
- idle/rest
- light wandering
- local exploration
- soft approach
- soft avoidance
- player reaction

### Interaction
- touch
- hold
- drag
- release

### Depth
Depth adds spatial readability and life, but must not break the slime rebound identity.

## 8. Mental model V1

### Stable traits
- Curiosity
- Sociability
- Fear
- Aggression
- Attachment
- Impulsiveness
- Emotional stability

### Dynamic states
- Mood
- Energy
- Stress
- Social need
- Player trust

### Synthetic visible states
- Calm
- Curious
- Playful
- Tired
- Tense
- Nervous
- Unstable
- Sociable

## 9. Inter-slime co-presence V1

No deep persistent relationship system yet.

Allowed contextual reactions:
- ignore
- tolerate / coexist
- soft approach
- soft avoidance

Driven lightly by:
- sociability
- fear
- aggression
- emotional stability
- stress
- social need

## 10. Persistence V1

Offline local only.

Persist exactly:
- money
- slots and slot order
- slime records
- seed
- visual genome
- stable traits
- main dynamic state
- timestamps
- current incubator slime offer + rotation expiry

Recompute:
- synthetic labels
- profile descriptions
- transient prairie micro-state

## 11. Onboarding

No heavy tutorial.
Teach through action and a few contextual hints.

Player should understand quickly:
- slimes appear in incubator
- they cost money
- they can be bought
- they go into slots
- they generate passive income
- they can be inspected
- they can be sent to prairie

## 12. Definition of V1 done

V1 is considered real when:
- buying works
- slots persist
- passive/offline income works
- slime identity persists
- profile sheet is useful
- prairie presence is readable
- differences between slimes are perceptible
- smartphone readability survives
