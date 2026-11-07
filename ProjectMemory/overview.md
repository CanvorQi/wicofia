# Overview

## Purpose
Wicofia is a teaching app for machine learning. A home page leads to four characters. The user teaches the character Phyou the four operations by asking questions and supplying correct answers, trains the character Serino, a small language model, on text, trains the character Eroson, who learns to get through mazes from rewards, and trains the character Merisu, who learns to draw pixel art from words. All show how the learning happens. A separate experiment card, Advanced Phyou, learns unshown additions by checking its own guesses, then finds subtraction through its own addition.

## Architecture
One static page with fifteen inline scripts and no dependencies. `brain`, `llm`, `maze`, `pixel` and `adv` hold pure learning code, `ui`, `serino-ui`, `eroson-ui`, `merisu-ui` and `adv-ui` read from them and draw, `book`, `serino-book`, `eroson-book` and `merisu-book` show the four books. Home cards pick a character through the URL hash, and only the open character trains. All training runs on the main thread inside `requestAnimationFrame`.

## File map
- `index.html`: styles, markup, `brain`, `llm`, `ui`, `maze`, `serino-ui`, `book`, `eroson-ui`, `serino-book`, `pixel`, `merisu-ui`, `merisu-book`, `adv`, `adv-ui`, `adv-book`, `eroson-book` scripts
- `samples/`: Serino's default text and question file as Markdown
- `sw.js`, `manifest.webmanifest`, `icon.svg`, `icon-*.png`: offline use and installing
- `CLAUDE.md`: project rules and commands
- `.claude/settings.json`: Claude Code permissions
- `ProjectMemory/`: this library
