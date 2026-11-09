# Sudoku

A lightweight, single-file Sudoku web app built for Safari on iOS. Set in Instrument Serif, with an animated dither-shader background and automatic light and dark themes.

No build step, no framework, no dependencies to install.

## Features

- **Four difficulties:** Easy, Medium, Hard and Expert. Tap the difficulty button to cycle through them.
- **Unique-solution puzzles:** generated on the device for every new game.
- **Stats on the landing screen:** games played, average solve time, favorite difficulty, hardest difficulty solved and solve rate. A personal best (fastest solve on the hardest difficulty solved) sits at the bottom of the screen.
- **Notes:** pencil marks that clear automatically when a number is placed in the same row, column or box.
- **Cell highlighting:** the selected cell, its row, column and box, and every cell with the same digit.
- **Mistake limit:** wrong numbers are never placed. Three mistakes end the game.
- **Timer:** pauses while the tab is hidden.
- **Dither-shader background:** a WebGL radial gradient with ordered dithering and waves that move slowly outwards.
- **System theme:** white to light blue in light mode, black to hot red in dark mode.
- **Animations:** [anime.js](https://animejs.com) transitions between screens, kept subtle.

## Run locally

Open `sudoku.html` in a browser, or serve the folder:

```sh
python3 -m http.server 8000
# then visit http://localhost:8000/sudoku.html
```

## Deploy

Any static host works. For GitHub Pages, rename the file to `index.html`, then go to **Settings → Pages** and publish from the branch root.

## How to play

1. Tap **Start a new game** (tap the difficulty button first to change it).
2. Tap an empty cell, then tap a number below the grid.
3. Turn on **Note** to add or remove pencil marks instead of placing numbers.
4. Tap the title to leave a game and return to the menu.

Keyboard: `1`–`9` to enter a number, `N` to toggle notes.

## How it works

Everything lives in one file, `sudoku.html`.

**Puzzles.** A solved grid is generated with a randomized backtracking solver that uses bitmasks and always fills the cell with the fewest candidates first. Cells are then removed in random order. A removal is kept only if the solver still finds exactly one solution, which is checked by counting solutions up to two.

| Difficulty | Cells removed | Clues left |
| ---------- | ------------- | ---------- |
| Easy       | 41            | 40         |
| Medium     | 47            | 34         |
| Hard       | 51            | 30         |
| Expert     | 55            | 26         |

Difficulty is set by clue count only. For Expert, a puzzle can keep a few extra clues if no further cell can be removed without breaking uniqueness.

**Background.** A fragment shader draws a radial gradient with outward-moving waves and applies 8×8 Bayer ordered dithering. It renders at one third of the screen resolution and is scaled up with `image-rendering: pixelated`, so it stays light on the GPU. It is capped at about 30 fps and pauses when the tab is hidden. Canvas opacity is 20% on the landing screen and 10% in a game. On phones, the dots fade out towards the top edge so the background meets the status bar cleanly.

**Stats.** Stats and the last chosen difficulty are saved in `localStorage` under the key `sdk1`. A game counts as played when it starts, so a game you abandon counts as unsolved. Games in progress are not saved.

**Theming.** Colors are CSS custom properties that follow `prefers-color-scheme`. Setting `data-theme="light"` or `data-theme="dark"` on the root element overrides the system theme.

## Customizing

| What | Where |
| ---- | ----- |
| Clues per difficulty | The `[41,47,51,55]` array in `gen()` |
| Colors and highlights | The CSS variables on `:root` |
| Dither dot size | The `/3` in the shader's `size()` function |
| Phone top-fade height | The `50` passed to `gl.uniform1f(uF, …)` (canvas pixels) |
| Mistake limit | `g.mis>=3` in `put()` and the `/3` label in `draw()` |

## Browser support

Built and tested for Safari on iOS. It needs WebGL and CSS `aspect-ratio`, so iOS 15 or later. If WebGL is unavailable, the app runs without the background. If the animation library fails to load, it runs without animations.

If the operating system's reduced-motion setting is on, animations are skipped and the background renders as a single still frame.

## Credits

- [Instrument Serif](https://fonts.google.com/specimen/Instrument+Serif), loaded from Google Fonts (SIL Open Font License)
- [anime.js](https://animejs.com) 3.2.1, loaded from cdnjs (MIT)

## License

Add a license of your choice, for example MIT, as `LICENSE` in the repo root.
