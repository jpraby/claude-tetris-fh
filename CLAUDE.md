# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Classic Tetris implemented in vanilla JavaScript with the HTML5 Canvas 2D API. No dependencies, no build step, no package manager. The UI and code comments/strings are in Spanish.

## Running

Open `index.html` directly in a browser, or serve statically:

```bash
python3 -m http.server 8000   # then open http://localhost:8000
npx serve .
```

There is no build, lint, or test tooling. Changes to `game.js`/`style.css` take effect on browser reload.

## Architecture

Three files cooperate; all game logic lives in `game.js` (~300 lines, single global scope, `'use strict'`).

- **`index.html`** — DOM structure. Two canvases: `#board` (300×600, the playfield) and `#next-canvas` (120×120, next-piece preview). Panel elements (`#score`, `#lines`, `#level`) and the `#overlay` box (used for both PAUSA and GAME OVER states).
- **`style.css`** — dark/retro arcade theme; overlays use `backdrop-filter`.
- **`game.js`** — model, rendering, and input.

### Key model concepts in `game.js`

- **Board**: `ROWS × COLS` matrix; each cell is `0` (empty) or a color index `1–7`. The color index also encodes piece type — `COLORS` and `PIECES` are parallel 1-indexed arrays (index `0` is `null`).
- **Pieces**: square matrices in `PIECES`. Rotation is transpose + row-reverse (`rotateCW`); `tryRotate` attempts wall kicks in order `[0, -1, 1, -2, 2]` columns.
- **Collision** (`collide`): rejects out-of-bounds and overlap with locked cells; cells above the top (`ny < 0`) are allowed so pieces can spawn/rotate at the ceiling.
- **Game loop** (`loop`): `requestAnimationFrame`-driven; accumulates `dt` into `dropAccum` and steps the piece down once `dropInterval` (ms) is exceeded. `draw()` runs every frame in this order: grid → locked board → ghost (alpha 0.2) → current piece.
- **Lock cycle**: `lockPiece` → `merge` (writes piece into board) → `clearLines` → `spawn`.
- **Level/speed**: level rises every 10 lines; `dropInterval = max(100, 1000 - (level-1)*90)`.
- **Scoring**: `LINE_SCORES` (`[0,100,300,500,800]`) × level for clears; hard drop +2/cell, soft drop +1/row.
- **State reset**: `init()` (re)initializes all state and restarts the loop; it is the restart-button handler and runs on load.

### Important coupling

Canvas dimensions are hardcoded in `index.html` as `width`/`height` attributes. If you change `COLS`, `ROWS`, or `BLOCK` in `game.js`, update `#board` to `COLS*BLOCK × ROWS*BLOCK` in the HTML or rendering will be misaligned.
