# Tag Table — LAB4 Embotelladora (physical I/O only)

Per project architecture ([[tia-portal-project-architecture]]) and the
assignment constraint: **only Start, Stop, and Emergency Stop are physical
inputs** — no sensors anywhere else in the solution. Addresses are
placeholders pending real module mapping.

Computed parameters (from team ID digits):
- Bottle count target: **17** (sum of cédula's last 3 digits: 4+8+5)
- Fill time per bottle: **3 s** (given by the assignment)
- Bottle base diameter: **5 cm** (código's last digit) — used in the
  mechanical/servo sizing, not in this SCL logic directly.

## Inputs

| Tag name  | Address | Type | Description                                    |
|-----------|---------|------|--------------------------------------------------|
| I_Start   | %I0.0   | Bool | Starts the filling process                        |
| I_Stop    | %I0.1   | Bool | Graceful stop — halts after current cycle step    |
| I_EStop   | %I0.2   | Bool | Emergency stop — immediate halt, overrides all    |

## Outputs

| Tag name  | Address | Type | Description                                    |
|-----------|---------|------|--------------------------------------------------|
| Q_Fill    | %Q0.0   | Bool | ON while a bottle is actively being filled        |
| Q_Running | %Q0.1   | Bool | ON while the process is active (latched by Start) |
| Q_Done    | %Q0.2   | Bool | ON once all 17 bottles have been filled           |

Note: `I_Stop` vs `I_EStop` are intentionally separate — the assignment
requires distinguishing a graceful stop (finishes current step, resumable)
from an emergency stop (immediate, unconditional halt). See
`FB_Embotelladora.scl` for how each is handled differently.
