# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A classic Tetris game implemented in vanilla JavaScript, HTML5 Canvas, and CSS. No dependencies, no build step, no package.json — just static files.

## Running the game

Open `index.html` directly in a browser, or serve it locally:

```bash
python3 -m http.server 8000
# or
npx serve .
```

There is no build, lint, or test tooling in this repo.

## Architecture

Everything lives in three files that cooperate directly via the DOM — there is no module system or bundler:

- `index.html` — DOM structure: the main `<canvas id="board">` (300×600, 10×20 grid of 30px blocks), a `<canvas id="next-canvas">` for the next-piece preview, HUD spans (`#score`, `#lines`, `#level`), and the pause/game-over `#overlay`.
- `style.css` — dark/retro arcade visual theme.
- `game.js` — all game logic, structured around a small set of global mutable state variables (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropAccum`, `dropInterval`, `animId`) rather than a class or state object. Any change to game behavior likely touches several of the functions below since they share this state directly.

Key mechanics in `game.js`:

- **Board model**: `ROWS × COLS` matrix where each cell is `0` (empty) or a color index `1–7` identifying which piece type locked there.
- **Pieces**: defined as square matrices in `PIECES`. Rotation is done via `rotateCW` (transpose + reverse), not by storing pre-rotated states.
- **Collision** (`collide`): checks board bounds and overlap with locked cells.
- **Wall kicks** (`tryRotate`): after rotating, tries offsets `[0, -1, 1, -2, 2]` before giving up on the rotation.
- **Game loop** (`loop`): driven by `requestAnimationFrame`; accumulates elapsed time in `dropAccum` and advances the piece when it exceeds `dropInterval`.
- **Line clearing** (`clearLines`): scans bottom-to-top, splices full rows out and unshifts empty rows at the top; note the `r++` after a splice to re-check the same row index against the shifted board.
- **Scoring**: `LINE_SCORES = [0, 100, 300, 500, 800]` multiplied by current `level`; hard drop adds 2 points per cell dropped, soft drop adds 1 point per row.
- **Leveling/speed**: level increases every 10 lines; `dropInterval = max(100, 1000 - (level-1)*90)`.
- **Ghost piece** (`ghostY`): projects the current piece straight down to its landing row, drawn at `globalAlpha = 0.2`.
- **Game over**: triggered in `spawn()` when a freshly spawned piece immediately collides.

Tunable constants at the top of `game.js`: `COLS`, `ROWS`, `BLOCK`, `COLORS`, `LINE_SCORES`, and the initial `dropInterval` in `init()`. If `COLS`/`ROWS`/`BLOCK` change, the `#board` canvas `width`/`height` in `index.html` must be updated to match (`COLS×BLOCK` and `ROWS×BLOCK`).
