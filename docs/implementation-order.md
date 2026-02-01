# Crystal Circuitry — Implementation Order & Architecture Checklist (MVP)

Namespace: `raven`  
ID Prefix: `cc_`  
Project: Crystal Circuitry  
Applies To: MVP only

This document defines **what to implement, in what order, and why**, to deliver the Crystal Circuitry MVP with minimal refactoring, strong test coverage, and stable performance.

Guiding rule: **Build core logic first, prove it with tests, then integrate with the game — but ensure assets exist early so you can validate in-world quickly.**

---

## Guiding Architecture Principles

- Event-driven only (no ticking, no polling)
- Core logic must be testable without a running server
- World-facing code should be thin adapters around core systems
- State must be explicit and serializable
- UI drives behavior; UI must not contain business logic
- Asset work should be incremental and testable: start with placeholders, then iterate

---

## Architecture Layers

Implementation is divided into five layers:

0. Asset Pack Layer (blocks/items/models/textures/blockstates)
1. Core Logic Layer (pure Java, unit-tested)
2. Persistence Layer (save/load safety)
3. World Integration Layer (blocks, UI, events)
4. Polish & Lockdown Layer (cleanup, submission readiness)

Do not mix responsibilities across layers.

---

## Phase 0 — Asset Pack Foundation (Required Before Gameplay Integration)

**Goal:** Ensure every MVP block exists in-game with basic visuals and the required block states, even if logic is not yet wired.

### 0.1 Define Asset IDs and Folder Structure

Create asset IDs (MVP):

- `cc_green_switch`
- `cc_yellow_emitter`
- `cc_yellow_receiver`
- `cc_white_emitter`
- `cc_white_receiver`
- `cc_lamp`
- `cc_door_anchor`

Confirm your pack namespace remains consistent with plugin identity (`raven`).

---

### 0.2 Placeholder Textures and Models

Create minimal textures for each block, even if they are simple placeholders:

- `cc_green_switch_off`, `cc_green_switch_on`
- `cc_yellow_emitter_unlinked`, `cc_yellow_emitter_linked`, `cc_yellow_emitter_active`
- `cc_yellow_receiver_unlinked`, `cc_yellow_receiver_linked`, `cc_yellow_receiver_active`
- `cc_white_emitter_inactive`, `cc_white_emitter_active`
- `cc_white_receiver_inactive`, `cc_white_receiver_active`
- `cc_lamp_off`, `cc_lamp_on`
- `cc_door_anchor_off`, `cc_door_anchor_on`

Models:
- MVP default: simple cube models for all blocks unless a special shape is needed.
- Any non-cube model is optional and can be added during polish.

---

### 0.3 Block Definitions

Create block definitions for each MVP block:
- can be placed in-world
- appears in creative inventory (or your dev access mechanism)
- has correct default state

---

### 0.4 Block State Variants (Critical)

Define block state toggles required by `block-spec.md`:

- `cc_green_switch`: `off/on`
- `cc_yellow_emitter`: `unlinked/linked_inactive/linked_active`
- `cc_yellow_receiver`: `unlinked/linked_inactive/linked_active`
- `cc_white_emitter`: `inactive/active`
- `cc_white_receiver`: `inactive/active`
- `cc_lamp`: `off/on`
- `cc_door_anchor`: `off/on`

Asset-layer expectation:
- The block state can visually change (texture/model swap) when state changes.
- Do not implement signal logic here; just ensure the state variants exist and render.

---

### 0.5 Asset Smoke Test (In-World)

Before writing any logic:
- Place each block in-world.
- Verify textures/models display.
- Verify each block has a valid default state.
- Confirm that switching the state manually (via debug/admin if available) changes visuals correctly.

Exit criteria for Phase 0:
- All MVP blocks exist, render, and support their required state variants.

---

## Phase 1 — Core Logic Layer (No Game Code)

**Goal:** Implement and validate all signal, frequency, and constraint logic without referencing Hytale APIs.

### 1.1 Core Packages

Create the following packages:

- `raven.crystalcircuitry.signal`
- `raven.crystalcircuitry.frequency`
- `raven.crystalcircuitry.link`
- `raven.crystalcircuitry.constraints`

Code in these packages must not reference:
- world objects
- blocks
- players
- UI
- server lifecycle

---

### 1.2 Frequency Model

Implement the frequency system:

- Frequency ID (numeric or UUID-backed)
- `ChannelType` enum:
    - `YELLOW`
    - `WHITE`
- `Frequency` object containing:
    - frequency ID
    - channel type
    - active/inactive state
    - owning emitter identifier

Rules enforced here:
- Frequencies are created explicitly (never on placement)
- Frequencies are deleted on reset
- Yellow and White channels never overlap

