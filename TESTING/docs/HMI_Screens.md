# TESTING — HMI screen (2 independent dual-control channels)

Same architecture as [[lab3-overview]]: physical I/O stays in the Tag Table
(`src/TagTable_Test.md`), everything else lives in `FB_Test`'s instance DB
(`FB_Test_DB`). Two independent channels — each HMI button OR's with its
own physical input to drive its own physical output.

## FB_Test — Static variables

Added via `FB_Test`'s interface table, **Static** section:

| Name         | Data type | Comment                                   |
|--------------|-----------|----------------------------------------------|
| HMI_Button1  | Bool      | HMI-only trigger for channel 1, no physical equivalent |
| HMI_Button2  | Bool      | HMI-only trigger for channel 2, no physical equivalent |

Code pane (body only):

```
"Q_Test1" := "I_Test1" OR #HMI_Button1;
"Q_Test2" := "I_Test2" OR #HMI_Button2;
```

## PLC tags (see `src/TagTable_Test.md`)

| Tag name | Address | Type | Description             |
|----------|---------|------|---------------------------|
| I_Test1  | %I0.0   | Bool | Physical test input 1   |
| Q_Test1  | %Q0.0   | Bool | Physical test output 1  |
| I_Test2  | %I0.1   | Bool | Physical test input 2   |
| Q_Test2  | %Q0.1   | Bool | Physical test output 2  |

`HMI_Button1`/`HMI_Button2` are **not** PLC tags — no physical wire, so
they stay inside `FB_Test_DB` only.

## HMI screen elements

| Element | Type | Bound tag | Notes |
|---|---|---|---|
| Button 1 | Button | `"FB_Test_DB".HMI_Button1` | Event: Press → `SetBitWhileKeyPressed`. OR'd with physical `I_Test1`. |
| Circle 1 | Circle, Appearance | `"Q_Test1"` (physical output tag) | Green = 1 (ON), gray/red = 0 (OFF). |
| Button 2 | Button | `"FB_Test_DB".HMI_Button2` | Event: Press → `SetBitWhileKeyPressed`. OR'd with physical `I_Test2`. |
| Circle 2 | Circle, Appearance | `"Q_Test2"` (physical output tag) | Green = 1 (ON), gray/red = 0 (OFF). |

## Build steps

1. Add `HMI_Button1 : Bool` and `HMI_Button2 : Bool` to `FB_Test`'s Static
   interface section.
2. Set the code pane to the two-line body above. Compile.
3. Add `I_Test2`/`Q_Test2` to the PLC tags table alongside the existing
   `I_Test1`/`Q_Test1` (renamed from `I_Test`/`Q_Test`).
4. On the HMI screen: drag **2 Buttons**, bind each Press event
   (`SetBitWhileKeyPressed`) to its own `"FB_Test_DB".HMI_ButtonN`.
5. Drag **2 Circles**, Animations → Appearance, bind each to its own
   `"Q_TestN"` (0 = gray/red, 1 = green).
6. Compile `FB_Test`, `Main [OB1]`, and the HMI. Download both. Test each
   channel independently: physical input 1 → circle 1; HMI button 2 →
   circle 2; etc. — channels don't affect each other.
