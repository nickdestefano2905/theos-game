# CLAUDE.md — Theo's Game

## Project Overview

**Theo's Game — Survive to 30!** is a browser-based 2D side-scrolling platformer set in a cartoonish Boston. The player ages from childhood to adulthood while dodging monsters (Bigfoot, Nessie, Godzilla) and collecting coins. Victory is reaching age 30.

## Tech Stack

- **Pure vanilla JavaScript** — no frameworks, no dependencies
- **HTML5 Canvas 2D** for all rendering
- **Single file**: `index.html` (~1,750 lines) contains all HTML, CSS, and JavaScript
- **No build system** — open `index.html` directly in a browser to run

## Running the Game

Open `index.html` in any modern browser. No server, bundler, or install step needed.

## Code Architecture

### File Structure

The entire game lives in `index.html`. Code is organized into clearly commented sections:

1. **Constants** — `GRAVITY`, `GROUND_Y_OFFSET`, `MAX_FALL_SPEED`, etc.
2. **Game State** — global variables for player, entities, camera, UI state
3. **Input Handling** — keyboard (arrows/WASD/space) and mobile touch controls
4. **Player Logic** — physics, movement, collision, animation, aging
5. **Utility Functions** — `rectOverlap()`, `lerp()`, helpers
6. **World Generation** — procedural buildings, platforms, enemies, coins, water zones
7. **Update Loop** — per-frame game logic (`update(dt)`)
8. **Render/Draw** — layered rendering pipeline (`draw()`)
9. **UI** — HUD (age bar, coins, lives), menus (start, game over, victory)
10. **Game Loop** — `requestAnimationFrame` driven (`gameLoop(timestamp)`)

### Key Patterns

- **State machine**: `gameState` = `'menu'` | `'playing'` | `'gameover'` | `'victory'`
- **Entity arrays**: `platforms[]`, `buildings[]`, `enemies[]`, `coins[]`, `particles[]`, `waterZones[]`
- **Factory functions**: `createBigfoot()`, `createNessie()`, `createGodzilla()`
- **Procedural generation**: world generates in 1200px chunks ahead of the camera
- **AABB collision detection**: `rectOverlap()` for all entity interactions
- **Camera**: lerp-smoothed viewport tracking the player

### Game Loop

```
requestAnimationFrame → gameLoop(timestamp) → update(dt) → draw()
```

### Rendering Order (back to front)

1. Sky gradient (shifts color with age)
2. Clouds (parallax)
3. Buildings (with windows)
4. Platforms
5. Water zones with waves
6. Coins
7. Enemies
8. Player
9. Particles
10. Ground/sidewalk
11. UI overlay

## Coding Conventions

- **camelCase** for variables and functions
- **UPPER_CASE** for constants
- **2-space indentation**
- **Section dividers**: `// --- Section Name ---`
- **Descriptive names**: `invincibleTimer`, `difficultyMult`, `gameState`
- **No external dependencies** — use only native browser APIs

## Game Mechanics Reference

| Mechanic | Details |
|---|---|
| Aging | 10 seconds = 1 year; goal = age 30 |
| Difficulty | `1 + age * 0.05` multiplier |
| Lives | Start with 1; earn extra every 100 coins |
| Invincibility | 2.5s after damage or respawn |
| Jump | Variable height (release key = lower jump); scales with age |
| Enemies | Bigfoot (walks), Nessie (water/bobs), Godzilla (stomps, age 5+) |

## Development Guidelines

- Keep everything in the single `index.html` file — do not split into separate files
- Test changes by opening the file in a browser and playing through
- No linter or formatter is configured; maintain existing style manually
- No test framework exists; all testing is manual play testing
- Mobile touch controls are supported — test on mobile viewport when modifying input handling
