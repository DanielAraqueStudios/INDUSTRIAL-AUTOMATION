# LAB3 HMI — single screen (Timers + Counters)

Dual-driven demo ([[tia-portal-project-architecture]]): each trigger can
also be fired from a physical input (see `TagTable_Timers_Counters.md`),
OR'd together inside `FB_Timers_Counters` — the HMI is one of two ways in,
not the only way. **Trigger buttons bind to PLC memory tags** (`HMI_...`,
`%M`, same convention as `TESTING`) — not FB Static variables — so both the
physical input and the HMI button live side by side in the PLC tags table,
imported into the HMI tag table directly from the PLC (paths matched).
Setpoint/readout fields (no physical or trigger nature) bind to
`"Block_1_DB".<tag>` (the FB's instance DB — rename to
`FB_Timers_Counters_DB` if you renamed the FB); status circles bind to the
**physical** `Q_...` output tags instead of a DB copy, since those already
reflect the block's state regardless of which path drove it.

**Status circle convention:** green = ON (Q/QU/QD = TRUE), gray/red = OFF —
via Animations → Appearance, bound to the tag's Bool value (0 = gray/red,
1 = green).

**Buttons:** all momentary (press = TRUE, release = FALSE) unless noted.

All 6 blocks live on **one screen**, laid out in two rows or two columns
(Timers on one side, Counters on the other) — purely a layout choice, no
functional difference from a multi-screen design.

---

## Timers section

### TON block
| Element | Type | Bound tag | Notes |
|---|---|---|---|
| Preset time (PT), s | I/O field, editable Real | `"Block_1_DB".HMI_PT_TON_s` | On-delay setpoint |
| Elapsed time (ET), s | I/O field, read-only | `"Block_1_DB".HMI_ET_TON_s` | |
| Start | Button | `"HMI_Start_TON"` (PLC memory tag) | OR'd with physical `I_Start_TON`; hold ≥ PT to trip |
| Reset | Button | `"HMI_Reset_TON"` (PLC memory tag) | Folded into IN (AND NOT); instant reset |
| Status circle | Circle, Appearance | `"Q_TON"` (physical output tag) | ON once ET ≥ PT |

### TOF block
| Element | Type | Bound tag | Notes |
|---|---|---|---|
| Preset time (PT), s | I/O field, editable Real | `"Block_1_DB".HMI_PT_TOF_s` | Off-delay setpoint |
| Elapsed time (ET), s | I/O field, read-only | `"Block_1_DB".HMI_ET_TOF_s` | |
| Start | Button | `"HMI_Start_TOF"` (PLC memory tag) | OR'd with physical `I_Start_TOF` |
| Reset | Button | `"HMI_Reset_TOF"` (PLC memory tag) | Folded into IN; Q drop delayed by PT after Reset |
| Status circle | Circle, Appearance | `"Q_TOF"` (physical output tag) | ON while active/holding |

### TP block
| Element | Type | Bound tag | Notes |
|---|---|---|---|
| Preset time (PT), s | I/O field, editable Real | `"Block_1_DB".HMI_PT_TP_s` | Pulse width |
| Elapsed time (ET), s | I/O field, read-only | `"Block_1_DB".HMI_ET_TP_s` | |
| Start | Button | `"HMI_Start_TP"` (PLC memory tag) | OR'd with physical `I_Start_TP`; rising edge triggers pulse |
| Reset | Button | `"HMI_Reset_TP"` (PLC memory tag) | Only blocks a NEW pulse; can't cut a running one short |
| Status circle | Circle, Appearance | `"Q_TP"` (physical output tag) | ON for the pulse duration |

---

## Counters section

### CTU block
| Element | Type | Bound tag | Notes |
|---|---|---|---|
| Preset (PV) | I/O field, editable Int | `"Block_1_DB".Preset_CTU` | Count-up target |
| Current value (CV) | I/O field, read-only | `"Block_1_DB".HMI_CV_CTU` | |
| Count Up | Button | `"HMI_CU_CTU"` (PLC memory tag) | OR'd with physical `I_CU_CTU` |
| Reset | Button | `"HMI_Reset_CTU"` (PLC memory tag) | OR'd with physical `I_Reset_CTU`; CTU has native R |
| Status circle | Circle, Appearance | `"Q_CTU"` (physical output tag, = `.QU`) | ON once CV ≥ PV |

### CTD block
| Element | Type | Bound tag | Notes |
|---|---|---|---|
| Preset (PV) | I/O field, editable Int | `"Block_1_DB".Preset_CTD` | Count-down start value |
| Current value (CV) | I/O field, read-only | `"Block_1_DB".HMI_CV_CTD` | |
| Count Down | Button | `"HMI_CD_CTD"` (PLC memory tag) | OR'd with physical `I_CD_CTD` |
| Load | Button | `"HMI_Load_CTD"` (PLC memory tag) | OR'd with physical `I_Load_CTD`; CTD has no R, LD is reset/reload |
| Status circle | Circle, Appearance | `"Q_CTD"` (physical output tag, = `.QD`) | ON once CV ≤ 0 |

### CTUD block
| Element | Type | Bound tag | Notes |
|---|---|---|---|
| Preset (PV) | I/O field, editable Int | `"Block_1_DB".Preset_CTUD` | Count-up target / load value |
| Current value (CV) | I/O field, read-only | `"Block_1_DB".HMI_CV_CTUD` | |
| Count Up | Button | `"HMI_CU_CTUD"` (PLC memory tag) | OR'd with physical `I_CU_CTUD` |
| Count Down | Button | `"HMI_CD_CTUD"` (PLC memory tag) | OR'd with physical `I_CD_CTUD` |
| Reset | Button | `"HMI_Reset_CTUD"` (PLC memory tag) | OR'd with physical `I_Reset_CTUD` |
| Load | Button | `"HMI_Load_CTUD"` (PLC memory tag) | OR'd with physical `I_Load_CTUD` |
| Status circle (up) | Circle, Appearance | `"Q_CTUD_QU"` (physical output tag) | ON once CV ≥ PV |
| Status circle (down) | Circle, Appearance | `"Q_CTUD_QD"` (physical output tag) | ON once CV ≤ 0 |

---

**How to apply:** if the demo grows more blocks, keep the pattern — a
physical Tag Table entry + an `HMI_...` **PLC memory tag** OR'd together
for every trigger, a plain FB-Static/DB variable for every setpoint/readout
with no physical or trigger nature, and the status circle bound to the
physical `Q` tag. See [[lab3-overview]].
