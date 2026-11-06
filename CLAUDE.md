# Wicofia

## Purpose
HTML-based app for understanding machine learning, with a home page leading to two characters: the user teaches the character Phyou the four arithmetic operations by checking its answers, trains the character Serino (a small character-level GPT) on text, and watches the learning process behind the scenes.

## Commands
No build step: open `index.html` in a browser. Offline use and installing need http(s), for example `python -m http.server`. No test or lint setup.

## Structure
- `sw.js`, `manifest.webmanifest`, `icon.svg`, `icon-*.png`: offline use and installing, no app logic.
- `index.html`: the whole app, no dependencies.
  - `<script id="brain">`, `<script id="llm">`: learning code for Phyou and Serino, no DOM access, testable on their own.
  - `<script id="ui">`, `<script id="serino-ui">`: rendering, training loops, lesson flow.
  - `<script id="book">`, `<script id="serino-book">`: the two books; their text must stay true to the code.

## Rules
- All app code stays in one dependency-free `index.html`; networks and backprop stay hand-written so every step can be shown.
- UI text is Turkish.
- Never train on exam questions or exam text; the study/exam split must stay honest.
- Phyou's and Serino's moods come from real metrics, never faked.

## Project Memory
Knowledge library: `ProjectMemory/INDEX.md`. Read the index at session start; update the affected docs with every code change.
