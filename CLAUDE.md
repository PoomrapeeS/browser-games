# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the games

No build step — open the HTML files directly in a browser:

- Tic Tac Toe: `index.html`
- Shooter: `shooter/index.html`

## Architecture

Two self-contained single-file browser games with no dependencies and no framework.

### Tic Tac Toe (`index.html`)
Vanilla JS with inline CSS. State lives in a single `state` object `{ board, current, done, score }`. `checkWinner()` iterates the 8 `WINS` combinations. DOM is manipulated directly — no virtual DOM. Score persists across games via the `state.score` object but resets on page reload.

### Shooter (`shooter/index.html`)
Canvas 2D game with a fixed 960×600 logical resolution that scales to viewport. Key structural pieces:

- **Game states** (`STATE` enum): `MENU → INTRO → PLAYING → WAVE_CLEAR / LEVEL_CLEAR → GAME_OVER / VICTORY`
- **Classes**: `Player`, `Enemy` (types: `grunt`, `runner`, `brute`), `WaveManager`
- **Bullet pool**: Fixed-size array of 64 reusable bullet objects (`bulletPool`) — no allocations during gameplay
- **Particles**: Simple array pushed to on death/hit, cleaned up in `updateParticles(dt)`
- **Levels**: Declared in the `LEVELS` config array; `WaveManager` builds and shuffles spawn queues per wave

All drawable sprites are procedural canvas primitives — no image assets. `Vec2` is an immutable value class (returns new instances from all operations).

## Git workflow

After completing any meaningful unit of work, commit and push to GitHub so progress is never lost:

```bash
git add <files>
git commit -m "short, descriptive message in imperative mood"
git push
```

Commit messages should describe *what changed and why* in one line (e.g. `add restart button to Tic Tac Toe`, `fix runner charge cooldown reset`). Avoid vague messages like `update` or `fix stuff`.

## Conventions

- All interactive HTML elements carry `data-testid` attributes for automated testing.
- The shooter uses `requestAnimationFrame` with a `dt`-based update loop; max delta is clamped to `0.05s` to prevent tunnelling on tab restore.
