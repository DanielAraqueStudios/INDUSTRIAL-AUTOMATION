# Tag Table — TESTING (physical I/O only)

Per project architecture ([[tia-portal-project-architecture]]): this table
holds **only physical I/O**. Two independent channels, each with its own
physical input/output pair, both also HMI-controllable (see
`docs/HMI_Screens.md`). Addresses are placeholders pending real module
mapping.

## Inputs

| Tag name | Address | Type | Description               |
|----------|---------|------|-----------------------------|
| I_Test1  | %I0.0   | Bool | Physical test input 1      |
| I_Test2  | %I0.1   | Bool | Physical test input 2      |

## Outputs

| Tag name | Address | Type | Description                |
|----------|---------|------|------------------------------|
| Q_Test1  | %Q0.0   | Bool | Physical test output 1     |
| Q_Test2  | %Q0.1   | Bool | Physical test output 2     |
