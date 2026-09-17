# README — creating the LAB3 PLC tags in TIA Portal

This walks through entering the physical I/O from
`src/TagTable_Timers_Counters.md` into TIA Portal's **PLC tags**, so
`FB_Timers_Counters` compiles (it references these tags by name).

## Steps

1. In the **Project tree**, expand `PLC_1` → double-click **PLC tags**.
2. By default there's a **Default tag table**. You can use it directly, or
   add a dedicated one: right-click **PLC tags** → **Add new tag table**,
   name it e.g. `Tags_Timers_Counters`.
3. Double-click the tag table to open it. You'll get a spreadsheet-style
   grid with columns: **Name**, **Data type**, **Address**, **Comment**.
4. Enter each row from the **Inputs** table below, then **Outputs**, then
   **HMI memory tags** — one PLC tag per row. Press Enter/Tab to move
   between cells; TIA auto-adds a new blank row at the bottom.
5. Save (Ctrl+S). No need to compile the tag table itself — it's checked
   automatically when you compile `FB_Timers_Counters`/`Main [OB1]`.

## Inputs

| Name         | Data type | Address | Comment                                     |
|--------------|-----------|---------|----------------------------------------------|
| I_Start_TON  | Bool      | %I0.0   | Starts TON timer (on-delay)                   |
| I_Reset_TON  | Bool      | %I0.1   | Resets TON (forces Q/ET := 0 while held)      |
| I_Start_TOF  | Bool      | %I0.2   | Starts/holds TOF timer (off-delay)            |
| I_Reset_TOF  | Bool      | %I0.3   | Resets TOF                                    |
| I_Start_TP   | Bool      | %I0.4   | Triggers TP timer (fixed pulse)               |
| I_Reset_TP   | Bool      | %I0.5   | Resets TP                                     |
| I_CU_CTU     | Bool      | %I0.6   | Count-up pulse input for CTU                  |
| I_Reset_CTU  | Bool      | %I0.7   | Resets CTU counter                            |
| I_CD_CTD     | Bool      | %I1.0   | Count-down pulse input for CTD                |
| I_Load_CTD   | Bool      | %I1.1   | Loads CTD counter with preset value           |
| I_CU_CTUD    | Bool      | %I1.2   | Count-up pulse input for CTUD                 |
| I_CD_CTUD    | Bool      | %I1.3   | Count-down pulse input for CTUD               |
| I_Reset_CTUD | Bool      | %I1.4   | Resets CTUD counter                           |
| I_Load_CTUD  | Bool      | %I1.5   | Loads CTUD counter with preset value          |

## Outputs

| Name        | Data type | Address | Comment                                     |
|-------------|-----------|---------|------------------------------------------------|
| Q_TON       | Bool      | %Q0.0   | TON output (ON after preset time elapses)      |
| Q_TOF       | Bool      | %Q0.1   | TOF output (OFF after preset time elapses)     |
| Q_TP        | Bool      | %Q0.2   | TP output (pulse for preset duration)          |
| Q_CTU       | Bool      | %Q0.3   | CTU output (ON when count reaches preset)      |
| Q_CTD       | Bool      | %Q0.4   | CTD output (ON when count reaches 0)           |
| Q_CTUD_QU   | Bool      | %Q0.5   | CTUD up-count output                           |
| Q_CTUD_QD   | Bool      | %Q0.6   | CTUD down-count output                         |

## HMI memory tags (%M)

HMI trigger buttons bind to these — no physical hardware behind them, but
they're PLC tags (memory bits), not FB Static variables, so the physical
input and the HMI button both live in the same tag table and get OR'd in
`FB_Timers_Counters`.

