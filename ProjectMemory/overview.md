# Overview

## Purpose
Wicofia is a teaching app for machine learning. A home page leads to two characters. The user teaches the character Phyou the four operations by asking questions and supplying correct answers, and trains the character Serino, a small language model, on text. Both show how the learning happens.

## Architecture
One static page with six inline scripts and no dependencies. `brain` and `llm` hold pure learning code, `ui` and `serino-ui` read from them and draw, `book` and `serino-book` show the two books. Home cards pick a character through the URL hash, and only the open character trains. All training runs on the main thread inside `requestAnimationFrame`.

## File map
- `index.html`: styles, markup, `brain`, `llm`, `ui`, `serino-ui`, `book`, `serino-book` scripts
- `sw.js`, `manifest.webmanifest`, `icon.svg`, `icon-*.png`: offline use and installing
- `CLAUDE.md`: project rules and commands
- `.claude/settings.json`: Claude Code permissions
- `ProjectMemory/`: this library
