# INKU V1 — Master Overview

## Executive synthesis

INKU V1 is a **mobile-first offline local laboratory game** where the player buys procedural slimes from an incubator, stores them in **4 persistent slots**, earns **passive income**, and can place owned slimes into a **prairie scene** where they feel alive through readable minimal behavior.

The V1 is not a full creature-AI simulation.
The V1 is not a battle game.
The V1 is not yet a server-driven production build.

The V1 must prove one core product truth:

**a slime bought in the incubator has economic value, persistent identity, slot ownership, and a believable living presence in the prairie.**

## Player promise

The player:
- sees a slime appear in the incubator
- wants it
- buys it if they have enough money
- stores it in one of only 4 slots
- earns passive income from stored slimes
- opens a profile sheet, can rename it, sell it, or delete it
- sends it to the prairie
- starts to perceive it as more than an item

## Core loop

Title screen → offline local session → incubator → buy slime → auto-place in empty slot → passive income → inspect/manage slot → optional prairie presence → return to incubator → save session → receive offline income later.

## Primary scenes

- **Game title**: entry, tone, session start
- **Incubator**: economic progression core
- **Prairie**: living presence core
- **Game menu**: navigation shell
- **Slime profile sheet**: deep management surface

## V1 pillars

1. Slimes must feel owned, not merely generated.
2. Mobile readability must survive every system decision.
3. Slots must create real tension because there are only 4.
4. Prairie must justify existence through visible creature presence.
5. The system must remain phaseable and not explode into fake depth.

## Locked V1 systems

- Offline local persistence
- 4 persistent slots
- Rarity / price / passive income / resale
- Slime profile sheet
- Minimal mental model
- Minimal prairie behavior
- Minimal contextual co-presence
- Offline income delta time calculation

## Explicitly not V1

- full combat
- deep social memory
- advanced inter-slime relationships
- reproduction
- production backend / cloud authority
- heavy simulation layers
- multiple currencies / broad management complexity

## Implementation order

1. Economy + persistence
2. Possession + slot management
3. Prairie vertical slice
4. Mental model integration
5. Minimal inter-slime co-presence
6. Mobile polish and onboarding

## Reference truth files

- `01_preproduction/00_master/INKU_V1_GDD_LOCKED.md`
- `01_preproduction/00_master/INKU_V1_SCOPE_AND_HORSCOPE.md`
- `01_preproduction/00_master/INKU_V1_IMPLEMENTATION_ROADMAP.md`

## Product truth in one sentence

**INKU V1 is a collectible laboratory game where persistent slimes generate value, occupy scarce slots, and begin to matter because they can be observed as living entities.**
