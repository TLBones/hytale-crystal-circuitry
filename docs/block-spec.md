# Crystal Circuitry — Block Specification (MVP)

Namespace: `raven`  
ID Prefix: `cc_`  
Project: Crystal Circuitry

This document defines the **minimum viable block set (MVP)** for Crystal Circuitry.
All blocks listed here are required for the first functional release.

---

## Design Rules (Applies to All Blocks)

- All blocks & items use the `cc_` ID prefix.
- All state changes must be event-driven (no ticking).
- Visual feedback must be subtle and readable.
- Blocks should be understandable without external documentation.
- No item movement or automation logic.

---

## 1. Input Blocks

### 1.1 Green Crystal Switch
**ID:** `cc_green_switch`

**Role:**  
Primary player input. Acts as a binary toggle (on/off).

**States:**
- `off`
- `on`

**Behavior:**
- Player interaction toggles state.
- Emits a signal event when state changes.
- Does nothing while idle.

**Textures Required:**
- `cc_green_switch_off`
- `cc_green_switch_on` (subtle glow)

**Notes:**
- No pulse/timer behavior in MVP.
- This is the only direct player-operated logic block in MVP.

---

## 2. Signal Transfer Blocks (Targeted)

### 2.1 Yellow Crystal Emitter
**ID:** `cc_yellow_emitter`

**Role:**  
Sends a signal to exactly one paired Yellow Receiver.

**States:**
- `unlinked`
- `linked_inactive`
- `linked_active`

**Behavior:**
- Can be paired to one `cc_yellow_receiver`.
- When receiving a signal, forwards it to its paired receiver.
- Does not scan for receivers; pairing is explicit.

**Textures Required:**
- `cc_yellow_emitter_unlinked`
- `cc_yellow_emitter_linked`
- `cc_yellow_emitter_active`

---

### 2.2 Yellow Crystal Receiver
**ID:** `cc_yellow_receiver`

**Role:**  
Receives a signal from exactly one paired Yellow Emitter.

**States:**
- `unlinked`
- `linked_inactive`
- `linked_active`

**Behavior:**
- Reacts only to its paired emitter.
- Forwards signal state to any attached actuator.

**Textures Required:**
- `cc_yellow_receiver_unlinked`
- `cc_yellow_receiver_linked`
- `cc_yellow_receiver_active`

---

## 3. Signal Transfer Blocks (Broadcast / Aura)

### 3.1 White Crystal Emitter
**ID:** `cc_white_emitter`

**Role:**  
Broadcasts a signal to all White Receivers within range.

**States:**
- `inactive`
- `active`

**Behavior:**
- Emits signal events to registered receivers within radius.
- Radius is fixed for MVP (configurable later).
- Broadcast occurs only on state change.

**Textures Required:**
- `cc_white_emitter_inactive`
- `cc_white_emitter_active`

**Notes:**
- No per-tick radius scanning.
- Receivers register/unregister themselves.

---

### 3.2 White Crystal Receiver
**ID:** `cc_white_receiver`

**Role:**  
Receives broadcast signals from nearby White Emitters.

**States:**
- `inactive`
- `active`

**Behavior:**
- Activates when any bound emitter broadcasts `on`.
- Deactivates when all bound emitters are inactive.

**Textures Required:**
- `cc_white_receiver_inactive`
- `cc_white_receiver_active`

---

## 4. Actuator Blocks

### 4.1 Crystal Lamp
**ID:** `cc_lamp`

**Role:**  
Simple visual actuator (light on/off).

**States:**
- `off`
- `on`

**Behavior:**
- Turns on when receiving `on` signal.
- Turns off when receiving `off` signal.
- No light flicker or animation in MVP.

**Textures Required:**
- `cc_lamp_off`
- `cc_lamp_on` (emissive)

---

### 4.2 Door Anchor Block
**ID:** `cc_door_anchor`

**Role:**  
Relays a Crystal signal to one or two nearby door blocks, enabling synchronized opening and closing [ ≤ 5 blocks by default ].

**States:**
- `off` 
- `on`


**Behavior:**
- Toggles state on signal change.
- Only affects linked doors.
- Allows up to 2 doors to be linked to a single anchor.

**Textures Required:**
- `cc_door_anchor_off`
- `cc_door_anchor_on` (emissive)

**Notes:**
- MVP implementation may swap block state rather than animate movement.
- Advanced movement systems are out of scope.
- The Door Anchor does not store open/closed intentionally - it will store the signal it is given from a switch and relay that to the doors it's linked to.

---

## 5. Explicitly Out of Scope (MVP)

- Timers, delays, inverters
- Item transport or automation
- Moving block physics
- Multi-block machines
- Energy generation or storage

These may be added in later phases only after MVP stability.

---

## MVP Completion Criteria

The MVP is considered complete when:
- All listed blocks can be placed in-game.
- Signals flow correctly between switches, transmitters, and actuators.
- State persists across server restarts.
- No noticeable performance degradation occurs with dozens of blocks active.
