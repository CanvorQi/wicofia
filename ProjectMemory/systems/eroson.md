# Eroson

## Files
`index.html`: `<script id="maze">` (world and learner, no DOM), `<script id="eroson-ui">` (screen), `<script id="eroson-book">` (book, start-up), `#erosonMode` markup, and the Eroson section of `<style>`.

## Purpose
Third character, green and male. A small neural network learns by reinforcement to get through mazes it moves in step by step, like a player. The user trains him, watches him on unseen mazes, and can draw a map of their own.

## How it works
- **Mazes:** `makeMaze` carves a perfect maze with a depth-first walk from a seed. The start is random and the exit is the farthest cell from it. `open` holds one bitmask per cell (up, right, down, left).
- **Study and exam:** `mazeSets` sets aside `MAZE_EXAM` (20) exam mazes first, then takes `MAZE_STUDY` (300) study mazes, dropping any equal to an exam maze. Sizes are 5, 7 and 9.
- **Senses:** `MazeRun.sense` gives `MAZE_F` (8) numbers per direction: open, unvisited, visit count, towards the exit, reverse of the last move, exit next door, visits here, unvisited ways here. He has no map.
- **Network:** Phyou's `Brain`, sizes 8-24-24-1. The same network scores each direction separately. Walls are not moves (`ways`), so a greedy player cannot stand still forever.
- **Learning:** deep Q-learning. `study` plays one move (random with chance `eps`, from 100% down to 5% in 40.000 moves) and stores it in a replay memory of 20.000 moves. Every `RL_EVERY` (4) moves `learn` draws `RL_BATCH` (16) moves and regresses towards reward plus `RL_GAMMA` (0,98) times the best next score from `goal`, the target network copied every `RL_SYNC` (1000) moves.
- **Rewards:** exit +1, each step -0,01, a repeated cell -0,01 more. Limit: 3 times the cell count.
- **Scores:** `evaluate` only plays, greedily, on 20 study mazes and all exam mazes. `eBase` holds the right-hand rule and a random walker on the same exam.
- **Mood:** `eMood` uses exam solve rate, and the study/exam gap for `cram`.
- **Own map:** `eMap` holds the drawing. `customMaze` turns blocked cells into the same `open` form and returns null when the exit cannot be reached. Eroson can play it (`eWatch`, no learning) or study it: `agent.custom` then takes half of the games. It stays outside both `sets`, so the exam is unchanged.
- **Switching:** leaving the screen stops training and any watching. `eroson` is created the first time his screen opens. See [shell.md](shell.md).

## Key functions
`makeMaze`, `customMaze`, `mazeDistances`, `mazeSets`, `MazeRun.sense`, `MazeRun.move`, `ErosonAgent.study`, `ErosonAgent.learn`, `ErosonAgent.evaluate`, `erosonReset`, `eLoop`, `eRefresh`, `eWatch`, `eDrawGame`, `eDrawBars`, `eDrawMap`, `ePaint`.

## Interactions
Uses `Brain` from [brain.md](brain.md), `big`, `pct`, `tokens` from [ui.md](ui.md), `tr2`, `sCount`, `sNote` from [serino.md](serino.md). Book pages, `term` and `live` are shared; see [book.md](book.md). `setWho` calls `eSetRun`, `eStopDemo`, `erosonReset`, `eRefresh`.

## Change-safety notes
- Book page 10 holds measured results, and pages 5, 8 and 9 state rewards, memory and exam rules. Changing training or rewards means re-measuring page 10.
- With the exit in a fixed corner he reached 100% in seconds by learning "right and down". That is why start and exit are random; do not make them fixed again. The book's last page tells this story.
- `Brain` is shared with Phyou. Changing it affects both.
- Eroson's training does not touch `sets.exam`. A new way of choosing games must keep it that way.
- `eroson.custom` is copied when chosen, so later drawing needs choosing again; the note says so.
- Element ids start with `e`; Serino's start with `s`.
- There is no memory file for him yet, and a reload loses training and the drawn map.
