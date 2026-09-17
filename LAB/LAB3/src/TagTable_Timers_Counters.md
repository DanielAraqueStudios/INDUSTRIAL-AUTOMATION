# Tag Table — LAB3 Timers & Counters (physical I/O + HMI memory tags)

Per project architecture ([[tia-portal-project-architecture]]): physical I/O
and HMI-only trigger bits both live in the **PLC tags table** now (memory
tags convention, same as `TESTING`). Every physical input below is `OR`'d
with its memory-tag counterpart inside `FB_Timers_Counters`, so each block
can be driven from the physical switch **or** the HMI screen. Presets
(PT/PV) and readouts (ET/CV) stay FB-Static/DB-only — no physical hardware
and no HMI-side trigger nature, so no PLC tag needed for those. Addresses
are placeholders pending real module mapping.

## Inputs (physical)

| Tag name       | Address | Type | Description                                  |
|-----------------|---------|------|-----------------------------------------------|
| I_Start_TON     | %I0.0   | Bool | Starts TON timer (on-delay)                   |
| I_Reset_TON     | %I0.1   | Bool | Resets TON (forces Q/ET := 0 while held)      |
| I_Start_TOF     | %I0.2   | Bool | Starts/holds TOF timer (off-delay)            |
| I_Reset_TOF     | %I0.3   | Bool | Resets TOF                                    |
| I_Start_TP      | %I0.4   | Bool | Triggers TP timer (fixed pulse)               |
| I_Reset_TP      | %I0.5   | Bool | Resets TP                                     |
| I_CU_CTU        | %I0.6   | Bool | Count-up pulse input for CTU                  |
| I_Reset_CTU     | %I0.7   | Bool | Resets CTU counter                            |
| I_CD_CTD        | %I1.0   | Bool | Count-down pulse input for CTD                |
| I_Load_CTD      | %I1.1   | Bool | Loads CTD counter with preset value            |
| I_CU_CTUD       | %I1.2   | Bool | Count-up pulse input for CTUD                 |
| I_CD_CTUD       | %I1.3   | Bool | Count-down pulse input for CTUD               |
| I_Reset_CTUD    | %I1.4   | Bool | Resets CTUD counter                           |
| I_Load_CTUD     | %I1.5   | Bool | Loads CTUD counter with preset value           |

## Outputs (physical)

| Tag name       | Address | Type | Description                                  |
|-----------------|---------|------|-----------------------------------------------|
| Q_TON           | %Q0.0   | Bool | TON output (ON after preset time elapses)     |
| Q_TOF           | %Q0.1   | Bool | TOF output (OFF after preset time elapses)    |
| Q_TP            | %Q0.2   | Bool | TP output (pulse for preset duration)         |
| Q_CTU           | %Q0.3   | Bool | CTU output (ON when count reaches preset)     |
| Q_CTD           | %Q0.4   | Bool | CTD output (ON when count reaches 0)          |
| Q_CTUD_QU       | %Q0.5   | Bool | CTUD up-count output                          |
| Q_CTUD_QD       | %Q0.6   | Bool | CTUD down-count output                        |

## HMI memory tags (%M — HMI-only triggers, no physical hardware)

| Tag name        | Address | Type | Description                                  |
|------------------|---------|------|-----------------------------------------------|
| HMI_Start_TON    | %M0.0   | Bool | HMI Start button for TON                      |
| HMI_Reset_TON    | %M0.1   | Bool | HMI Reset button for TON                      |
| HMI_Start_TOF    | %M0.2   | Bool | HMI Start button for TOF                      |
| HMI_Reset_TOF    | %M0.3   | Bool | HMI Reset button for TOF                      |
| HMI_Start_TP     | %M0.4   | Bool | HMI Start button for TP                       |
| HMI_Reset_TP     | %M0.5   | Bool | HMI Reset button for TP                       |
| HMI_CU_CTU       | %M0.6   | Bool | HMI Count-Up button for CTU                   |
| HMI_Reset_CTU    | %M0.7   | Bool | HMI Reset button for CTU                      |
| HMI_CD_CTD       | %M1.0   | Bool | HMI Count-Down button for CTD                 |
| HMI_Load_CTD     | %M1.1   | Bool | HMI Load button for CTD                       |
| HMI_CU_CTUD      | %M1.2   | Bool | HMI Count-Up button for CTUD                  |
| HMI_CD_CTUD      | %M1.3   | Bool | HMI Count-Down button for CTUD                |
| HMI_Reset_CTUD   | %M1.4   | Bool | HMI Reset button for CTUD                     |
| HMI_Load_CTUD    | %M1.5   | Bool | HMI Load button for CTUD                      |

HMI status circles bind to the physical `Q_...` tags (single source of
truth, reachable from either the physical panel or the HMI). HMI trigger
buttons bind to the `HMI_...` memory tags above. HMI setpoint/readout
fields (`Preset_...`, `HMI_PT_..._s`, `HMI_ET_..._s`, `HMI_CV_...`) have no
physical or trigger nature, so they stay FB-Static and bind to
`"FB_Timers_Counters_DB".<tag>`.
