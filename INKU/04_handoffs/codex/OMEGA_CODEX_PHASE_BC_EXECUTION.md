# OMEGA_CODEX_PHASE_BC_EXECUTION

## Context

You are executing **Phase B / Phase C** for the real mobile-first game project **INKU**.

This is **not** a greenfield project.
This is **not** a redesign mission.
This is **not** a brainstorming session.

You are entering an existing project repository that contains:
- authoritative preproduction documents
- implementation briefs
- mockup HTML references
- handoff structure already prepared for this phase

Your job is to perform a **strict audit-first, regression-averse implementation** of:

- **Phase B — Economic Vertical Slice**
- **Phase C — Possession Vertical Slice**

You must execute against the repository reality.
You must not hallucinate architecture.
You must not assume a system exists until you verify it in code.

---

## Authority Order

If sources disagree, obey this order:

1. `01_preproduction/00_master/`
2. `04_handoffs/implementation_briefs/`
3. other `01_preproduction/` docs
4. `02_mockups_and_sources/`
5. `03_working_notes/`

Mockups are visual/runtime references only.
They do **not** override locked product/system/data decisions.

---

## Required Reading Before Any Code Change

You must read and align with these files before modifying anything:

### Master
- `01_preproduction/00_master/INKU_V1_SCOPE_AND_HORSCOPE.md`
- `01_preproduction/00_master/INKU_V1_GDD_LOCKED.md`
- `01_preproduction/00_master/INKU_V1_IMPLEMENTATION_ROADMAP.md`

### Phase B/C implementation briefs
- `04_handoffs/implementation_briefs/INKU_V1_IMPLEMENTATION_BRIEF_PHASE_BC.md`
- `04_handoffs/implementation_briefs/INKU_V1_UI_GAMEFLOW_EXECUTION_BRIEF_PHASE_BC.md`
- `04_handoffs/implementation_briefs/INKU_V1_CANONICAL_SAVE_SCHEMA_FINAL.md`
- `04_handoffs/implementation_briefs/INKU_V1_MODULE_AND_FILE_IMPLEMENTATION_MAP_PHASE_BC.md`

You must treat those files as the execution contract.

---

## Locked Mission

Implement a reliable local-first V1 foundation proving:

- local persistent money
- persistent `currentIncubatorOffer`
- purchase from incubator if enough money
- refusal if not enough money
- refusal if 4 slots are full
- canonical slime creation on successful purchase
- automatic placement into the **first empty slot**
- exactly **4 persistent slots**
- passive income from owned slot-stored slimes
- offline income with an **8-hour cap**
- slot tap opening a profile sheet
- rename
- sell
- delete
- reorder
- minimal `isInPrairie` slot/profile badge only

This phase is about proving **local truth of ownership and economy**.

---

## Locked Decisions You Must Not Change

These decisions are already settled. Do not reopen them.

### 1. Offline income cap
Offline income is **capped at 8 hours** for this first build.

### 2. Current incubator offer persistence
`currentIncubatorOffer` must be persisted exactly.
It survives app close/reopen.
It changes only when:
- a purchase succeeds
- or a future explicit reroll action exists

For this phase, there is **no automatic time-based reroll**.

### 3. `isInPrairie` representation
`isInPrairie` is represented only by a **minimal informational badge** in slot/profile display.
No prairie system expansion must be implemented here.

---

## Strict Scope

### Phase B
Implement only:
- canonical local save skeleton
- load / validate / repair / save pipeline
- persistent currency
- persistent incubator offer
- purchase transaction
- first-empty-slot auto-fill
- passive income source from owned slimes in slots
- offline income application on load
- UI refresh based on canonical state

### Phase C
Implement only:
- slot interaction from canonical save
- profile sheet opening from slot
- rename
- sell
- delete
- slot reorder
- minimal `isInPrairie` badge

---

## Non-Negotiable Non-Goals

Do **not** implement or expand:

- backend
- auth
- cloud sync
- server authority
- social systems
- gifting
- trading
- anti-duplication server logic
- combat
- prairie full scene logic
- relationship systems
- advanced AI / mental simulation
- inventory expansion beyond 4 slots
- multi-currency economy
- dashboard-heavy redesign
- architecture rewrites unrelated to this phase

Do not drift scope.

