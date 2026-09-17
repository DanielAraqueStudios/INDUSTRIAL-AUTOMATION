# README — creating the TESTING PLC tags in TIA Portal

This walks through entering the physical I/O from `src/TagTable_Test.md`
into TIA Portal's **PLC tags**, so `FB_Test` compiles (it references these
tags by name).

## Steps

1. In the **Project tree**, expand `PLC_1` → double-click **PLC tags**.
2. Use the **Default tag table**, or add a dedicated one: right-click
   **PLC tags** → **Add new tag table**, name it e.g. `Tags_Test`.
3. Double-click the tag table to open the grid: **Name**, **Data type**,
   **Address**, **Comment**.
4. Enter the rows below. Save (Ctrl+S).

| Name   | Data type | Address | Comment               |
|--------|-----------|---------|------------------------|
| I_Test | Bool      | %I0.0   | Physical test input   |
| Q_Test | Bool      | %Q0.0   | Physical test output  |

## Notes

- **Addresses are placeholders** — change the Address column to match your
  real I/O module; keep the **Name** column exact since `FB_Test` references
  `"I_Test"`/`"Q_Test"` by name.
- This must be done **before** `FB_Test`/`Main [OB1]` will compile — the FB
  fails with "identifier not declared" errors otherwise.

## Building it in TIA Portal

1. Create the PLC tags above.
2. Add a new **Function Block** named `FB_Test`, language SCL, paste
   `src/FB_Test.scl`'s body. Compiling it auto-creates `FB_Test_DB`.
3. Open `Main [OB1]`, drag `FB_Test` from the Project tree into the code
   editor (or paste `src/OB1_Test.scl`'s call line) — accept the default
   instance DB name `FB_Test_DB`.
4. Compile everything, download to PLC.
