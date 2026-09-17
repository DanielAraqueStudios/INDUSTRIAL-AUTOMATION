# Tag Table — LAB4 Servo pulse interface (physical I/O)

Deliberate deviation from the assignment's "pulse must be generated in
AWL" requirement — done in SCL instead, by explicit user decision, aware
this may cost credit on that specific grading point.

Servo feedback (`I_ServoReady`, `I_ServoFault`) are drive-status signals
from the servo's own communication interface, not process sensors — kept
separate from the "no sensors elsewhere" constraint, which applies to
detecting bottle position/process state, not servo health feedback
required for the assignment's own "comunicación bidireccional" mandate.

## Computed placeholder parameters (swap once real servo datasheet exists)

- Pulley diameter: 4 cm → circumference ≈ 12.57 cm
- Bottle diameter (index distance): 5 cm (código's last digit)
- Revolutions per index: 5 / 12.57 ≈ **0.398 rev**
- Servo: 200 pulses/rev (placeholder) → pulses per index ≈ **80**
- Angle per index: 0.398 × 360° ≈ **143.2°**

## Inputs

| Tag name     | Address | Type | Description                                  |
|--------------|---------|------|------------------------------------------------|
| I_ServoReady | %I0.3   | Bool | Servo drive reports ready (from comm/status)  |
| I_ServoFault | %I0.4   | Bool | Servo drive reports fault (from comm/status)  |

## Outputs

| Tag name      | Address | Type | Description                                  |
|---------------|---------|------|------------------------------------------------|
| Q_ServoPulse  | %Q0.3   | Bool | Toggling pulse output driving the servo PTI   |
| Q_ServoDir    | %Q0.4   | Bool | Direction (FALSE=forward/index, TRUE=return)  |
| Q_Positioned  | %Q0.5   | Bool | ON once the commanded pulse count is reached  |
| Q_ServoAlarm  | %Q0.6   | Bool | ON if I_ServoFault is active — process suspended |
