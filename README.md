# Sokoban Asset Editors

[English](README.md) | [简体中文](README.zh-CN.md)

Two browser-based editors for an e-ink Sokoban game: one for drawing character
animations, one for drawing levels. Each is a single HTML file — open it and it
runs. No dependencies, no build step, no framework.

| File | What it is |
| --- | --- |
| `character.html` | Character animation editor (28×28 pixel art, 4 directions, 12 frames) |
| `map_editor.html` | Level map editor (16×16 grid, with built-in playtest) |

Both handle data only and never touch the game at runtime, so they also work on
their own or in another project.

---

## Character Animation Editor

Edit character sprites pixel by pixel on a 28×28 grid, with a live demo scene on
the right so you can watch the sprite move while you draw.

### Actions and directions

- **4 directions × 3 actions** — Left, Up and Down each have 4 frames (walk 1–2,
  push 3–4). Right is mirrored from Left automatically, so you never draw it
  twice.
- **Linked edits** — editing Left updates Right by mirroring; editing Up 3 and
  Up 4 flips Down 3 and Down 4 vertically.

### Drawing

- **Paint and erase** — left click paints, right click erases; hold and drag for
  a continuous stroke. Brush sizes of 1 px and 4 px. With the eraser selected,
  left click erases too, following the same brush size.
- **Lasso** — drag to make a new selection, Shift-drag to add, Alt-drag to
  subtract. Only the final selection is drawn, so there's no pile of leftover
  outlines.
- **Moving a selection** — drag inside it or use the arrow keys. The pixels float
  above the canvas and never overwrite what's underneath; the content below is
  preserved.
- **Committing** — click the lasso again, switch brush or eraser, change frame,
  copy/cut/mirror, or save. Until then the floating layer stays adjustable.
- **Delete** (or Backspace) removes the pixels inside the selection.

### One-row tools

Copy, Cut, Paste (both follow the actual shape of the selection), Mirror (asks
whether horizontal or vertical), Select All, Undo (`Ctrl + Z`), Restore (back to
the built-in original matrices), Save.

### Test in place

- Arrow keys anywhere on the page make the character in the demo scene take one
  step; the facing changes accordingly. Arrow keys go to the selection first if
  there is one.
- Press and drag inside the demo scene to move in that direction. A drag shorter
  than 12 px counts as a click and does nothing.

### Frame cycling

- Holding the same direction alternates frames 1 and 2, so it reads as walking.
  Releasing starts again from frame 1.
- Changing direction resets to that direction's frame 1, so each direction has a
  complete stepping cycle.
- Frames keep cycling even when a wall or a box blocks the move: the character
  stays put but the legs don't freeze.
- Pushing a box uses frames 3 and 4, alternating between them so a long push
  doesn't hold one pose. Whenever a box is directly ahead the push pose is shown,
  even if it can't move.
- A box that reaches a target turns into a filled square with a cross. It can
  still be pushed off, and the target cell reappears behind it.
- Long-press the demo scene for about half a second to reset everything to the
  starting state.

### Saving

Save writes the 12 frames to local storage and generates a drop-in replacement
for the game's `domain/character.lua`.

---

## Map Editor

Draw levels on a 16×16 grid, then play them on the spot to confirm they're
solvable.

### Tools

- **Six tools** — Floor, Wall, Target, Box, Player, Erase. Click a tool then
  click a cell, or press `1`–`6` to switch.
- **Hold and drag** to paint a whole run — most useful for walls and floors.
- **Right click erases** a cell or a dragged run without switching tools. On
  touch devices just pick Erase.
- **Clicking the same tile type again** turns it back into floor, which makes
  fixing mistakes quick.
- **Only one player** — placing a new one restores the previous cell to floor.
- **Clear means clear** — it also removes the surrounding wall ring, leaving a
  blank sheet. `Ctrl + Z` brings it back.

### Fixed size

Always 16×16, matching the game's 100-level table, so exported maps drop in
without conversion.

### Shortcuts

| Key | Action |
| --- | --- |
| `1` – `6` | Switch tool: floor / wall / target / box / player / erase |
| `↑ ↓ ← →` | Move the cursor while editing; move the character while playtesting |
| `Space` / `Enter` | Paint the current tool on the cursor cell |
| `Delete` / `Backspace` | Clear the cursor cell back to floor |
| `Ctrl + Z` | Undo |
| `Ctrl + Shift + Z` / `Ctrl + Y` | Redo |
| `P` | Enter / leave playtest |
| `W A S D` | Step while playtesting (same as arrow keys) |
| `Esc` | Close a dialog; leave playtest while playtesting |

### Playtest

- Click "Play" or press `P`, then step with arrow keys, WASD, or by swiping on
  the board.
- The step count and the number of boxes not yet on targets update below the
  canvas.
- A box on a target turns into a filled style; all boxes on targets means the
  level is solved.
- A map with a player but no boxes can still be walked around — it simply has no
  win condition.
- Playtesting never writes to game progress. Leaving returns to the editor with
  the map intact.

### Import / export

JSON in and out. The format matches the level table, so a map can be moved
between the editor and the game without any conversion step.

---

## Notes

- Both editors are plain HTML, CSS and JavaScript with no runtime dependencies.
  Open the file directly from disk; no server required.
- Character data is exchanged as `0`/`1` point matrices (one string per row
  inside a JSON object), which keeps it readable and easy to diff.
- The two editors talk to each other through these exported files, so either one
  can be used alone.