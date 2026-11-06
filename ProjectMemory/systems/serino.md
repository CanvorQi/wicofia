# Serino

## Files
`index.html`: `<script id="llm">` (model, no DOM), `<script id="serino-ui">` (screen), `<script id="serino-book">` (book content), `#serinoMode` markup, and the Serino section of `<style>`.

## Purpose
Second character, pink and female. The user trains a small character-level GPT on text, asks it to continue a prompt, and watches loss, attention and next-letter probabilities.

## How it works
- **Text:** `defaultText` builds about 30k characters from sentence patterns with a fixed seed. `cleanText` lowercases with Turkish rules and collapses whitespace. Order is pasted text, scanned files, ready text, so the exam comes from the end of the combined text. `buildCorpus` makes the vocabulary and the split.
- **File scan:** the Okuyacağı metin card takes several files by picker or drop. `readDoc` reads text as UTF-8, UTF-16 by BOM or windows-1254, strips HTML, and pulls `word/document.xml` out of a `.docx` with a hand-written zip walk and `DecompressionStream`. PDF and files holding NUL are refused by name. `tidyText` plus `cleanText` go into `sDocs`; an identical text is refused, since it would land in both study and exam. A file added unchecks the ready text, so the exam comes from the user's own text. `sApplyText` shrinks files evenly to fit `LLM_MAX_TEXT`, and `fitVocab` drops the rarest characters above `LLM_MAX_VOCAB`. Both report what they left out.
- **Questions and answers:** the Soru-cevap card takes files through `readDoc`, then `parseQA` tries JSON or JSONL, `Soru:`/`Cevap:` lines, and a two-column table, in that order. Repeated questions keep their first answer, within and across files. `sApplyQa` shuffles the pairs and writes them as `§question→answer¶` (`qaText`). The format lives only in the text: `buildCorpus` detects it with `qaPairs` and sets `corpus.qa`, so the memory file needs no new field. In this mode the Bana yazdır card asks questions and writing stops at a marker (`QA_MARKS`). `sQaExam` asks up to `QA_EXAM_MAX` study and exam questions with `SerinoModel.answer` at temperature 0,1; only an identical answer counts. It runs forward passes only and stops training first.
- **Sample lesson:** Serino starts on `sampleCorpus`: the text of `samples/serino-metin.md`, then the pairs of `samples/serino-soru-cevap.md` in a fixed shuffled order. Copies live in `#sampleText` and `#sampleQa`, since a page opened as a file cannot fetch. The `#sSampleOn` checkbox switches between this and `defaultText`; `serinoReset` keeps it true to the model, so own text turns it off and a memory file of the sample turns it on.
- **Mixed corpus:** `buildCorpus` accepts plain text followed by pairs. The exam is then only the last 10% of pairs, and the text is all study.
- **Exam split:** the last `LLM_EXAM` (10%) is never studied. In question mode the split falls on a pair boundary, the last 10% of pairs (at least one), so no study window sees the start of an exam question. `studyWindow` windows end at or before `split - 1` as their last target index.
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
`defaultText`, `buildCorpus`, `MiniGPT.forward`, `MiniGPT.accumulate`, `SerinoModel.studyWindow`, `SerinoModel.evaluate`, `SerinoModel.write`, `setWho`, `serinoReset`, `sRefresh`, `sDrawLook`, `sApplyText`, `sApplyNet`, `sScan`, `readDoc`, `docxText`, `tidyText`, `fitVocab`, `parseQA`, `qaText`, `qaPairs`, `sScanQa`, `sApplyQa`, `sQaExam`, `SerinoModel.answer`, `sShowMode`, `sampleCorpus`.

## Interactions
The book's page turning, `term`, `num`, `live` and `#termPop` are shared; see [book.md](book.md). `sRefresh` calls `bookRefresh`. Uses `big`, `pct`, `showTip`, `tokens` from the UI script; see [ui.md](ui.md).

## Change-safety notes
- Book pages 19-27 state batch, learning rate, exam share, window count, network layout and measured results. Changing any means updating `SPAGES`, and training changes mean re-measuring page 27.
- The `LLM_BATCH` cap in `sLoop` is required: without it the loop never ends if the clock stalls.
- Editing `forward` means editing `accumulate` and re-running the numeric gradient check. Last check: worst relative error 2e-7 on 3 shapes.
- Serino element ids start with `s`, and Phyou already has `sSeen`, `sTrain` and `sTest`. Check for duplicates when adding one.
- User text, file names and memory text reach the DOM only through `textContent`.
- `sDocs` and `sQaDocs` are pending input, not model state: they are not saved in the memory file and survive a reset.
- With context 32 most real pairs exceed the window, so the end of an answer cannot see its question; `sApplyQa` warns and points to Bağlam (max 64). A longer limit needs the page 27 table re-measured.
- `samples/*.md` and their copies in `index.html` must stay identical. Sample pairs must fit context 32 (question + answer + 2 markers). Changing them changes the exam order and stops old memory files turning the checkbox on.
- The measured table in the book says "Hazır metin", which is `defaultText`, not the sample lesson.
- Pair lengths in `sApplyQa` and the `¶`-split in `qaPairs` assume the markers never occur inside a pair; `parseQA` removes them.
- Book pages on the exam, what she cannot do and big models mention question mode; keep them true.
- `NUMS` is shared with Phyou's book: `m1`, `m2`, `v1`, `v2` belong to the Adam table.
