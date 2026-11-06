# Memory File

## Files
`index.html`: `Student.toMemory` and `Student.fromMemory` in the `brain` script; `exportMemory` and `importMemory` in the `ui` script; the Hafıza card.

## Purpose
Lets the user download what Phyou learned as a JSON file and load it back later.

## How it works
- **File:** `format`, `version` (1), `savedAt`, `seen`, `sizes`, `exam`, `weights`, `adam` (`t`, `m`, `v`), `history`. About 85 KB with the default network, about 1.2 MB with the largest. `sizes` carries the network shape.
- **Export:** builds a Blob and downloads `phyou-hafiza-<date>.json`. An unfinished batch of up to 15 examples is dropped.
- **Import:** parses, then `fromMemory` builds a new `Student` and validates everything before it is used. Only after that, and a confirm if Phyou has progress, does `reset(next, past)` swap it in.

## Key functions
`toMemory`, `fromMemory`, `exportMemory`, `importMemory`, `reset`.

## Interactions
`reset` accepts a ready `Student` and chart history. See [brain.md](brain.md), [ui.md](ui.md). Book page 27 and the `hafiza` glossary entry describe this feature; see [book.md](book.md).

## Change-safety notes
- `exam` must stay in the file: without it a loaded Phyou would be examined on questions it studied.
- `adam` must stay too: without it training after a load would not continue the same way.
- `fromMemory` builds the network from the file's `sizes`, after `shapeOf` checks them against `NET_LIMITS`; a load also changes the Beyin card. Files from before the network became editable stay valid, so `version` stayed 1. Changing the file layout needs a `version` bump and a decision about old files.
- `fromMemory` rebuilds `train` lists in grid order, not shuffled order; sampling is random, so this has no effect.
- A load continues exactly as if never saved. Exam accuracy can still swing widely early on, since an answer only counts when it rounds exactly right.
