# Merisu

## Files
`index.html`: `<script id="pixel">` (pictures and model, no DOM), `<script id="merisu-ui">` (screen), `<script id="merisu-book">` (book), `#merisuMode` markup, and the Merisu section of `<style>`.

## Purpose
Fourth character, red-haired and female. A small image model that draws only pixel art from words: 16 × 16 cells, 24-entry fixed palette including empty. The output cannot be anything else, since every cell picks one palette entry.

## How it works
- **Lessons:** `PX_SUBJECTS` holds 10 shape generators, `PX_HUES` 6 colours with 3 shades each. `pxPicture` draws a shape with random parameters, adds outline and light (`pxFinish`), and shifts it by at most 1 cell (`pxSlide`). All pictures are made by this code from a fixed seed; no outside art.
- **Study and exam:** `pxExamHue` keeps one colour of each shape for the exam, so 50 pairs give 1200 study pictures and 10 pairs give 80 exam pictures. Exam pairs are never trained. `pxIsExam` also refuses them as names of user pictures.
- **Network:** `PixelNet`, a conditional decoder with hand-written backprop and Adam. Word cards (24 numbers each, summed) plus 10 imagined numbers go through 96 and 64 ReLU neurons to 15 scores per cell. A ramp colour scores shade plus family. `oSkip` links each word directly to the families, and a cell's own family score is scaled down by `PX_FAM` (0,1).
- **Learning:** `MerisuModel.study` trains on 8 whole pictures, cross-entropy per cell. Each study picture has its own learned code in `p` above `oBrain`, with noise `PX_NOISE` and pull `PX_PULL`. There is no encoder. A word is dropped 15% of the time, so one word also draws.
- **Drawing:** `spread` measures how far the codes reach, `code` draws new numbers from there, scaled by the Hayal slider, `paint` picks each cell with temperature `PX_SHARP`.
- **Scores:** `evaluate` only draws, with a fixed seed: 3 pictures for each of 10 pairs on each side. `pxJudge` names the shape by the closest outline among every fourth study picture, and the colour by the most used family. Correct means both match.
- **Own pictures:** `teach` adds a picture and its words; own pictures take half of each batch. Up to `PX_OWN_MAX` (32) pictures and `PX_WORD_MAX` (64) words.
- **Mood:** `mMood` uses the exam score, and the study/exam gap for `cram`.
- **Memory file:** `mExport` downloads `merisu-hafiza-<date>.json`, about 4,2 MB: `format`, `version` (1), `savedAt`, `stamp`, `steps`, `seen`, `loss`, `t`, `vocab`, `own`, `weights`, `adamM`, `adamS`, `history`, `notes`. Arrays go through `pxPack` as Float32 base64, so a load matches to about 3e-8 and continues almost, not exactly, the same way. The lesson pictures are not in the file: they come from a fixed seed, and `pxStamp` proves they are the same. `fromMemory` validates everything before use and refuses an own picture named after an exam pair. `merisuReset(next, past, notes)` swaps it in.

## Key functions
`pxPicture`, `pxSets`, `pxIsExam`, `pxJudge`, `pxNearest`, `PixelNet.forward`, `PixelNet.cell`, `PixelNet.learn`, `MerisuModel.study`, `MerisuModel.teach`, `MerisuModel.evaluate`, `MerisuModel.toMemory`, `MerisuModel.fromMemory`, `merisuReset`, `mExport`, `mImport`, `mLoop`, `mRefresh`, `mDraw`, `mDrawBars`, `mSnap`.

## Interactions
Uses `big`, `pct`, `num1` from [ui.md](ui.md), `tr2`, `sCount`, `sNote`, `stepsToNext` from [serino.md](serino.md), and `eDrawChart` from [eroson.md](eroson.md) with its own canvas, history and accent. Book pages, `term` and `live` are shared; see [book.md](book.md). `setWho` calls `mSetRun`, `merisuReset`, `mRefresh`.

## Change-safety notes
- Book page 10 holds measured results; changing the network, data or training means re-measuring it. Pages 3-9 state sizes through constants, but pages 6-8 describe the design in words.
- A first version chose cells one by one from earlier cells. After a minute's training most canvases stayed empty and it got 0 of 40 exam drawings right. The book's last page tells this story; do not go back without measuring.
- Do not raise `PX_FAM` without measuring. At 1 cells memorised which colours a shape never had: exam pairs turned to colour confetti and fell to 53-63% at 96k pictures. At 0,1 they reach 93-97%. Own pictures with many colours rely on these scores, so they are drawn less exactly.
- Training is slow on the main thread, one update is 8 whole pictures. `learn` skips settled scores for speed, which is a very small approximation of the gradient.
- Scripts are classic and share one scope, so names use the `PX_`, `px`, `m` prefixes. Element ids start with `m` plus a capital.
- Changing a shape generator changes `pxStamp`, so older memory files are refused with a message.
- Changing the network sizes changes the length of `p`, so older files are refused as broken.
- The SVG id is `mFace`, not `merisu`. An element with the same id as the route makes the browser scroll to it; `serino` and `eroson` still have this problem.
