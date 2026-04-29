# Tetris

A browser-based Tetris game implemented as a single HTML file with inline CSS and JavaScript.

## Project Overview

This is a standalone, client-side Tetris clone that requires no build step, no external dependencies, and no backend. The entire application lives in a single `index.html` file that can be opened directly in any modern web browser.

The game supports three play modes, two visual themes, and bilingual UI (English / Simplified Chinese).

## Technology Stack

- **HTML5** — semantic markup, Canvas API for rendering
- **CSS3** — custom properties for theming, Flexbox for layout
- **Vanilla JavaScript (ES6+)** — game logic, rendering loop, input handling
- **No frameworks, no build tools, no package manager**

## File Structure

```
.
└── index.html        # Single file containing markup, styles, and game logic
```

## How to Run

Open `index.html` directly in a web browser, or serve it with any static file server:

```bash
# Python 3
python -m http.server 8000

# Node.js (npx serve)
npx serve .

# VS Code Live Server extension
```

Then navigate to `http://localhost:8000`.

## Code Organization

Everything is inline inside `index.html`:

1. **`<style>`** (lines 7–183)  
   CSS custom properties drive the two themes (`classic` and `pink`). All UI layout (menu, game panels, overlays) is styled here.

2. **`<body>` markup** (lines 185–257)  
   Three main screens: main menu, game container (canvas + side panels), and overlay (pause / game over).

3. **`<script>`** (lines 258–885)  
   JavaScript is grouped into the following logical sections:

   | Section | Description |
   |---------|-------------|
   | I18N | Translation strings for `en` and `zh` |
   | Game Constants | Board size (`10×20`), block size (`30 px`), piece shapes, SRS-like kick table, theme color maps |
   | State | Board array, current piece, hold, next queue, score, level, timers, DAS/soft-drop state |
   | Logic | Board creation, 7-bag randomizer, piece spawning, rotation, collision detection, line clears, hold, ghost piece |
   | Draw | Canvas rendering for the playfield, ghost piece, hold preview, and next-piece queue |
   | Game Loop | `requestAnimationFrame` loop handling gravity, DAS, soft drop, and time limits |
   | Controls | Keyboard input (`ArrowLeft`, `ArrowRight`, `ArrowDown`, `ArrowUp`, `Space`, `Z`, `C`, `P`, `Escape`) |
   | Modes | `startMode`, `endGame`, `retryMode`, `backToMenu` |

### Game Modes

| Mode | Goal | End Condition |
|------|------|---------------|
| `infinite` | Play endlessly for a high score | Game over when stack reaches the top |
| `time` | Score as many points as possible in 100 seconds | Timer expires or stack reaches the top |
| `sprint` | Clear 40 lines as fast as possible | 40 lines cleared or stack reaches the top |

### Controls

| Key | Action |
|-----|--------|
| `← / →` | Move left / right |
| `↑` | Rotate clockwise |
| `Z` | Rotate counter-clockwise |
| `↓` | Soft drop |
| `Space` | Hard drop |
| `C` | Hold piece |
| `P` / `Escape` | Pause / unpause |
| `Escape` (on game over) | Return to main menu |

### Mechanics

- **7-bag randomizer** — pieces are drawn from a shuffled bag of all seven tetrominoes.
- **Super Rotation System (SRS) kicks** — basic wall-kick table is used for rotation attempts.
- **Ghost piece** — shows where the current piece will lock.
- **DAS** (Delayed Auto-Shift) — `170 ms` delay, `50 ms` repeat.
- **Soft drop** — `50 ms` repeat interval.
- **Scoring** — standard guideline scoring with level multiplier, back-to-back Tetris bonus (`1.5×`), and combo bonus.
- **Leveling** — level increases every 10 lines cleared; gravity speeds up by `80 ms` per level (minimum `50 ms`).

## Code Style Guidelines

- The project uses plain JavaScript with no transpiler or linter configured.
- Prefer `const`/`let`; avoid `var`.
- Global state variables are declared near the top of the script section.
- Canvas drawing helpers (`drawBlock`, `drawMini`) are kept small and reusable.
- When making changes, keep all code inline in `index.html` to preserve the single-file architecture.

## Testing

There is no automated test suite. All testing is manual:

1. Open `index.html` in a browser.
2. Cycle through all three game modes.
3. Verify piece movement, rotation, wall kicks, hold, hard drop, and line clears.
4. Switch languages and themes from the main menu.
5. Confirm pause/resume, retry, and "back to menu" flows work correctly.

## Deployment

Because the project is a single static HTML file, deployment is trivial:

- Copy `index.html` to any static hosting service (GitHub Pages, Netlify, Vercel, S3, etc.).
- No environment variables, build steps, or server-side runtime are required.

## Security Considerations

- No user input is persisted or sent over the network.
- No external resources (scripts, fonts, images) are loaded.
- The game runs entirely in the browser sandbox with no special permissions.

## Agent Environment Notes

### Network / Proxy Configuration

This development machine routes internet traffic through **Clash Verge** (system proxy `127.0.0.1:20000`).

- **Browsers and PowerShell** automatically use the system proxy and can reach GitHub without issues.
- **Git does NOT automatically use the system proxy.** If you need to push/pull from GitHub, configure Git explicitly:
  ```bash
  git config --global http.proxy http://127.0.0.1:20000
  git config --global https.proxy http://127.0.0.1:20000
  ```
- If Clash is turned off later, unset the proxy so Git continues to work:
  ```bash
  git config --global --unset http.proxy
  git config --global --unset https.proxy
  ```
- The `hosts` file contains hard-coded GitHub IPs (`github.com`, `raw.githubusercontent.com`, etc.) as a DNS fallback.
