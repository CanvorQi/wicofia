# Overview

## Purpose
Wicofia is a teaching app for machine learning. A home page leads to three characters. The user teaches the character Phyou the four operations by asking questions and supplying correct answers, trains the character Serino, a small language model, on text, and trains the character Eroson, who learns to get through mazes from rewards. All show how the learning happens.

## Architecture
One static page with nine inline scripts and no dependencies. `brain`, `llm` and `maze` hold pure learning code, `ui`, `serino-ui` and `eroson-ui` read from them and draw, `book`, `serino-book` and `eroson-book` show the three books. Home cards pick a character through the URL hash, and only the open character trains. All training runs on the main thread inside `requestAnimationFrame`.

## File map
- `index.html`: styles, markup, `brain`, `llm`, `ui`, `maze`, `serino-ui`, `book`, `eroson-ui`, `serino-book`, `eroson-book` scripts
- `samples/`: Serino's default text and question file as Markdown
- `sw.js`, `manifest.webmanifest`, `icon.svg`, `icon-*.png`: offline use and installing
- `CLAUDE.md`: project rules and commands
- `.claude/settings.json`: Claude Code permissions
- `ProjectMemory/`: this library
