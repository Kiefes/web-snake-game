# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A single-page Snake game implemented entirely in [index.html](index.html) — HTML, CSS, and JavaScript are inlined in one file with no build step, dependencies, or package manager.

## Running the game

Open `index.html` directly in a browser, or serve it locally:

```bash
python3 -m http.server 8123
```

Then visit `http://localhost:8123`. There is no build, lint, or test tooling in this project.

## Architecture

Everything lives in `index.html`, structured in three inline sections:

- **Markup**: a `<canvas>` for the game field, a score display, and a `#game-over` overlay (hidden by default, shown via a `.visible` class).
- **Styles**: dark theme, canvas sized via `width`/`height` attributes (400×400), overlay absolutely positioned over the canvas.
- **Script**: a single self-contained game loop with no external state management:
  - Grid-based movement: the canvas is divided into a `TILE_COUNT × TILE_COUNT` grid (`GRID_SIZE = 20`); the snake is an array of `{x, y}` grid cells.
  - `init()` resets all game state (snake, direction, score, food) and starts a `setInterval` loop (`tick`) driving the game at `GAME_SPEED_MS`.
  - `tick()` advances the snake head by the current direction, checks wall/self collision, handles food consumption (grow + rescore) or normal movement (pop tail), then calls `draw()`.
  - `direction` vs. `nextDirection`: keydown handlers write to `nextDirection`, which is only applied to `direction` at the start of the next `tick()`. This prevents reversing directly into the snake's own body within a single frame.
  - Collision (wall or self) calls `endGame()`, which stops the interval and shows the game-over overlay with the final score.
  - The "Neu starten" button re-runs `init()` to restart.
  - The highscore is persisted across sessions in `localStorage` under `HIGHSCORE_KEY` (`'snake-highscore'`), loaded once into the `highscore` variable on script load. `endGame()` compares the final score against it, updates `localStorage` and the `#highscore` display when beaten, and flags the game-over message with "Neuer Highscore!".
  - Snake/food colors are never hardcoded in `draw()` — they read from the `colors` object (`{ head, body, food }`), which loads from `localStorage` under `COLORS_KEY` (`'snake-colors'`, falling back to `DEFAULT_COLORS`) via `loadColors()`. The `#settings-panel` (toggled by `#settings-btn`) exposes three `<input type="color">` fields; each fires `draw()` directly on `input` so a color change is visible immediately, independent of the `tick()` loop, and `saveColors()` persists it.
  - `draw()` also renders a dot per grid cell (`GRID_DOT_COLOR`) before the food/snake, marking the grid the snake moves on against the light `PLAYFIELD_BG`.

When modifying gameplay (speed, grid size, scoring), all relevant constants (`GRID_SIZE`, `TILE_COUNT`, `GAME_SPEED_MS`) are declared together near the top of the `<script>` block.

## Analytics

`index.html` loads Vercel Speed Insights via a `<script defer src="/_vercel/speed-insights/script.js">` tag (no npm package — kept dependency-free per the project's single-file constraint). That path only resolves on Vercel; expect a harmless 404 for it when serving locally.
