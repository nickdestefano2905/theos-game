# CLAUDE.md — Theo's Game

## Project Overview

**Theo's Game — Survive to 30!** is a browser-based 2D side-scrolling platformer with two levels. The player ages from childhood to adulthood while dodging monsters and collecting coins. Victory requires surviving to age 30 in both levels.

- **Level 1 — Boston**: City streets with Bigfoot, Nessie, and Godzilla
- **Level 2 — Jungle**: Tropical wilderness with Giant Snakes, Panthers, and King Kong

## Tech Stack

- **Pure vanilla JavaScript** — no frameworks, no dependencies
- **HTML5 Canvas 2D** for all rendering
- **Single file**: `index.html` (~2,500 lines) contains all HTML, CSS, and JavaScript
- **No build system** — open `index.html` directly in a browser to run

## Running the Game

Open `index.html` in any modern browser. No server, bundler, or install step needed.

## Code Architecture

### File Structure

The entire game lives in `index.html`. Code is organized into clearly commented sections:

1. **Constants** — `GRAVITY`, `GROUND_Y_OFFSET`, `MAX_FALL_SPEED`, etc.
2. **Game State** — global variables for player, entities, camera, UI state, `currentLevel`
3. **Input Handling** — keyboard (arrows/WASD/space) and mobile touch controls
4. **Player Logic** — physics, movement, collision, animation, aging
5. **Utility Functions** — `rectOverlap()`, `lerp()`, helpers
6. **World Generation** — `generateBostonWorld()` and `generateJungleWorld()`, dispatched by `generateWorld()`
7. **Enemy Factories** — Boston (`createBigfoot`, `createNessie`, `createGodzilla`) and Jungle (`createSnake`, `createPanther`, `createGorilla`)
8. **Update Loop** — per-frame game logic (`update(dt)`), enemy AI in `updateEnemy()`
9. **Render/Draw** — layered rendering pipeline (`draw()`)
10. **Drawing Functions** — level-aware: `drawBuilding`/`drawJungleStructure`, `drawGround`, `drawPlatform`, enemy drawers
11. **UI** — HUD (age bar, coins, lives, level label), menus (start, game over, level complete, victory)
12. **Game Loop** — `requestAnimationFrame` driven (`gameLoop(timestamp)`)

### Key Patterns

- **State machine**: `gameState` = `'menu'` | `'playing'` | `'gameover'` | `'levelComplete'` | `'victory'`
- **Level system**: `currentLevel` (1 = Boston, 2 = Jungle) controls generation, drawing, and enemy types
- **Entity arrays**: `platforms[]`, `buildings[]`, `enemies[]`, `coinList[]`, `particles[]`, `waterZones[]`
- **Factory functions**: per-level enemy creators
- **Level-aware rendering**: `drawBuilding()`, `drawGround()`, `drawSidewalk()`, `skyColor()` all branch on `currentLevel`
- **Procedural generation**: world generates in 1200px chunks ahead of the camera
- **AABB collision detection**: `rectOverlap()` for all entity interactions
- **Camera**: lerp-smoothed viewport tracking the player

### Game Loop

```
requestAnimationFrame → gameLoop(timestamp) → update(dt) → draw()
```

### Level Flow

```
Menu → Level 1 (Boston, age 0→30) → Level Complete screen → Level 2 (Jungle, age 0→30) → Victory
```

On game over, restart repeats the current level (preserving `currentLevel`).

### Rendering Order (back to front)

1. Sky gradient (shifts color with age; tropical tones in jungle)
2. Clouds (parallax; misty green in jungle)
3. Buildings/Trees (Boston buildings or jungle trees/rocks)
4. Platforms (ledges/floats in Boston; vines/logs/branches in jungle)
5. Water zones (harbor blue in Boston; murky green rivers in jungle)
6. Coins
7. Enemies
8. Player
9. Particles
10. Ground (street in Boston; dirt with grass in jungle)
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
| Aging | 10 seconds = 1 year; goal = age 30 per level |
| Difficulty | `1 + age * 0.05` multiplier |
| Lives | Start with 1 per level; earn extra every 100 coins |
| Invincibility | 2.5s after damage or respawn |
| Jump | Variable height (release key = lower jump); scales with age |
| Boston Enemies | Bigfoot (walks), Nessie (water/bobs), Godzilla (stomps, age 5+) |
| Jungle Enemies | Giant Snake (slithers), Panther (pounces), King Kong (chest-pounds, age 5+) |

## Platform Types by Level

| Boston | Jungle |
|---|---|
| `roof` (building tops) | `branch` (tree canopy) |
| `ledge` (fire escapes) | `vine` (rope with leaves) |
| `float` (wooden planks) | `log` (floating logs) |
| — | `rock` (cliff formations) |

## Development Guidelines

- Keep everything in the single `index.html` file — do not split into separate files
- Test changes by opening the file in a browser and playing through
- No linter or formatter is configured; maintain existing style manually
- No test framework exists; all testing is manual play testing
- Mobile touch controls are supported — test on mobile viewport when modifying input handling
- When adding a new level, follow the pattern: add `generateXWorld()`, enemy factories, draw functions, and update `currentLevel` branching