---

## Truthfulness Rules

You must be strict about truthfulness.

- Do not claim something is implemented unless runtime/code proves it.
- Do not assume a save system is reusable until you inspect it.
- Do not assume slots are canonical if they are only UI placeholders.
- Do not assume current slime preview equals owned slime entity.
- Do not say a phase is complete because TypeScript compiles.

If the real repo differs from the briefs, you must:
1. identify the difference precisely
2. preserve authority order
3. adapt implementation to the repo while keeping the locked contract intact

---

## Required Execution Style

You must behave as:
- senior mobile game frontend architect
- local persistence engineer
- gameplay economy integrator
- regression-averse refactoring lead
- implementation auditor

Your implementation style must be:
- audit first
- patch minimal
- no broad speculative refactors
- no parallel systems unless absolutely required
- no fake abstractions
- no product redesign

---

## Canonical Data Contract You Must Respect

You must implement or align to the canonical save contract defined in:

`04_handoffs/implementation_briefs/INKU_V1_CANONICAL_SAVE_SCHEMA_FINAL.md`

At minimum, the canonical save must support:

- `saveVersion`
- `createdAt`
- `updatedAt`
- `lastActiveAt`
- `playerState.softCurrency`
- `incubatorState.currentOffer`
- `slotState.slots` with exactly 4 entries
- `slimeRegistry`

You must keep a strict distinction between:
- persisted canonical truth
- transient UI/runtime state

Do **not** persist:
- DOM state
- animation state
- soft-body physics state
- temporary modal/open state
- transient drag state
- other volatile runtime artifacts

---

## Core Invariants You Must Preserve

These invariants are non-negotiable.

### Ownership invariants
- exactly 4 slots
- a slot contains at most one slime
- a slime can appear in only one slot
- every occupied slot references an existing slime
- every owned slime in this phase must be referenced by exactly one slot
- no owned slime may exist outside slot storage in Phase B/C

### Economy invariants
- `softCurrency >= 0`
- purchase success = exact debit
- sell success = exact resale credit
- delete = zero credit
- no slime creation if purchase fails
- no debit without successful owned slime creation + slot assignment

### Offer invariants
- only one current offer at a time
- re-opening the incubator must not silently change the offer
- offer preview is not an owned slime record

### Save invariants
- every business mutation saves
- load must repair or reset invalid data safely
- slot truth has priority over contradictory slime `slotIndex` metadata

---

## Required Audit Before Patching

Before implementing, inspect the real repo and determine:

1. real app/bootstrap entry point
2. current local save/storage mechanism
3. current source of money truth
4. current incubator offer generation/render path
5. whether slots already exist as real persisted state or only UI
6. whether profile sheet/modal already exists
7. whether rename/sell/delete already exist partially
8. whether reorder exists or is only visual
9. whether current preview object and owned slime object are wrongly coupled
10. where safest minimal integration points are

You must summarize these findings before or alongside your patch work.

---

## Required Implementation Sequence

You must implement in this order unless the repo reality forces a narrowly justified variation.

### Step 1 — Audit and map the real code
Find the real modules/files and current state flow.
Do not patch blindly.

### Step 2 — Install or align canonical save skeleton
Implement or align:
- `createNewSave`
- `loadSave`
- `validateAndRepairSave`
- `saveToStorage`
- canonical root structure

### Step 3 — Connect incubator offer to canonical persistence
Ensure the displayed offer comes from persisted canonical state, not render-time random generation.

### Step 4 — Implement purchase transaction
On successful purchase:
- verify current offer exists
- verify money
- verify empty slot
- debit currency
- create canonical slime record
- insert into first empty slot
- replace current offer with a new one
- save immediately

### Step 5 — Implement offline income
On load:
- compute elapsed time from `lastActiveAt`
- cap at 8 hours
- sum passive income from slot-stored owned slimes
- credit money
- update `lastActiveAt`
- save

### Step 6 — Bind 4 slots to canonical save
Render slot area strictly from canonical slot state.

### Step 7 — Implement profile sheet actions
Support:
- open from occupied slot
- rename
- sell
- delete
- close

### Step 8 — Implement reorder
Persist reorder and synchronize slime `ownership.slotIndex`.

