# Wicofia

## Purpose
HTML-based app for understanding machine learning, with a home page leading to four characters: the user teaches the character Phyou the four arithmetic operations by checking its answers, trains the character Serino (a small character-level GPT) on text, trains the character Eroson to get through mazes by reinforcement, trains the character Merisu (a small image model) to draw pixel art from words, and watches the learning process behind the scenes. A separate experiment card, Advanced Phyou, learns unshown additions by checking its own guesses, then finds subtraction through its own addition.

## Commands
No build step: open `index.html` in a browser. Offline use and installing need http(s), for example `python -m http.server`. No test or lint setup.

## Structure
- `sw.js`, `manifest.webmanifest`, `icon.svg`, `icon-*.png`: offline use and installing, no app logic.
- `samples/`: Serino's default lesson as Markdown; copies sit in `index.html` and must stay identical.
- `index.html`: the whole app, no dependencies.
  - `<script id="brain">`, `<script id="llm">`, `<script id="maze">`, `<script id="pixel">`: learning code for Phyou, Serino, Eroson and Merisu, no DOM access, testable on their own.
  - `<script id="ui">`, `<script id="serino-ui">`, `<script id="eroson-ui">`, `<script id="merisu-ui">`: rendering, training loops, lesson flow.
  - `<script id="adv">`, `<script id="adv-ui">`, `<script id="adv-book">`: Advanced Phyou's learning code (no DOM access), screen and book; separate from Phyou's state, and the book's text must stay true to the code.
  - `<script id="book">`, `<script id="serino-book">`, `<script id="eroson-book">`, `<script id="merisu-book">`: the four books; their text must stay true to the code.

## Rules
- All app code stays in one dependency-free `index.html`; networks and backprop stay hand-written so every step can be shown.
- UI text is Turkish.
- Never train on exam questions, exam text, exam mazes or exam picture pairs; the study/exam split must stay honest.
- Merisu's output stays pixel art only: a fixed grid and a fixed palette.
- Every character's mood comes from real metrics, never faked.

## Project Memory
Knowledge library: `ProjectMemory/INDEX.md`. Read the index at session start; update the affected docs with every code change.
