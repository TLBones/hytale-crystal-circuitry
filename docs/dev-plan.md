# Crystal Circuitry — Development Plan

Plugin Namespace: `raven`  
Asset/ID Prefix: `cc_`  
Project Name: Crystal Circuitry

## Guiding Constraints
- Not a factory/automation mod (no belts, hoppers, item logistics).
- Focus: redstone-adjacent building functionality (doors, hidden doors, moving walls, lights, puzzles).
- Performance-first: event-driven updates; avoid per-tick scanning.
- Visual clarity: subtle VFX; avoid constant beams/particle spam.

---

## Phase 1 — Project Setup ✅
### Goals
- Stable dev environment and repeatable run loop.
- Plugin loads under correct identity.

### Checklist
- [x] Repo created (fork) and cloned locally
- [x] Gradle builds successfully
- [x] Dev server runs from IntelliJ Run Configuration
- [x] Plugin identity claimed: `raven:CrystalCircuitry`
- [x] Server log confirms plugin is listed/loaded
- [x] Changes committed and pushed to GitHub (rename/claim work)

### Exit Criteria
- You can run server and see `raven:CrystalCircuitry` in logs every time.

---

## Phase 2 — Design & Documentation (CURRENT)
### Goals
- Create a “single source of truth” spec for blocks/items/states/textures/behavior.
- Lock MVP scope and naming conventions before implementation.

### Documents
- [x] `docs/dev-plan.md` (this file)
- [ ] `docs/block-spec.md` (core MVP block/item definitions)
- [ ] `docs/test-plan.md` (repeatable tests + perf checks)

### Checklist
- [ ] Decide MVP block set
- [ ] Define IDs for each block/item using `cc_` prefix
- [ ] Define block states per block (on/off, linked/unlinked, etc.)
- [ ] Define texture list per block (by state)
- [ ] Define interaction rules (inputs → signals → actuators)
- [ ] Define what is explicitly out-of-scope for MVP

### Exit Criteria
- A teammate could create assets from `block-spec.md` without asking questions.
- MVP scope is clear enough to implement without redesign.

---

## Phase 3 — Implementation: Assets
### Goals
- Create all MVP blocks/items in the Asset Editor (or pack format).
- Ensure visuals are readable and consistent.

### Checklist
- [ ] Create texture set for each MVP block (including on/off variants)
- [ ] Define block models (start with simple cubes where possible)
- [ ] Define block state variants (on/off, linked, active)
- [ ] Define crafting recipes (shards → components)
- [ ] Spawn/creative access for testing

### Exit Criteria
- All MVP blocks can be placed in-game and display correct states.

---

## Phase 4 — Implementation: Plugin Logic
### Goals
- Implement signal + actuator logic with event-driven updates.
- Persist links/bindings.

### Checklist
- [ ] Green switch toggles emit signal events
- [ ] Yellow linking: 1-to-1 emitter/receiver pairing
- [ ] White aura: broadcast within radius via receiver registration (no scanning)
- [ ] Actuators: lamp + hidden door respond to signals
- [ ] Save/load: links persist across server restart
- [ ] Performance guardrails: no global ticking loops

### Exit Criteria
- MVP demo works in a fresh world, after a restart, without lag spikes.

---

## Phase 5 — Polish & Submission Prep
### Goals
- Improve usability, feedback, and stability.

### Checklist
- [ ] Add subtle VFX/SFX feedback (toggle pulse, active glow)
- [ ] Add in-game linking UX (simple prompts/indicators)
- [ ] Add documentation: how to use each block
- [ ] Build release artifact (jar/pack)
- [ ] Verify clean install on a second machine or fresh folder

### Exit Criteria
- A new user can install and use the mod from README + in-game cues.

---

## MVP Scope (initial target)
In scope:
- cc_green_switch (toggle input)
- cc_yellow_emitter / cc_yellow_receiver (1-to-1 link)
- cc_white_emitter / cc_white_receiver (broadcast aura)
- cc_lamp (on/off)
- cc_hidden_door (open/close or solid/passable)

Out of scope (MVP):
- Item automation, inventory movers, belts, hoppers
- Complex movers/physics systems
- Advanced logic gates beyond basic toggle (timers/inverters later)
