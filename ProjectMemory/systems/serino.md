# Serino

## Files
`index.html`: `<script id="llm">` (model, no DOM), `<script id="serino-ui">` (screen), `<script id="serino-book">` (book content), `#serinoMode` markup, and the Serino section of `<style>`.

## Purpose
Second character, pink and female. The user trains a small character-level GPT on text, asks it to continue a prompt, and watches loss, attention and next-letter probabilities.

## How it works
- **Text:** `defaultText` builds about 30k characters from sentence patterns with a fixed seed. `cleanText` lowercases with Turkish rules and collapses whitespace. The user's text goes first and the ready text after it, so the exam comes from the end of the combined text. `buildCorpus` makes the vocabulary and the split.
- **Exam split:** the last `LLM_EXAM` (10%) is never studied. `studyWindow` windows end at or before `split - 1` as their last target index.
- **Model:** `MiniGPT`. Token and learned position embeddings, then blocks: norm, multi-head self-attention with a causal limit, output matrix, residual, norm, feed-forward (width `dim * LLM_FF`, tanh), residual. Final norm, then logits. All weights live in one `Float64Array`.
- **Norm:** RMS norm with no learned weights (`normF`, `normB`).
- **Backprop:** `accumulate` is hand-written, reverse of `forward`. `mulF` and `mulB` are the matrix pair.
- **Training:** `studyWindow` runs one window; every `LLM_BATCH` windows make one Adam update. `sLoop` spends 14 ms per frame, since a whole update takes about 100 ms. `sRefresh` runs at most every 1.5 s.
- **Scores:** `SerinoModel.evaluate` reads 12 evenly spaced windows from each part: loss and next-letter hit rate.
- **Mood:** `sMood` uses exam loss divided by ln(vocabulary), and the study/exam gap for `cram`.
- **Attention view:** `sDrawLook` shows real attention from the last block, averaged over heads, for the last letter on screen, and the top 5 next-letter probabilities.
- **Progress book:** `sSnaps` holds writing samples at doubling update counts (0, 20, then twice the last).
- **Memory file:** `serino-memory` version 1: text, shape, steps, weights, Adam state, chart history, samples. `fromMemory` validates fully before anything changes.
- **Switching:** leaving the screen stops training, since all training runs on the main thread. `serino` is created the first time her screen opens. See [shell.md](shell.md).

## Key functions
`defaultText`, `buildCorpus`, `MiniGPT.forward`, `MiniGPT.accumulate`, `SerinoModel.studyWindow`, `SerinoModel.evaluate`, `SerinoModel.write`, `setWho`, `serinoReset`, `sRefresh`, `sDrawLook`, `sApplyText`, `sApplyNet`.

## Interactions
The book's page turning, `term`, `num`, `live` and `#termPop` are shared; see [book.md](book.md). `sRefresh` calls `bookRefresh`. Uses `big`, `pct`, `showTip`, `tokens` from the UI script; see [ui.md](ui.md).

## Change-safety notes
- Book pages 19-27 state batch, learning rate, exam share, window count, network layout and measured results. Changing any means updating `SPAGES`, and training changes mean re-measuring page 27.
- The `LLM_BATCH` cap in `sLoop` is required: without it the loop never ends if the clock stalls.
- Editing `forward` means editing `accumulate` and re-running the numeric gradient check. Last check: worst relative error 2e-7 on 3 shapes.
- Serino element ids start with `s`, and Phyou already has `sSeen`, `sTrain` and `sTest`. Check for duplicates when adding one.
- User text and memory text reach the DOM only through `textContent`.
- `NUMS` is shared with Phyou's book: `m1`, `m2`, `v1`, `v2` belong to the Adam table.
