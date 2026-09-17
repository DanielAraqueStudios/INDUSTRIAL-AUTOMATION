# README — building LAB4's SCL timer/counter logic in TIA Portal

Same pattern as LAB3: Tag Table (physical I/O only) → FB (all logic) →
OB1 (single call, no logic). This covers only the SCL timer/counter
piece — the AWL pulse block (servo PTI) and GRAFCET sequence come later.

## Steps

1. Create the 6 PLC tags below (`PLC_1 → PLC tags`).
2. Create `FB_Embotelladora` (Function Block, language SCL).
3. Declare the Static variables (table below) in the FB's interface table.
4. Paste `src/FB_Embotelladora.scl`'s content into the code pane (body
   only — don't type `FUNCTION_BLOCK`/`BEGIN`/`END_FUNCTION_BLOCK`).
5. Compile. Creates `FB_Embotelladora_DB`.
6. Create `Main [OB1]`, paste `src/OB1_Embotelladora.scl`'s call line
   (adjust the DB name if TIA names it differently, e.g. `Block_1_DB`).
7. Compile everything, download, test.

## PLC tags

| Name      | Data type | Address | Comment                                    |
|-----------|-----------|---------|----------------------------------------------|
| I_Start   | Bool      | %I0.0   | Starts the filling process                   |
| I_Stop    | Bool      | %I0.1   | Graceful stop                                |
| I_EStop   | Bool      | %I0.2   | Emergency stop — immediate, unconditional    |
| Q_Fill    | Bool      | %Q0.0   | ON while actively filling a bottle           |
| Q_Running | Bool      | %Q0.1   | ON while the process is active               |
| Q_Done    | Bool      | %Q0.2   | ON once all 17 bottles are filled            |

## FB_Embotelladora — Static section variables

| Name             | Data type | Default | Comment                                    |
|-------------------|-----------|---------|-----------------------------------------------|
| Running           | Bool      |         | Latched process-active flag                  |
| PrevFillQ         | Bool      |         | Previous scan's Timer_Fill.Q, for edge detect |
| Timer_Fill        | TON       |         | 3s fill timer (resolves to TON_TIME)          |
| Preset_Fill       | Time      | T#3s    | Fill time per bottle                          |
| Counter_Bottles   | CTU       |         | Counts completed fills (resolves to CTU_INT)  |
| Preset_Bottles    | Int       | 17      | Target bottle count (sum of ID digits 4+8+5)  |

## FB_ServoPulse — servo pulse generation (SCL, deliberate deviation)

The assignment requires this in AWL ("el pulso debe generarse en AWL...
es condición única, relevante y requisito"). By explicit decision, this
is done in SCL instead, same pattern as the rest — aware this may cost
credit on that specific grading point.

### Additional PLC tags (see `src/TagTable_Servo.md`)

| Name         | Data type | Address | Comment                                |
|---------------|-----------|---------|------------------------------------------|
| I_ServoReady  | Bool      | %I0.3   | Servo drive ready (comm/status feedback)|
| I_ServoFault  | Bool      | %I0.4   | Servo drive fault (comm/status feedback)|
| Q_ServoPulse  | Bool      | %Q0.3   | Toggling pulse output to servo PTI       |
| Q_ServoDir    | Bool      | %Q0.4   | Direction (unused by this FB yet)        |
| Q_Positioned  | Bool      | %Q0.5   | ON once commanded pulse count reached    |
| Q_ServoAlarm  | Bool      | %Q0.6   | ON while I_ServoFault is active          |

### FB_ServoPulse — Static section variables

| Name          | Data type | Default | Comment                                   |
|----------------|-----------|---------|----------------------------------------------|
| Advance        | Bool      |         | One-shot: command to move one index          |
| PrevAdvance    | Bool      |         | Previous scan's Advance, for edge detect      |
| Moving         | Bool      |         | Latched while a move is in progress           |
| ClockTimer     | TP        |         | Toggle clock (resolves to TP_TIME)            |
| ClockPeriod    | Time      | T#10ms  | Pulse half-period (placeholder rate)          |
| PulseState     | Bool      |         | Current output level, toggled each clock tick |
| PulseCounter   | CTU       |         | Counts completed pulses (resolves to CTU_INT) |
| Preset_Pulses  | Int       | 80      | Pulses per index (see TagTable_Servo.md calc) |

### Temp variable (interface table, Temp section — not typed in the pane)

| Name      | Data type | Comment                          |
|-----------|-----------|-------------------------------------|
| StartMove | Bool      | Scratch value, recomputed every scan |

## FB_ServoPulse_AWL — servo pulse generation (STL/AWL, assignment-compliant)

This is the version to actually submit — `FB_ServoPulse.scl` is the
deliberate SCL deviation, kept only for comparison. Same Static/Temp
variables as the SCL version (see tables above); the difference is the
code pane language (**STL**, not SCL) and syntax.

### Building it in TIA

1. When creating the FB, set **Language: STL** (not SCL) in the Add new
   block dialog.
2. Declare the same Static and Temp variables as `FB_ServoPulse.scl`'s
   tables above.
3. Paste `src/FB_ServoPulse_AWL.awl`'s content into the code pane.
4. STL is column-sensitive for jump labels (`M001:`) — keep the label at
   the start of the line, statement right after, exactly as the file
   shows.
5. Compile.

### STL instructions used (quick reference)

| Instruction | Meaning                                             |
|-------------|--------------------------------------------------------|
| `U` / `UN`  | AND / AND NOT the operand into the logic result (RLO)   |
| `O`         | OR the operand into RLO                                 |
| `=`         | Assign RLO to the given bit                             |
| `S` / `R`   | Set / Reset the given bit if RLO = 1                    |
| `L` / `T`   | Load a value into the accumulator / transfer it out     |
| `>=I`       | Integer compare (accumulator 2 >= accumulator 1) → RLO  |
| `CALL`      | Invoke a function block instance with named parameters (same call syntax as SCL) |
| `JCN`       | Jump to a label if RLO = 0                              |
| `NOP 0`     | No-operation — used here purely as a jump-label target  |

## Notes

- **Q_Done** reads `#Counter_Bottles.QU`, not `.Q` — TIA's counter type
  has no plain `Q` member (learned in LAB3; confirmed via compiler
  autocomplete: CD, CU, CV, LD, PV, QD, QU, R).
- `I_Stop` and `I_EStop` both clear `Running` in this SCL-only version —
  the distinction (graceful vs. immediate) will matter once the GRAFCET
  sequence layer is added on top; this FB just gives GRAFCET a place to
  plug in a "finish current step" condition for Stop without needing to
  change this file.
- The self-retriggering `Timer_Fill` pattern (`IN := Running AND NOT Q`)
  replaces GRAFCET's `delay` instruction, per the assignment's constraint
  that timing must come from an SCL timer block, not a GRAFCET delay.
