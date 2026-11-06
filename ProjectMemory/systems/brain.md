# Brain

## Files
`index.html`, `<script id="brain">`. No DOM access.

## Purpose
The learning itself: questions, network, training, grading.

## How it works
- **Data:** a and b are 0-9. Division keeps only whole results with b not 0, giving 32 questions; the other operations have 100.
- **Split:** 20% of each operation is held out as exam questions.
- **Input:** 6 values: a and b scaled to -1..1, plus a one-hot operation.
- **Network:** 6 inputs, hidden layers of equal size, 1 output; tanh hidden layers, linear output. Output is the answer divided by `Y_SCALE` (40).
- **Shape:** `{ layers, neurons }` counts hidden layers and the neurons in each. `NET_DEFAULT` is 2 and 32 (6-32-32-1); `NET_LIMITS` allows 1-4 layers and 1-128 neurons.
- **Training:** MSE with Adam, batch 16, `BASE_LR` 0.003, learning rate halves after `LR_HALFLIFE` examples.
- **Sampling:** each operation is picked equally often, so division is not drowned out.
- **Grading:** an answer counts as correct when the rounded prediction equals the true value.

## Key functions
- `netSizes`, `validShape`, `shapeOf`, `Student.shape`: shape to sizes and back, limit checks
- `buildData`: questions, `grid` lookup, study/exam split
- `Brain.forward`, `Brain.accumulate`, `Brain.step`: inference, backprop, Adam
- `Student.studyOne`: one automatic study example
- `Student.lesson`: one hand lesson, `LESSON_REPEATS` (20) repeats
- `Student.evaluate`: writes `pred` and `err` on every item, returns per-operation scores

## Interactions
The UI calls `Student` and reads `item.pred`, `item.err`, `item.test`. `evaluate` must run before the UI reads those fields.

## Change-safety notes
- `lesson` trusts the teacher's label even when wrong; this is deliberate.
- Changing input encoding means updating `encode` and the first entry in `netSizes` together.
- Adam updates are costly per parameter; network growth lowers training speed directly. Measured in headless Edge: 6-32-32-1 about 88k examples/s, 6-64-64-64-1 about 10k, the maximum 6-128-128-128-128-1 about 1.6k.
- `NET_LIMITS` also bounds the memory file: the maximum network saves about 1.2 MB, under the 5 MB import limit.
- Phyou's book quotes these settings; see [book.md](book.md) before changing them.
