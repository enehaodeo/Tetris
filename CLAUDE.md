# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## How to Run / Test

```bash
# Serve locally (Python)
python -m http.server 8000
# Then open http://localhost:8000

# Or just open index.html directly in a browser
```

There is no automated test suite. Testing is manual: open in browser, cycle through all three game modes, verify controls, pause/resume, language/theme switching, retry, and back-to-menu flows.

## Architecture

Single-file vanilla browser game — `index.html` contains all markup, CSS, and JavaScript. No build step, no dependencies, no backend.

**`<script>` sections (in order):**
- **I18N** — `I18N` object with `en`/`zh` string tables; `applyLang()` updates every DOM element by ID.
- **Constants** — Board (`10×20`, `BLOCK=30px`), `SHAPES` (7 tetrominoes as 2D arrays), `KICKS` (SRS-like wall kick offsets), `THEMES` (classic/pink color maps).
- **State** — Global mutable state: `board` (ROWS×COLS array), `current` piece, `hold`, `bag`/`nextPieces` (7-bag queue), score/level/lines, DAS/soft-drop timers, mode flags (`running`, `paused`, `gameOver`).
- **Logic** — `newBoard()`, `isValid()`, `lockPiece()`, `clearLines()` (scoring: back-to-back Tetris bonus 1.5×, combo bonus, level multiplier), `tryRotate()` (SRS kicks), `hardDrop()`, `doHold()`.
- **Draw** — `drawBlock()` / `drawMini()` helpers, `draw()` renders board + ghost + current piece to `#game-canvas`, plus hold and next previews on side canvases.
- **Loop** — `requestAnimationFrame`-based `loop()`: gravity timer, DAS auto-repeat, soft-drop repeat, elapsed time tracking (100s limit for time mode).
- **Controls** — `keydown`/`keyup` handlers. DAS direction-switching on keyup (if opposite arrow held, immediately move that way).
- **Modes** — `startMode('infinite'|'time'|'sprint')`, `endGame()`, `retryMode()`, `backToMenu()`.
- **Settings** — DAS delay / ARR persisted to `localStorage` via `saveSettings()`/`loadSettings()`, plus theme and language.

**DOM structure:** Three top-level screens — `#menu-screen` (mode buttons, theme selector, settings panel), `#game-container` (main canvas + two side panels), `#overlay` (pause/game-over overlay). Only one visible at a time.

**Theming:** CSS custom properties on `[data-theme]` attribute drive the two themes. JS themes define block colors and ghost/grid/stroke colors.

**Game modes:** `infinite` (endless, stack-to-top ends), `time` (100s timer), `sprint` (clear 40 lines). Scoring is disabled in sprint mode — only line count and time matter.

## Code Style

- Plain JS, no transpiler/linter. Prefer `const`/`let`.
- Keep all code inline in `index.html` — preserve the single-file architecture.
- Global state variables declared near the top of the `<script>`.

AGENTS.md has additional detail on mechanics (scoring formulas, leveling, DAS parameters) and deployment notes.
