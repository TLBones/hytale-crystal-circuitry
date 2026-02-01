# Crystal Circuitry — Test Plan (MVP)

Namespace: `raven`  
ID Prefix: `cc_`  
Project: Crystal Circuitry  
Applies To: MVP only

This document defines **automated** and **manual** tests required to validate the Crystal Circuitry MVP.

Automated tests verify logic, constraints, and persistence.  
Manual tests verify in-world behavior, visuals, and performance.

---

## Testing Principles

- Core logic **must** be covered by automated tests.
- In-world behavior **must** be validated manually.
- Automated tests must be:
    - deterministic
    - fast
    - runnable headlessly
- Manual tests must be repeatable in a fresh world.
- No test may rely on per-tick polling.

---

## 1. Automated Test Suite (Required)

Automated tests live under:

`src/test/java/raven/crystalcircuitry/`

They are executed via:

`./gradlew test`

These tests do **not**:
- start a server
- load assets
- require a world

They validate pure logic only.

---

### 1.1 Signal State Tests

**Purpose:**  
Validate signal state transitions without world context.

**Covered Logic:**
- Green switch emits correct signal state
- Signal state changes only on explicit toggle
- Idle state emits no events

**Assertions:**
- OFF → ON → OFF transitions behave correctly
- No duplicate events are emitted
- No state mutation without input

---

### 1.2 Yellow Crystal Pairing Tests

**Purpose:**  
Verify strict one-to-one pairing behavior.

**Covered Logic:**
- Emitter binds to only one receiver
- Receiver binds to only one emitter
- Rebinding behavior is deterministic

**Assertions:**
- Invalid pairings are rejected or replaced safely
- No orphaned links remain
- Pairing state serializes correctly

---

### 1.3 White Crystal Broadcast Logic Tests

**Purpose:**  
Validate broadcast resolution rules.

**Covered Logic:**
- Receiver activates if **any** bound emitter is active
- Receiver deactivates only if **all** emitters are inactive

**Assertions:**
- Activation order does not matter
- No race conditions occur
- No lingering active state after deactivation

---

### 1.4 Door Anchor Constraint Tests

**Purpose:**  
Validate door anchor rules without interacting with real door blocks.

**Covered Logic:**
- Maximum of two linked doors
- Distance constraint logic (≤ 5 blocks)
- Safe unlinking behavior

**Assertions:**
- A third door cannot be linked
- Invalid or removed doors are unlinked safely
- Anchor state mirrors signal state only (not door state)

---

### 1.5 Persistence Tests

**Purpose:**  
Ensure save/load safety for all Crystal Circuitry data.

**Covered Logic:**
- Serialization of:
    - Yellow emitter/receiver pairings
    - White receiver registrations
    - Door anchor links
- Deserialization restores only valid state

**Assertions:**
- State survives reload
- Invalid references are discarded safely
- Corrupted data does not crash the plugin

---

## 2. Manual Integration Tests (In-World)

Manual tests validate engine integration and player experience.

---

### 2.1 Environment Setup

**Steps:**
1. Delete the worlds folder
2. Start the dev server
3. Join a fresh world

**Expected:**
- Server boots cleanly
- Plugin `raven:CrystalCircuitry` loads
- No Crystal Circuitry errors appear in logs

---

### 2.2 Green Crystal Switch

**Steps:**
1. Place a Green Crystal Switch
2. Toggle ON and OFF
3. Toggle rapidly several times

**Expected:**
- Immediate state change
- No visual desync
- No idle signal emission

---

### 2.3 Yellow Crystal Circuit

**Blocks:**
- `cc_green_switch`
- `cc_yellow_emitter`
- `cc_yellow_receiver`
- `cc_lamp`

**Steps:**
1. Pair Yellow Emitter to Yellow Receiver
2. Attach a Crystal Lamp
3. Toggle the Green Crystal Switch

**Expected:**
- Lamp mirrors switch state
- Only paired receiver responds
- Unlinked receivers remain inactive

---

### 2.4 White Crystal Broadcast

**Blocks:**
- `cc_white_emitter`
- Two or more `cc_white_receiver`
- `cc_lamp`

**Steps:**
1. Place a White Crystal Emitter
2. Place multiple White Crystal Receivers in range
3. Attach lamps
4. Toggle emitter ON and OFF

**Expected:**
- All receivers activate together
- No delay or flicker

---

### 2.5 Door Anchor

**Blocks:**
- `cc_door_anchor`
- One or two standard doors

**Steps:**
1. Link one door to a Door Anchor
2. Toggle signal ON and OFF
3. Link a second door
4. Toggle again

**Expected:**
- Doors open and close together
- No state desynchronization
- Anchor does not store door state

---

## 3. Persistence Tests (Manual)

### 3.1 Restart Validation

**Steps:**
1. Build a circuit (switch → yellow → lamp, plus door anchor bindings)
2. Shut down the server
3. Restart the server
4. Re-test toggles

**Expected:**
- Links are preserved
- Devices still respond correctly
- No orphaned bindings

---

## 4. Performance Tests (Manual)

### 4.1 Moderate Load

**Steps:**
1. Place ~30 Crystal Lamps
2. Place ~10 Green Crystal Switches
3. Mix Yellow and White circuits
4. Toggle repeatedly

**Expected:**
- No noticeable lag
- No exponential signal propagation
- No log spam

---

## 5. MVP Guardrails (Non-Tests)

The following behaviors are out of scope and must not be implemented in MVP:

- Timers, delays, or inverters
- Tick-based polling
- Animated block movement
- Inventory interaction or item transport
- Complex movers or physics systems

---

## MVP Completion Criteria

The MVP is considered complete when:
- `./gradlew test` passes consistently
- All manual tests pass in a fresh world
- State persists after restart
- No Crystal Circuitry errors appear in logs
- Server performance remains stable
