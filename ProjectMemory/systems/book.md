# Book

## Files
`index.html`: `<script id="book">`, `<dialog id="bookDlg">`, and the book section of `<style>`.

## Purpose
Phyou explains in first person how it learns and where it stores what it learned. Serino's book does the same for a language model, in `<script id="serino-book">`.

## Two books
`BOOKS` holds each book's pages, title and open page; `shelf` is the current one and `chooseBook(who)` switches it. `#bookDlg[data-who]` recolors the cover and accent. `serino-book` adds its entries to `GLOSSARY`, `NUMS` and `LIVE`, then sets `BOOKS.serino` with `SPAGES` (cover plus 32 pages). `eroson-book` does the same with `EPAGES` (cover plus 12 pages) and `BOOKS.eroson`, `merisu-book` with `MPAGES` (cover plus 12 pages) and `BOOKS.merisu`, `adv-book` with `TPAGES` (cover plus 22 pages) and `BOOKS.advanced`; the last explains only that card's two experiments and their math, and its measured table must follow [advanced.md](advanced.md). Page facts below describe Phyou's book.

## How it works
- **Content:** `PAGES` holds the cover (index 0) and 36 pages; index equals the printed page number. `CHAPTERS` feeds the contents page.
- **Terms:** `GLOSSARY` is the single source for the glossary pages and pop-ups. `term(key, word)` makes a dotted word in the book; static markup can use `class="term"` with `data-term`, as the report card does. `showTerm` moves `#termPop` inside the dialog while it is open.
- **Number hovers:** `NUMS` holds every number in the worked calculation tables as `[shown value, name, source]`, keyed by example (`a` page 8 neuron, `b` chain, `t` 1-2-1 network). `num(key, shown)` makes a dotted button; `showTerm` reads `data-num` and opens `#termPop` on mouseover or tap. Tables with their own column headers (pages 6, 21, 31) and numbers in running text are not marked.
- **Live values:** `<span data-live="key">` is filled from `LIVE`, read from the current `student` and `stats`. `bookRefresh` also updates the error line figure and the weight grid; it runs after every UI `refresh` through `afterRefresh`.
- **Network shape:** `netFigure`, `netCaption` and the `layers` live value read the current brain; pages render when the book opens. The figure draws at most `NET_DOTS` (8) neurons per layer. `wCols` adds columns for big brains so the weight grid stays page-sized. Hovering a square names its connection or, for a bias, its layer; this relies on the weight layout in `Brain` (each neuron's input weights, then its bias).
- **Learning pages:** pages 11-23 work through gradients, the chain rule, the plain update and Adam. Pages 16-21 follow one full round on a 1-2-1 network (`tinyNetFigure`); pages 18-19 explain the slope (`tanhFigure`). Their tables use fixed numbers, computed from the page 8 neuron, a two-neuron chain and the 1-2-1 network; `steps` shows the real update count.
- **Size pages:** pages 30-31 explain neuron and layer counts. Page 30 reads the live shape and weight formula. Page 31's table holds measured results (1M examples, 3 runs each, headless Edge).
- **Running example:** `bookItem` is 7 × 8, or another study multiplication when 7 × 8 is an exam question.
- **Page turn:** desktop shows two-page spreads. `goTo` places a `.leaf` with a front and back page over the right half and rotates it with the Web Animations API. Phone width shows one page with no leaf.
- **Colors:** paper colors are fixed in both themes, since the book is a physical object.

## Key functions
`num`, `showTerm`, `pageHTML`, `bookRefresh`, `drawWeights`, `placeSpread`, `goTo`, `stepBook`, `openBook`.

## Interactions
Reads `student`, `stats`, `OPS`, and the helpers `big`, `pct`, `num1` from the UI script. Sets `afterRefresh`. See [ui.md](ui.md) and [brain.md](brain.md).

## Change-safety notes
- Book text must stay true to the code at every allowed network shape, down to 9 weights, so avoid fixed sizes like "thousands". Changing batch, learning rate, `LESSON_REPEATS`, the split, Adam settings or saving behavior means updating `PAGES` and `GLOSSARY`; pages 15-23 describe the updates, page 27 describes saving. Editing page 8 means recomputing pages 11, 12 and 15. Changing training settings means re-measuring the page 31 table.
- Changing an example number means updating its `NUMS` entry, including the source text. Same-valued numbers keep separate keys (the input and `v₂` are both 0,5).
- Do not put Turkish suffixes after a live number, since the right suffix depends on the value.
- `CHAPTERS` page numbers must match `PAGES`.
- Keep the page count even so the last spread is full.
- After editing text, recheck page overflow at short window heights.
