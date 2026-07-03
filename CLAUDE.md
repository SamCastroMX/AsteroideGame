# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A clone of the arcade classic **Asteroids**, implemented in vanilla HTML5 Canvas + ES6 JavaScript. No build tools, no bundler, no package.json, no dependencies — just `index.html` and `game.js`.

## Running

Open `index.html` directly in a browser, or serve it locally:

```bash
npx serve .
```

There is no build, lint, or test step — there are no config files or package.json in this repo. Verify changes by opening the page in a browser and playing.

## Architecture

Everything lives in `game.js` as a single file, organized into sections (marked with `── Section ──` comments) in this order: Input → Utils → Bullet → Asteroid → Ship → Particle → game state → update → draw → main loop.

- **Coordinate space**: fixed `W=800`, `H=600` canvas. All entities wrap toroidally at the edges via the `wrap()` util — there are no walls.
- **Entity pattern**: `Bullet`, `Asteroid`, `Ship`, `Particle` are plain classes each with `update(dt)` and `draw()`. Dead entities set `this.dead = true`; arrays are pruned each frame with `.filter(e => !e.dead)` rather than splicing in place.
- **Game state** is a set of module-level `let` variables (`ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`, `state`, `deadTimer`) reset by `initGame()` / `nextLevel()`. `state` is one of `'playing' | 'dead' | 'gameover'` and gates behavior in `update()`.
- **Input**: `keys[code]` tracks held keys; `justPressed[code]` + `pressed(code)` gives one-shot "key down this frame" checks (used for firing and restart) so holding Space doesn't repeat-fire faster than the cooldown already allows.
- **Collision**: simple circle-radius distance checks (`dist()` + `.radius`), done in `update()` for bullet-vs-asteroid and ship-vs-asteroid.
- **Asteroids** split recursively: size 3 → two size-2 → two size-1 → destroyed. Each size has its own radius/speed/points in the parallel `RADII`/`SPEEDS`/`POINTS` arrays indexed by size.
- **Main loop**: `requestAnimationFrame(loop)` computes `dt` in seconds (clamped to 0.05 max to avoid huge steps after a tab switch), then calls `update(dt)` then `draw()`.

## Notes

- Comments and UI text (score labels, game-over text) are in Spanish; keep new in-game text consistent with that.
- README.md currently describes power-ups and a "shooting star" asteroid type that were removed from `game.js` (see git history) — don't treat the README as a source of truth for current features.
