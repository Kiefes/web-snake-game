---
name: add-feature
description: Use this whenever the user asks to add, change, or tweak a feature of this Snake game (speed, scoring, obstacles, colors, controls, sound, mobile support, levels, power-ups, etc.), even if they don't say "feature" explicitly — e.g. "make it faster", "add walls that wrap around", "can the snake speed up as it grows". Walks through implementing the change in index.html, testing it in the browser, updating CLAUDE.md when the architecture changed, and committing the result. Scoped to this repo only.
---

# Add a feature to the Snake game

This repo is a single-file game (`index.html`) with no build step, so every change is
a direct edit followed by a manual browser check — there's no test suite or linter to
lean on. This skill is the checklist that keeps that manual process from skipping
steps under time pressure.

## 1. Implement the feature

Read [CLAUDE.md](../../../CLAUDE.md) first if you haven't already this session — it
describes the `init` / `tick` / `draw` loop, the `direction` vs `nextDirection`
pattern (which exists specifically to stop the snake reversing into itself), and the
constants block (`GRID_SIZE`, `TILE_COUNT`, `GAME_SPEED_MS`) near the top of the
`<script>` tag.

When implementing:
- Follow the existing structure rather than introducing a new pattern for something
  the codebase already has a convention for (e.g. new tunable numbers go in the
  constants block next to the existing ones, not hardcoded inline).
- Keep it a plain single-file change — no new files, no dependencies, no build tooling.
  That constraint is the point of this project.
- Match the existing German-labeled UI text (`Punkte`, `Game Over`, `Neu starten`,
  `Steuerung: Pfeiltasten`) if the feature adds user-facing text.

## 2. Test it in the browser

Don't skip this — with no test suite, this is the only verification there is.

1. Start the preview server (config `snake-server` in `.claude/launch.json` runs
   `python3 -m http.server 8123`) and open `http://localhost:8123`.
2. Check the console for errors after load.
3. Exercise the specific feature you changed.
4. Re-check the core loop still works: arrow-key movement in all four directions,
   eating food (score increments, snake grows), wall collision, self collision, and
   that "Neu starten" fully resets state. A change that's isolated-looking (e.g. a
   color or a new constant) can still break `tick()`'s collision math — it's worth
   the thirty seconds to confirm the game is still playable end to end.

If something breaks, fix it and re-test before moving on — don't hand off a broken
build to the commit step.

## 3. Update CLAUDE.md if needed

CLAUDE.md documents the architecture, not a changelog. Update it only if the change
alters what it currently describes: a new constant worth knowing about, a new
concept added to the loop (like `direction`/`nextDirection`), or a structural change
to the markup/style/script split. Skip this step for changes that don't shift the
architecture — a new color, a tweaked speed value, or a new food type doesn't need a
CLAUDE.md update just because it happened.

## 4. Commit

Stage only the files this feature touched (typically `index.html`, and `CLAUDE.md` if
you updated it) — avoid a blanket `git add -A`. Write a commit message in the
repo's existing style: a short imperative summary line describing what the game can
now do, e.g. "Add wrap-around walls" or "Speed up snake as it grows", with an
optional body line if the change needs a sentence of context. Don't push — a local
commit is the end of this workflow.