### Step 9 — Add integrity guards and recovery logic
Repair:
- invalid slots
- duplicate slime references
- orphan slimes
- invalid offer
- invalid currency
- corrupted save structure

---

## Exact Behavioral Rules

### Purchase success
A successful purchase must:
- reduce money immediately
- create a canonical owned slime
- place it into the first empty slot
- generate a new current offer
- persist everything immediately

### Purchase refusal: insufficient money
Must:
- not mutate canonical state
- produce a clear UI refusal

### Purchase refusal: full slots
Must:
- not mutate canonical state
- produce a clear UI refusal

### Rename
Must:
- trim input
- reject empty name after trim
- reject too-long names if max length exists in current UI contract
- persist valid rename immediately

### Sell
Must:
- credit exact resale value
- clear the slot
- remove the slime from registry
- save immediately

### Delete
Must:
- clear the slot
- remove the slime from registry
- give no money
- save immediately

### Reorder
Must:
- move or swap slot records
- synchronize slime ownership slot indices
- persist immediately

### `isInPrairie`
Must:
- only affect a minimal informational badge in slot/profile display
- not introduce prairie logic
- not disable income
- not expand into a sub-feature

---

## Repair Rules

If save data is invalid:

- invalid currency -> repair to safe value
- invalid/missing current offer -> regenerate one
- invalid slot array -> reconstruct 4-slot structure
- slot references missing slime -> clear slot
- duplicate slime across slots -> keep one occurrence, clear duplicates
- slime in registry with no slot reference -> remove it in Phase B/C
- slime `slotIndex` contradicting slot array -> repair slime metadata from slot truth

If save is fundamentally unrecoverable:
- create a clean new save
- do not leave the runtime in partial corruption

---

## UI/Gameflow Rules

You must respect the Phase B/C UI/gameflow brief.

Required visible truth:
- current money
- one current incubator offer
- four slots
- profile sheet for occupied slot
- rename / sell / delete actions
- minimal feedback for:
  - insufficient money
  - full slots
  - invalid name
  - sell/delete result
  - offline income gain if positive

The UI must not become:
- a dashboard
- a stat sheet overload
- an inventory expansion
- a fake deep simulation panel

---

## Anti-Regression Rules

Do not:

- refactor the whole app unnecessarily
- create a second parallel ownership model
- keep money in multiple conflicting sources of truth
- regenerate offer on every render/reopen
- persist preview objects as owned slime entities
- leave sold/deleted slimes in registry
- make reorder visual-only
- introduce backend abstractions
- implement prairie or social scope by stealth

Patch minimally but correctly.

---

## Required Validation

You must validate against runtime behavior, not just static reasoning.

At minimum verify:

### Phase B
- new save bootstrap works
- current offer persists across reload/reopen
- purchase succeeds only when valid
- insufficient money refusal works
- full slots refusal works
- first empty slot auto-fill works
- new offer appears after successful purchase
- offline income applies on reload
- offline income cap is 8 hours
- no duplicate slimes appear after repeated purchases

### Phase C
- occupied slot opens correct profile sheet
- empty slot does not break UI
- rename persists across reload
- sell credits correctly and frees slot
- delete frees slot without credit
- reorder persists across reload
- `isInPrairie` badge appears only when true
- sold/deleted slimes are fully removed

### Recovery
- corrupted or partial save does not hard-crash the app
- slot/slime inconsistencies are repaired or safely reset

---

## Required Deliverable Format

Your output must not be vague.

You must return:

### 1. Audit summary
- real files/modules found
- current architecture findings
- mismatches versus brief

### 2. Implementation summary
- exactly what you changed
- why each change was necessary

### 3. File-by-file patch list
- every modified file
- every created file
- brief purpose of each

### 4. Runtime validation summary
- what was actually tested
- what passed
- what remains uncertain

### 5. Risks / follow-ups
- any remaining limitations
- any tightly scoped recommendation for next pass

Do not declare full success unless runtime behavior genuinely supports it.

---

## Hard Constraint

Your mission is to make **Phase B/C locally true**.

Not aesthetically broader.
Not architecturally grander.
Not backend-ready.
Not socially expanded.

Just:
- buy
- own
- persist
- earn
- inspect
- rename
- sell
- delete
- reorder

Safely, cleanly, and without regression.
