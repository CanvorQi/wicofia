# Status

## Done (first version)
- Hand-written network, Adam, study/exam split
- Hand lessons and automatic teacher with speed control
- Report card, error heatmaps, train/exam error charts
- Phyou character driven by real metrics
- Phyou's book with live numbers, page turning, glossary, term pop-ups and number hovers
- Memory download and load as a JSON file
- Editable hidden layer and neuron counts with a default
- Wicofia shell: home page, Açık/Koyu theme, offline use and installing
- Serino: mini GPT on text, writing, attention view, loss chart, progress book, memory file, her own book
- Serino reads the user's files: txt, md, csv, html, docx by picker or drop
- Serino question mode: Q&A files (labelled lines, csv/tsv, json/jsonl, docx), asking, and a question exam split by whole pairs

- Serino's default lesson: sample text and 126 questions in `samples/`, on by default through a checkbox
- Eroson: maze character with deep Q-learning, game board, watching on unseen mazes, report card with two yardsticks, own map editor, his own book
- Merisu: pixel art image model with words and imagined numbers, drawing screen, cell chances, picture editor, exam on unseen colour and shape pairs, memory file, her own book
- Advanced Phyou: separate experiment card with Thinking Mode for addition only, rule checks, undoing bad rounds, real-metric moods, a question card, a second view that finds subtraction through its own addition, and its own 22-page book with worked math

## Unfinished
- Advanced Phyou has no memory file, and multiplication and division are not added
- With few shown additions Advanced Phyou can still end low or get worse, since the rules miss answers that all drift together
- `icon.svg` and the PNGs still show three characters; Merisu is not on the app icon
- Serino's sample answer to "arkadaşların kim" does not name Merisu; changing it changes the sample text and stops old memory files being recognised as the ready text
- Eroson has no memory file, and his drawn map is lost on reload
- Maps cannot be saved or shared as files yet
- Serino cannot read PDF files: a dependency-free parser would be unreliable for Turkish letters, so they are refused with a message
- Live network diagram
- Slow-motion view of one forward and backward pass
- Experiment controls: learning rate, noisy teacher, teach only addition then ask multiplication
- Comparing "right/wrong" feedback with "give the correct answer"
- Automatic saving in the browser: a reload still loses anything not downloaded; when added, update book page 27
- Serino training is main-thread and slow (about 10 updates per second); a Web Worker would keep the page smoother
- The cramming mood rarely appears, since the exam score tracks the study score closely; it may need a harder setup such as fewer study questions

## Testing
No automated tests in the repo. Checks so far ran through temporary headless Edge scripts; see [changelog.md](changelog.md).
