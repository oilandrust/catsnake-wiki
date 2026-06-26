# Cat Snake — Gameplay Specification

Cat Snake is a 3D puzzle game played from an isometric view. You control a snake-like cat through discrete, step-by-step moves across handcrafted levels. The goal is to solve each room’s logic—pushing objects, holding switches, and growing when needed—then escape through the exit.

---

## Core loop

1. Enter a level at the snake’s spawn point.
2. Move the snake to manipulate the environment.
3. Satisfy the level’s conditions (usually activating all pressure triggers).
4. Reach the open exit with the snake’s head.
5. Advance to the next level.

Levels are played one at a time. Completing the final level ends the run.

---

## The snake

- The snake is a chain of body segments with a distinct head.
- It moves **one cell at a time** in response to player input.
- The snake **cannot reverse** into its current travel direction (no instant 180° turns).
- While a move is animating, **one additional direction** may be queued for the next step.
- The snake **cannot move while falling**.

### Starting length

Each level defines where the snake spawns and how long it starts. If no custom spawn is set, a default length is used.

---

## Movement

### Horizontal movement (on the floor plane)

Four directions across the ground:

| Player input (keyboard) | Direction |
|-------------------------|-----------|
| Left / A                | Left      |
| Right / D               | Right     |
| Up / W                  | Forward   |
| Down / S                | Back      |

On touch devices, **swipes** map to the same four directions: horizontal swipes for left/right, vertical swipes for forward/back.

### Vertical movement

The snake can move **up** and **down** through 3D space:

| Action | Keyboard | Touch |
|--------|----------|-------|
| Move up | Enter, E, or Space | Double-tap |
| Move down | Q or Right Shift | Double-tap while holding another finger on the screen |

Vertical movement is essential for climbing and descending through the level’s height.

### Climbing over obstacles

When the snake tries to move horizontally into a **wall**, **box**, **its own body**, or (if enabled) the **level edge**, and that move is blocked, the game automatically attempts to **move up one level** instead—provided the space above is free. This lets the snake climb onto ledges and over low barriers without a separate “jump” button.

Direct up/down input always moves vertically; climbing only applies when a horizontal move is blocked.

---

## Level objects

### Walls

Solid obstacles. They can span multiple cells (large pieces of furniture, counters, etc.) and block movement at any height they occupy.

### Boxes

Pushable crates. Rules:

- The snake pushes a box by moving into it.
- A **row of adjacent boxes** in the push direction moves together, like a sliding chain.
- Boxes only move if the far end of the chain has a valid empty destination (or an acceptable tile such as a trigger or exit).
- Boxes **fall** when nothing supports them from below, until they land.
- A box resting on a **trigger** counts as holding it down.

### Food

Pickups that **extend the snake by one segment** when the head moves onto them. The food disappears after being eaten. A longer snake can reach switches, block paths, or bridge gaps in new ways.

### Triggers (pressure pads)

Floor switches that depress when **either the snake or a box** occupies them.

- Triggers start **released** (raised).
- They stay pressed while something is on them.
- **All triggers in a level must be pressed at the same time** to open the exit.

Design implication: you may need to push boxes onto pads, hold pads with your body, or arrange a chain of objects so every switch stays down before you can leave.

### Exit (goal)

The level exit is a special tile that acts as the win condition.

**Inactive (closed)**  
- Shown with a “closed” appearance.  
- The snake cannot complete the level through it.

**Active (open)**  
- Opens when every trigger is pressed.  
- Shows an “open” appearance and a **beam of light** rising above it.  
- Remains open while the snake is performing the exit sequence, even if triggers later release.

**Completing the level**  
- The **head** of the snake must move onto the active exit.  
- The snake then **descends through the exit**, segment by segment, until it has fully left the level.  
- The next level loads automatically.

---

## Gravity and falling

The world uses gravity:

- If the snake has **no support** beneath it and there is empty space below, it **falls**.
- Falling is animated; the snake drops until it lands on something solid (wall, box, floor, food tile, etc.).
- If the snake falls **far below the playable area** (into the void), the move is treated as a failure and **undo is applied automatically** to restore the prior state.
- Boxes follow the same falling rules and can land on triggers or other surfaces.

Falling counts as a distinct action in the undo history: a safe landing can be undone like any other move; a void fall triggers undo immediately.

---

## Level bounds (optional)

Some levels restrict how far the snake can travel **horizontally**. When this option is enabled:

- The snake cannot walk off the sides of the level’s footprint (left/right/forward/back at the ground level).
- Hitting the boundary behaves like hitting a wall: the snake may **climb upward** instead if the space above is clear.
- **Vertical movement is not restricted**—the snake can still move up and down freely, including above or below the bounded footprint.

Levels without this option allow horizontal travel beyond the main layout where empty space exists.

---

## Undo

The player can **undo the last action** (keyboard: Backspace; touch: Undo button in the top-right).

Undo reverses, in order:

- Snake moves (including queued moves not yet taken back individually—one undo step per recorded action)
- Box pushes
- Eating food (restores the food and removes the grown segment)
- Falls (return to position before the fall began)
- Starting a level exit (cancels the exit and restores the snake on the level)

Undo is **disabled during the exit animation** and after a level is marked complete. It is intended for experimentation and recovery from mistakes, not for rewinding after victory.

---

## Input summary

| Action | Keyboard | Touch |
|--------|----------|-------|
| Move left / right / forward / back | Arrow keys or WASD | Swipe |
| Move up | Enter, E, Space | Double-tap |
| Move down | Q, Right Shift | Double-tap + hold second finger |
| Undo | Backspace | Undo button |

Swipes and taps that start over UI elements (such as the Undo button) do not move the snake.

---

## Level progression

- The game consists of a **sequence of levels**, each in its own scene.
- After the exit animation finishes, the **next level loads** automatically.
- When the last level is cleared, the run ends (no further level to load).

Individual levels may include themed geometry (kitchens, tables, appliances, etc.) but share the same rules above.

---

## Design intent

Cat Snake levels are **spatial logic puzzles**, not reflex arcade games:

- **Triggers + exit** — classic “hold all switches, then escape” structure.
- **Boxes** — block paths, hold pads, or must be pushed out of the way.
- **Food** — optional growth for reach or weight on switches.
- **Height** — climbing and falling add a third dimension to routing.
- **Undo** — encourages trying sequences without fear of soft-locking.

The cat snake should feel like a flexible body navigating a diorama: every move is deliberate, mistakes are recoverable, and the satisfaction comes from seeing the room “click” when the last trigger stays down and the exit opens.
