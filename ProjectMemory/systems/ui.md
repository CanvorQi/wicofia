# UI

## Files
`index.html`: `<style>`, markup, `<script id="ui">`.

## Purpose
Runs the training loop, handles lessons, shows what the network is doing.

## How it works
- **Screens:** `setWho` shows the home page, `#phyouMode` or `#serinoMode` and stops training on the others; see [shell.md](shell.md). `body[data-who]` swaps the accent to pink.
- **Loop:** `loop` runs per frame, up to 4000 examples and a 12 ms budget. `refresh` re-grades and redraws about every 200 ms.
- **Lesson flow:** `ask` validates and shows the guess, `teach` trains and reports before and after. Exam questions are shown but never trained.
- **Character:** `updateMood` picks a mood from study and exam accuracy. `say` with a hold time keeps a message up before moods resume.
- **Heatmaps:** 400 DOM cells, repainted only when a cell's color step changes.
- **Charts:** two canvases, log y axis from 0.1 to 50. `record` keeps at most 420 points by thinning.
- **Beyin card:** two number inputs and a default button call `applyNet`, which validates, asks before erasing progress, and swaps in a new `Student` through `reset`. `reset` calls `showNet`, so the inputs always show the live brain. `Sıfırla` keeps the current shape.
- **Theme:** CSS tokens under `data-theme`, set by `setTheme`; see [shell.md](shell.md). `readTokens` copies them for canvas and heatmap code.

## Key functions
`reset`, `setRunning`, `loop`, `refresh`, `record`, `paintHeat`, `drawChart`, `updateMood`, `ask`, `teach`, `showNet`, `applyNet`.

## Interactions
Reads scores from `Student.evaluate` and drives `Student.studyOne` and `Student.lesson`. See [brain.md](brain.md). `refresh` calls `afterRefresh`, which the book sets; see [book.md](book.md).

## Change-safety notes
- The 4000 cap in `loop` is required: without it the fastest setting never ends if the clock stalls.
- The `[hidden]` CSS rule is required, since `.answer` sets `display: grid`.
- `askForm` uses `novalidate` so Turkish messages show instead of the browser's own.
- Chart hover hard-codes the left and right padding used in `drawChart`; keep them in sync.
