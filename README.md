# Cloth Simulation

An interactive real-time cloth physics simulation running entirely in the browser — no libraries, no build tools, no dependencies. Just open `index.html` and play.

The cloth is modelled as a grid of points connected by distance constraints and simulated with Verlet integration. You can pull, tear, and blow wind across the fabric.

---

## Getting Started

1. Clone or download this repository.
2. Open `index.html` in any modern browser.
3. No server, no install step — it works straight from the file system.

---

## Controls

| Input | Action |
|---|---|
| **Click + Drag** | Grab the nearest cloth point and pull the fabric |
| **T** | Toggle tear mode — move the mouse over the cloth to cut through it |
| **R** | Reset the cloth to its original state |
| **W** | Toggle wind — an oscillating horizontal force that blows across the fabric |

A small HUD in the top-left corner of the screen always shows these controls, along with the current active mode (tear / wind).

---

## How the Physics Work

### Points

The cloth is made up of a **30 × 30 grid of points** (900 in total). Each point has a current position and a previous position. The top row of points is **pinned** — they never move and act as the cloth's hanger.

### Verlet Integration

Instead of tracking velocity directly, the simulation uses **Verlet integration**: velocity is implicitly encoded as the difference between the current position and the previous position. Each frame, the simulation:

1. Computes the implied velocity: `velocity = current − previous`
2. Applies a tiny bit of friction by multiplying velocity by a damping factor (0.99)
3. Moves the point: `new position = current + velocity + acceleration`

Acceleration comes from gravity (always pulling downward) and wind (an optional sinusoidal horizontal push). This approach is numerically stable and very simple to implement.

### Constraints

Every pair of adjacent points (horizontally and vertically) is linked by a **distance constraint** that tries to keep them at their original spacing. Each frame, the solver runs multiple **relaxation passes**:

- Measure how far apart the two points are.
- If they are too far apart, push them closer together; if too close, pull them apart.
- Apply the full correction to whichever endpoint is free to move (pinned points are never adjusted).

Running this process **8 times per frame** makes the cloth feel stiff and natural without requiring complex math.

### Tearing

A constraint can be **torn** in two ways:

- **Manually** — press `T` to enter tear mode, then move the mouse over the cloth.
- **Automatically** — if a segment stretches beyond 60× its rest length (e.g. during an extreme drag), it snaps on its own.

Once a constraint is removed, the two points it connected are free to drift independently, creating the satisfying rip effect.

---

## Extension Ideas

1. **Collision detection** — Add circular or rectangular obstacles (e.g. a ball) that the cloth can drape over and bounce off.
2. **Structural (diagonal) constraints** — Add constraints along the diagonals of each grid cell to resist shearing and make the cloth feel stiffer.
3. **Self-collision** — Detect when cloth points overlap each other and push them apart so the fabric can fold over itself realistically.
4. **Multiple cloth panels** — Spawn several independent cloth meshes and let them interact via shared constraints or collision.
5. **Fire and burn effect** — When a constraint is torn, gradually "burn" the nearby points by reducing their mass, changing their colour, and letting them float upward.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Vanilla JavaScript (ES2020, no transpilation) |
| Rendering | HTML5 Canvas 2D API |
| Animation | `requestAnimationFrame` |
| Dependencies | **None** |
| Build tools | **None** — single static file |

---

## Configuration

All tuneable constants live at the top of the `<script>` block in `index.html`:

| Constant | Default | Effect |
|---|---|---|
| `COLS` / `ROWS` | 30 | Grid dimensions |
| `SPACING` | 18 px | Rest distance between points |
| `GRAVITY` | 0.5 | Downward acceleration per frame |
| `DAMPING` | 0.99 | Velocity retention (lower = more drag) |
| `ITERATIONS` | 8 | Constraint relaxation passes per frame |
| `TEAR_DIST` | 60 | Auto-tear stretch threshold (× rest length) |
| `MOUSE_RADIUS` | 36 px | Interaction radius for drag and tear |
| `WIND_STRENGTH` | 1.2 | Peak horizontal wind force |
| `WIND_SPEED` | 0.03 | Wind oscillation speed (rad/frame) |

---

## Code Structure

Everything lives in `index.html`. The JavaScript is split into three classes:

- **`Point`** — A single mass with current/previous position, a pinned flag, and a Verlet update method.
- **`Constraint`** — A distance link between two Points with a `satisfy()` relaxation method and a `draw()` method that shades the segment based on stretch.
- **`Cloth`** — Builds and owns the full 30 × 30 grid. Exposes `update()`, `draw()`, `reset()`, `nearestPoint()`, and `tearNear()`.

The `main()` function wires up the canvas, handles mouse/touch/keyboard events, and runs the `requestAnimationFrame` loop.
