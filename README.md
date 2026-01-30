# EMOJI//MASK (Working Title)

A small rhythm-driven prototype where **emojis act as masks**: you don’t attack directly—you **trigger a mask** to “handle” emotions.

---

## Game Concept
In **EMOJI//MASK**, the boss constantly speaks in **emotional dialogue bubbles**.  
You press rhythm input to activate your **emoji mask**, which automatically **clears bubbles** and **earns score**.

**Theme (MASK):** Emojis are modern emotional masks—identity, expression, and power.

---

## Current Version (MVP v0.1)
This build is intentionally minimal so we can ship a complete, playable loop.

**Included**
- Single playable stage (one short track segment)
- Single input (Space)
- Bubble spawning + timeout pressure
- Hit → clear 1 bubble (FIFO) + score
- Win / Fail + Restart

**Not Included (yet)**
- Emoji switching
- Perfect/Good/Miss accuracy scoring (only Hit/Miss)
- Map / multiple stages
- Complex combat, multi-target logic, counters, BPM auto-chart

---

## Controls
- **Space** — Rhythm input (Hit)

---

## How to Play
1. The stage starts immediately (music + bubbles begin).
2. Press **Space** when the rhythm timing feels right (near the hit moment).
3. **Hit** clears **one oldest bubble** (FIFO) and increases your **Score**.
4. If bubbles stay too long, they **timeout** and increase **Overflow** (pressure).
5. Reach the score threshold to win before pressure overwhelms you.

---

## Win / Fail Conditions
### Win
- **Score ≥ SCORE_THRESHOLD**

### Fail (Dual Condition)
You only fail if **both** are true:
- **OverflowCount ≥ OVERFLOW_LIMIT**
- **AND Score < SCORE_THRESHOLD**

If only one condition is met, the game continues.

---

## Tools
- **Engine:** Godot 4.x
- **Language:** GDScript
- **Assets:** Emoji-based visuals (credits below)

> Note: Any Godot 4.x version should work. If you hit compatibility issues, use the same Godot 4.x version across the team.

---

## Run From Source (Developers)
1. Install **Godot 4.x** (standard version, not Mono is fine unless you plan C#).
2. Open Godot → **Import** → select the project folder (the one containing `project.godot`).
3. Open the main scene:
   - `Main.tscn` (or whatever your entry scene is named)
4. Press **F5** (Play Project).

---

## Project Setup Notes (for beginners)
### Input Map
We recommend using an input action instead of hard-coding Space.
- Project → Project Settings → **Input Map**
- Add action: `hit`
- Bind: **Space**

In code, check:
- `Input.is_action_just_pressed("hit")`

### Export (Build) Basics
To export builds, install export templates:
- Editor → **Manage Export Templates** → Install

Then:
1. Project → **Export**
2. Add preset (Windows/macOS/Linux/Web)
3. Export

---

## Credits
- **Design:** TBD
- **Programming:** TBD
- **Art / UX:** TBD
- **Music / SFX:** TBD
- **Third-party assets:** TBD (add license links here)

---

## Known Issues (MVP)
- Timing may feel offset depending on device/audio latency (we may add an offset slider later).
- Balance values (thresholds/intervals) are placeholder and may change.