| Name           | Data type | Address | Comment                        |
|-----------------|-----------|---------|----------------------------------|
| HMI_Start_TON   | Bool      | %M0.0   | HMI Start button for TON        |
| HMI_Reset_TON   | Bool      | %M0.1   | HMI Reset button for TON        |
| HMI_Start_TOF   | Bool      | %M0.2   | HMI Start button for TOF        |
| HMI_Reset_TOF   | Bool      | %M0.3   | HMI Reset button for TOF        |
| HMI_Start_TP    | Bool      | %M0.4   | HMI Start button for TP         |
| HMI_Reset_TP    | Bool      | %M0.5   | HMI Reset button for TP         |
| HMI_CU_CTU      | Bool      | %M0.6   | HMI Count-Up button for CTU     |
| HMI_Reset_CTU   | Bool      | %M0.7   | HMI Reset button for CTU        |
| HMI_CD_CTD      | Bool      | %M1.0   | HMI Count-Down button for CTD   |
| HMI_Load_CTD    | Bool      | %M1.1   | HMI Load button for CTD         |
| HMI_CU_CTUD     | Bool      | %M1.2   | HMI Count-Up button for CTUD    |
| HMI_CD_CTUD     | Bool      | %M1.3   | HMI Count-Down button for CTUD  |
| HMI_Reset_CTUD  | Bool      | %M1.4   | HMI Reset button for CTUD       |
| HMI_Load_CTUD   | Bool      | %M1.5   | HMI Load button for CTUD        |

## FB_Timers_Counters — Static section variables (not PLC tags)

These are declared in `FB_Timers_Counters`'s **interface table, Static
section** (this is the FB's `VAR` block — TIA shows it as a table, not
typed text). They live inside the FB's instance DB
(`FB_Timers_Counters_DB`) once the FB compiles. Every one of these is
referenced in the code body, so the FB won't compile without them.

| Name           | Data type | Default | Comment                              |
|-----------------|-----------|---------|------------------------------------------|
| HMI_PT_TON_s    | Real      | 5.0     | TON preset time, seconds                 |
| HMI_PT_TOF_s    | Real      | 5.0     | TOF preset time, seconds                 |
| HMI_PT_TP_s     | Real      | 3.0     | TP pulse width, seconds                  |
| Preset_TON      | Time      |         | Converted TON preset (used by the block) |
| Preset_TOF      | Time      |         | Converted TOF preset                     |
| Preset_TP       | Time      |         | Converted TP preset                      |
| Timer_TON       | TON       |         | TON instance                             |
| Timer_TOF       | TOF       |         | TOF instance                             |
| Timer_TP        | TP        |         | TP instance                              |
| HMI_ET_TON_s    | Real      |         | TON elapsed time readout, seconds        |
| HMI_ET_TOF_s    | Real      |         | TOF elapsed time readout, seconds        |
| HMI_ET_TP_s     | Real      |         | TP elapsed time readout, seconds         |
| Preset_CTU      | Int       | 10      | CTU count-up target                      |
| Preset_CTD      | Int       | 10      | CTD count-down start value               |
| Preset_CTUD     | Int       | 10      | CTUD count-up target / load value        |
| Counter_CTU     | CTU       |         | CTU instance                             |
| Counter_CTD     | CTD       |         | CTD instance                             |
| Counter_CTUD    | CTUD      |         | CTUD instance                            |
| HMI_CV_CTU      | Int       |         | CTU current value readout                |
| HMI_CV_CTD      | Int       |         | CTD current value readout                |
| HMI_CV_CTUD     | Int       |         | CTUD current value readout               |

**Recommended build order (incremental, to catch typos early):** add and
compile one block's variables + its code line at a time, rather than all
20 rows and the full body at once:
1. TON's 4 rows + its code section → compile.
2. TOF's 4 rows + its code section → compile.
3. TP's 4 rows + its code section → compile.
4. CTU's 4 rows + its code section → compile.
5. CTD's 4 rows + its code section → compile.
6. CTUD's 4 rows + its code section → compile.

Each step only ever surfaces errors from what you just added.

## Notes

- **Addresses are placeholders.** If your real I/O module maps to
  different byte/bit addresses, change the **Address** column only — the
  **Name** column is what `FB_Timers_Counters` references (`"I_Start_TON"`,
  `"Q_TON"`, `"HMI_Start_TON"`, etc.), so names must stay exactly as above,
  addresses can move freely. `%M` addresses for the HMI tags can also move
  freely as long as they don't collide with each other.
- Presets (`Preset_...`, `HMI_PT_..._s`) and readouts (`HMI_ET_..._s`,
  `HMI_CV_...`) are **not** PLC tags — they live inside
  `FB_Timers_Counters`'s instance DB (declared as Static in the FB's
  interface table) and don't need any entry here.
- This must be done **before** `FB_Timers_Counters`/`Main [OB1]` will
  compile clean — the FB fails to compile with "identifier not declared"
  errors on any `"I_..."`/`"Q_..."`/`"HMI_..."` tag that isn't in the PLC
  tag table yet.
