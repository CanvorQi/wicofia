# Advanced Phyou

## Files
`index.html`: `.adv-*` styles, `#advMode`, `#advCtl`, `<script id="adv">` (no DOM), `<script id="adv-ui">`, `<script id="adv-book">`.

## Purpose
An experiment card separate from Phyou with two views. "Toplamayı düşün": the user shows a chosen number of additions, then Phyou tries to learn the remaining study questions by checking its own guesses. "Çıkarmayı bul": after that, the same brain finds subtraction with no subtraction answers. It has its own `Student`, look and screen, and does not read or change Phyou's state.

## How it works
- **Teaching:** `Thinker` picks the chosen count (`ADV_SHOWN`, 0-70, default 10) at random from the 80 study additions and trains `ADV_TEACH` (30k) examples on them only. With 0 shown, teaching is skipped and any later lessons are self-given only.
- **Checks:** `think` makes a guess, then compares it with up to 5 checks: swapped order, and the four neighbours plus or minus 1. Shown facts replace the network's output in checks.
- **Self-teaching:** at least `ADV_AGREE` (80%) of checks must match. Matching guesses become labels. Each of `ADV_ROUNDS` (10) rounds trains `ADV_THINK` (3k) examples, half self-labels and half shown questions.
- **Guard:** `note` scores each state as shown answers correct, then number of sure questions. A worse round is undone at once; `finish` restores the best state. Unshown true answers play no part.
- **Subtraction:** `SubFinder` needs a finished `Thinker`. `solve` tries 0-9 as g and picks the g whose own addition (`Thinker.value`) lands closest to the target, sure when under `ADV_GATE` (0.4). A smaller first number is solved as b − a and the sign flipped, since addition never saw negatives. Each of `ADV_SUB_ROUNDS` (15) rounds trains `ADV_SUB_THINK` (4k) examples, half found answers and half replay of the addition it was given.
- **Subtraction guard:** `note` scores addition kept plus found answers learned. A round below `ADV_KEEP` (90%) of the first kept count scores -1. Worse rounds are undone.
- **Grading:** `item.y` of an unshown question is read only for the log, stats and marks. The exam is the 20 addition exam questions; they never enter `labels`.
- **Screen:** `advLoop` runs a fixed number of examples per frame (`ADV_SPEED`) so the process stays watchable. Cells show answers and status, clicking one shows its checks, and the round table shows what was undone. Moods use real scores.
- **Asking:** the "Soru sor" card takes two digits and shows the answer, its status and the checks. `advPaintAnswer` only reads the network, so a question is never trained and exam questions stay safe.
- **Book:** `TPAGES` ("Düşünme Kitabı", 22 pages) explains only this card, with worked math: output scale, agreement count, self-label loss, gradient mix, round scores, why rules leave one free constant, and the subtraction search. Settings come from the `ADV_*` constants, live numbers from `thinker` and `finder`; measured tables and worked numbers are fixed. `advRefresh` calls `afterRefresh` so an open book keeps updating.

## Key functions
`Thinker`: `think`, `review`, `note`, `learn`, `advance`, `startThinking`, `finish`, `grade`. `SubFinder`: `search`, `solve`, `review`, `note`, `learn`, `advance`, `grade`. UI: `advFinder`, `advSubMood`, `advPaintSubChain`, `advSetRun`, `advLoop`, `advMood`, `advRefresh`, `advStart`, `advPress`.

## Interactions
Uses `Student`, `encode`, `shuffle` from [brain.md](brain.md); routing and home card from [shell.md](shell.md); `$`, `pct`, `big`, `num1`, `sNote` from other UI scripts. `adv-ui` must load before `eroson-book`.

## Measured
Exam share before and after thinking, real class, 12 seeds: 0 shown 3→3, 1 shown 7→8, 2 shown 9→10, 3 shown 44→52 (lowest 5), 5 shown 60→85 (lowest 15), 10 shown 83→95 (lowest 65), 20 shown 90→100, 40 shown 98→100, 70 shown 92→100. On 24 other seeds with the guard: 5 shown 69→86, harmed 2 of 24; 10 shown 77→99, lowest 85; 20 shown 97→100.

Subtraction exam before and after, addition exam after, real class, 12 seeds, by additions shown: 0: 3→6, 2; 3: 3→66 (lowest 0), 67; 5: 4→93 (lowest 45), 97; 10: 2→98 (lowest 90), 100; 20: 3→97 (lowest 90), 100; 40: 2→97 (lowest 90), 100; 70: 3→98 (lowest 90), 99.

## Change-safety notes
- A subtraction guard that ranks kept addition above everything undid almost every round: answers were 100% right but the exam stayed at 2-10%. Keep the sum with the `ADV_KEEP` floor.
- Without the guard, one run fell from 55% to 30% while sure of 60 of 70 questions. Do not remove it.
- The rules test differences only, so answers that all drift together pass them. Below about 20 shown questions a run can still end low or get worse.
- The rules and the plus or minus 1 step are given by code; screen text must keep saying so.
- Changing `Thinker` or `SubFinder` behaviour means re-measuring the tables on book pages 18 and 20 and checking the math pages.
- `chooseBook` runs Phyou's `openBook`, which reads Phyou's `student` but does not train it.
- It has no memory file. A reload loses it.
- `.adv` is the home card class; the screen uses `.adv-page`.
