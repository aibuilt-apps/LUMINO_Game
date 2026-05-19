# LUMINO — AI Prompt Context

You are working on **LUMINO**, a single-file HTML/CSS/JS Picross (Nonogram) puzzle game.

The entire game lives in one file: `nonogram.html`  
No frameworks, no build steps, no dependencies except two Google Fonts (DM Mono + Fraunces).

---

## What the game currently has

- 8 handcrafted 5×5 puzzles: Heart, Arrow, Diamond, Cat, Tree, Rocket, Crown, Star
- Left click to fill · Right click to mark × · Touch support (tap, long-press, drag)
- Timer, error counter, 3 hints per puzzle
- Auto-validation — clue numbers grey out when a line is fully solved
- Confetti + animated pixel-art reveal on solve
- Best times saved via `window.storage` (Claude artifact storage API)
- Puzzle selector bar with solved-state indicators
- "Next puzzle" flow on the solved card

---

## Design system

| Token | Value | Usage |
|---|---|---|
| `--bg` | `#EEF2EE` | Page background (soft sage) |
| `--surface` | `#F8FAF7` | Cards, cells, buttons |
| `--ink` | `#263327` | Primary text (dark forest) |
| `--ink2` | `#5C7A5E` | Secondary text |
| `--ink3` | `#9DB89F` | Muted / disabled text |
| `--filled` | `#3A5E3D` | Filled cells (calm forest green) |
| `--accent` | `#B8714A` | Errors, hint badge (warm amber) |
| `--accent2` | `#D4A882` | Long-press feedback |
| `--correct` | `#4A8C52` | Solved states (bright green) |
| `--cell` | `56px` | Cell size |
| `--gap` | `5px` | Grid gap |

**Fonts:** Fraunces (display serif, headers + solved card) · DM Mono (monospace, all UI)  
**Aesthetic:** calm, spacious, editorial — soft sage greens, generous whitespace, rounded corners (12px cells), subtle shadows on filled cells and hover states. No gradients.

---

## Key architecture

- **PUZZLES array** — each entry: `{ name, label, grid }` where `grid` is a 2D array of `0`s and `1`s
- Clues are **computed automatically** from the solution grid — never hardcoded
- `state[][]` tracks each cell as `'empty' | 'filled' | 'marked'`
- Filling a wrong cell (`solution === 0`) counts as an error and flashes red — it does **not** fill
- Solve check: every `solution===1` cell must be `'filled'` and no `solution===0` cell may be `'filled'`
- Storage: `window.storage.get/set(key)` — Claude artifact API, async

---

## Touch support (already implemented)

| Gesture | Action |
|---|---|
| Tap | Fill cell (left-click equivalent) |
| Long press 450 ms | Toggle × mark (right-click equivalent) |
| Drag | Sweep-fill multiple correct cells |

Ghost-click prevention: a 300 ms `touchHandled` flag blocks the browser's synthetic `click` after `touchend`.

---

## How to add new puzzles

Add an entry to the `PUZZLES` array at the top of `<script>`:

```js
{
  name: 'Name',
  label: 'emoji description',
  grid: [
    [0,1,0,1,0],
    [1,1,1,1,1],
    // ...
  ]
}
```

Clues and pixel preview generate automatically. Grids can be any size — the layout adapts. For puzzles larger than 5×5, thick border classes (`thick-right`, `thick-bottom`) are applied every 5 cells automatically.

**Solvability rule:** every puzzle must be solvable by logic alone — no guessing required. Test row and column constraints before adding.

---

## What to work on next (backlog)

- [ ] 10×10 puzzles with more complex pixel art
- [ ] Puzzle difficulty badge (Easy / Medium / Hard) based on grid size
- [ ] Puzzle editor UI — draw your own puzzle
- [ ] Color Picross (two fill colors instead of one)
- [ ] Dark mode toggle
- [ ] Streak / statistics screen

---

## Product owner

**Oz Soffy** — Product Manager  
[LinkedIn](https://www.linkedin.com/in/ozsoffy/) · [GitHub](https://github.com/ozsoffy)

---

## Rules for AI assistants

1. Keep everything in the **single `nonogram.html` file** — no separate CSS or JS files.
2. Match the existing design system exactly — tokens, fonts, border-radius, spacing rhythm.
3. Do not add comments unless the logic is genuinely non-obvious.
4. Do not introduce frameworks, bundlers, or external dependencies beyond the two Google Fonts already linked.
5. When adding puzzles, verify logical solvability before including them.
6. Prefer editing existing code over adding new abstractions.