Unit tests required before moving on.

---

### 1.3 Signal State Model

Implement signal state handling:

- `SignalState` enum:
    - `ON`
    - `OFF`
- Explicit state transitions only
- No ticking or time-based logic

Unit tests:
- OFF → ON → OFF transitions
- No duplicate events
- Idle state produces no output

---

### 1.4 Linking & Constraint Logic

Implement pure logic for:

- Yellow linking (1 emitter ↔ 1 receiver)
- White broadcast membership (1 emitter → many receivers)
- Door Anchor constraints (maximum of 2 doors)
- Discovery/range checks (Yellow 100 radius, White 25 radius, Door 5 radius)

Constraints enforced here:
- Maximum link counts
- Distance checks (math-only, no world objects)
- Safe unlinking when entities are removed

Unit tests must include:
- invalid link rejection
- deterministic rebinding behavior
- cleanup on removal/reset

---

## Phase 2 — Persistence Layer

**Goal:** Ensure save/load correctness before integrating gameplay.

### 2.1 Serializable Data Models

Create serializable representations for:

- Frequencies
- Emitter state
- Receiver bindings
- Door Anchor links

Rules:
- No direct references to world objects
- Use IDs only (coordinates, block IDs, UUIDs)

---

### 2.2 Persistence Tests

Write automated tests for:

- serialize → deserialize round trips
- missing or partially corrupted data
- orphaned references

Exit criteria:
- Corrupted data never crashes the plugin
- Invalid references are discarded safely

---

## Phase 3 — World Integration Layer

**Goal:** Wire core logic into real blocks, UI, and blockstate changes.

### 3.1 Block Registration & Placement

Ensure each asset-defined block is registered/usable in-world via the plugin.

---

### 3.2 Blockstate Wiring (Visual + Logic Bridge)

Implement the bridge between logic and visuals:
- When logic state changes, update the corresponding block state so visuals change.

Examples:
- Switch toggled → set `cc_green_switch` state `off/on`
- Lamp receives signal → set `cc_lamp` state `off/on`
- Receiver linked/unlinked → update `linked_*` states

Rule:
- Block state updates must be event-driven.
- No constant polling to “keep visuals correct.”

---

### 3.3 UI Wiring

Implement UI screens for:

- Yellow Emitters
- Yellow Receivers
- White Emitters
- White Receivers
- Door Anchors

UI rules:
- No automatic frequency generation
- Buttons enabled/disabled based on state
- Receivers only show valid, in-range frequencies
- Empty-state message when no frequencies are available

UI must not:
- bypass constraints
- contain business logic

---

### 3.4 In-World Event Flow Validation

Validate in-game behavior:

- Green switch emits exactly one event per toggle
- Emitters generate/reset frequencies only via UI
- Receivers display only in-range, existing frequencies
- Lamps and doors update immediately on signal change
- White receivers obey “any-on / all-off” rule for their tuned frequency network

No background scanning or ticking systems.

---

## Phase 4 — Test Closure

**Goal:** Prove MVP correctness.

### 4.1 Automated Tests

All automated tests must pass:

- signal state tests
- pairing tests
- broadcast tests
- constraint tests
- persistence tests

Run: `./gradlew test`

---

### 4.2 Manual Test Pass

Execute all scenarios defined in `test-plan.md`:

- fresh world
- after server restart
- under moderate load

Fix any:
- desynchronization
- missed updates
- UI inconsistencies
- incorrect block state visuals

---

## Phase 5 — MVP Lock & Cleanup

**Goal:** Freeze scope and prepare for submission.

### 5.1 Cleanup Tasks

- Remove debug logging
- Verify no ticking systems exist
- Confirm memory cleanup on reset/unlink
- Validate unused frequencies are deleted
- Verify block state updates do not spam logs or events

---

### 5.2 MVP Lock Rules

Once MVP is locked:

- No new block types
- No new logic features
- No UX expansion

Future features (post-MVP):
- Crystal Wrench / configuration copy tools
- Timers and logic gates
- Advanced movers or door systems

---

## Completion Criteria

Implementation is complete when:

- All blocks exist and render with correct state variants
- All unit tests pass
- All manual tests pass
- State persists after restart
- Server performance is stable
- Plugin behavior matches all design documents

At this point, Crystal Circuitry MVP is **feature-complete and submission-ready**.

---

## Final Guidance

When unsure during implementation, consult documents in this order:

1. `block-spec.md`
2. `linking-ux.md`
3. `test-plan.md`
4. `implementation-order.md`

If code conflicts with documentation, **fix the code** unless you intentionally revise the design.
