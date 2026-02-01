# Crystal Circuitry — Linking & UX Rules (MVP)

Namespace: `raven`  
ID Prefix: `cc_`  
Project: Crystal Circuitry  
Applies To: MVP only

This document defines how players link and configure Crystal Circuitry blocks using UI-first interactions.
The intent is to prevent invalid actions through interface constraints rather than relying on error sounds, particles, or trial-and-error.


---

## 1. Interaction Standard

### 1.1 Opening Configuration UI
- Press F while targeting a compatible Crystal Circuitry block to open its configuration UI.
- UI is the primary and required interaction method for linking in MVP

### 1.2 UI Feedback Philosophy
- UI must clearly show:
  - current state (on/off, linked/unlinked) 
  - current frequency (or none)
-  Minimal non-UI feedback is used in MVP.
- Invalid actions should be prevented outright by the UI wherever possible.

---

## 2. Frequency System (Shared Rules)

### 2.1 Channel Types
Frequencies are separated by channel type to prevent overlap:
- `YELLOW` channel type (targeted link system)
- `WHITE` channel type (aura/broadcast system)

UI may display these as:
- `Y-<number>` for Yellow
- `W-<number>` for White

Important Notes:
- Internally, channel type is stored as a distinct field (not a suffix requirement).
- Frequencies are never generated automatically. Emitters start unassigned and require explicit player action to create a frequency.


### 2.2 Visibility Rules (Receivers)
Receivers must only display:
- frequencies that exist
- frequencies within receiver discovery range (see sections 3 and 4)
- frequencies that are enabled/active as valid sources

Receivers must never display:
- disabled frequencies
- stale/deleted frequencies
- options to create frequencies

### 2.3 Empty State
If no valid frequencies exist within range, the receiver UI shows:

> No available frequencies in range.

(Exact phrasing can be adjusted, but it must clearly explain why the list is empty.)

### 2.4 Reset Behavior (Emitter Only)
Resetting a frequency:
- deletes the old frequency
- generates a new frequency
- causes any receivers tuned to the old frequency to become unlinked (or show missing/invalid until they retune)

This is required to avoid stale connections and reduce unnecessary memory usage.

---

## 3. Yellow Link System (Targeted)

### 3.1 Discovery Range
Yellow receiver discovery radius:
- **100 blocks** (spherical or block-distance radius; implementation-defined but consistent)

### 3.2 Yellow Emitter UI (`cc_yellow_emitter`)
UI displays:
- Current frequency (or `None` if unassigned)
- Button: **Generate Frequency** (only shown if no frequency exists)
- Button: **Reset Frequency** (only shown if a frequency exists)

Rules:
- Emitter is the only block that can create/reset a Yellow frequency.
- Reset deletes old frequency and generates a new one immediately.

### 3.3 Yellow Receiver UI (`cc_yellow_receiver`)
UI displays:
- List of available Yellow frequencies within 100 blocks
- Selection action: **Tune/Link to Frequency**
- Current tuned frequency (or `None`)

Rules:
- Receiver forces one-to-one behavior (MVP):
    - A receiver can be tuned to only one frequency at a time.
- Receiver cannot generate or reset frequencies.
- Receiver list shows only valid, existing, in-range frequencies.

---

## 4. White Aura System (Broadcast)

### 4.1 Discovery Range
White receiver discovery radius:
- **25 blocks**

### 4.2 White Emitter UI (`cc_white_emitter`)
UI displays:
- Current frequency (or `None` if unassigned)
- Button: **Generate Frequency** (only shown if no frequency exists)
- Button: **Reset Frequency** (only shown if a frequency exists)
- Current state indicator: `active/inactive`

Rules:
- Emitter is the only block that can create/reset a White frequency.
- Reset deletes old frequency and generates a new one immediately.

### 4.3 White Receiver UI (`cc_white_receiver`)
UI displays:
- List of available White frequencies within 25 blocks
- Selection action: **Tune/Link to Frequency**
- Current tuned frequency (or `None`)

Rules:
- Receivers tuned to the same White frequency respond together (broadcast behavior).
- Receiver list shows only valid, existing, in-range frequencies.
- If multiple White emitters exist on different frequencies, receiver choice determines which aura network it belongs to.

---

## 5. Door Anchor Linking

### 5.1 Door Anchor UI (`cc_door_anchor`)
UI displays:
- List of door blocks within range (≤ 5 blocks by default)
- Selection controls to link up to **two** doors
- Current linked doors (0–2)
- Indicator showing whether the anchor is currently `on/off` (signal state)

Rules:
- Door Anchor can link to a maximum of 2 doors.
- Door Anchor can only affect linked doors.
- Door Anchor stores signal state only (not door state).
- If a linked door is removed or becomes invalid, it must be removed from the anchor’s linked list automatically.

### 5.2 Door Eligibility
For MVP, eligible doors are:
- standard door blocks that support open/close state

(Exact door type detection depends on implementation, but the UX contract is: only doors that can be opened/closed are listed.)

---

## 6. Invalid Action Prevention (MVP)

The UI should prevent invalid actions by design:
- Receivers cannot create frequencies
- Receivers cannot see deleted/disabled frequencies
- Door anchor cannot link more than 2 doors
- Door anchor cannot link doors outside range
- Emitters can only reset their own frequency

If an invalid state occurs (e.g., a door is removed), the system resolves it automatically by unlinking and updating the UI.

---

## MVP Completion Criteria for Linking UX

Linking UX is considered complete when:
- Players can reliably create frequencies via emitters
- Receivers can reliably choose only available in-range frequencies
- White and Yellow frequency spaces never overlap
- Door anchor can link up to 2 doors and control them from a signal
- UI prevents invalid actions without requiring particle/sound debugging
- Future tooling (e.g., configuration-copy tools) may extend this system post-MVP.
