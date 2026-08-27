# Tabla de Tags PLC — Problema 2 (Alimentador de piezas)

Solo **entradas/salidas físicas** (%I / %Q). Ninguna otra variable va aquí —
toda la lógica interna, banderas, marcas de tiempo y constantes viven en
`FB_Alimentador` (ver `Problema2_AWL.awl`), como memoria estática de su DB de
instancia.

Direcciones son **placeholder** — ajústalas al mapeo real del módulo
posterior del banco de trabajo cuando lo tengas.

## Entradas (%I)

| Tag                 | Dirección | Tipo | Descripción                                                     |
|----------------------|-----------|------|-------------------------------------------------------------------|
| I_Start              | %I0.0     | Bool | Botón Start — común a todo el sistema                            |
| I_Stop               | %I0.1     | Bool | Botón Stop                                                        |
| I_Emergency          | %I0.2     | Bool | Botón Emergencia (1 = pulsado)                                    |
| I_S1_CintaAlimenta   | %I0.3     | Bool | Pulsador que simula "cinta alimenta otra placa" (dispara ciclo)   |
| I_S2_DetectaPieza    | %I0.4     | Bool | Detector de pieza de acero presente                               |
| I_SensorA_Pos1       | %I0.5     | Bool | Cilindro A avanzado — posición 1                                  |
| I_SensorA_Home       | %I0.6     | Bool | Cilindro A retraído                                                |
| I_SensorB_Pos1       | %I0.7     | Bool | Cilindro B avanzado — posición 1                                  |
| I_SensorB_Home       | %I1.0     | Bool | Cilindro B retraído                                                |
| I_SensorC_Pos2       | %I1.1     | Bool | Cilindro C avanzado — posición 2                                  |
| I_SensorC_Home       | %I1.2     | Bool | Cilindro C retraído                                                |
| I_Reset_Falla        | %I1.3     | Bool | Rearme de falla (también disponible como botón HMI)               |

## Salidas (%Q)

| Tag                    | Dirección | Tipo | Descripción                                     |
|--------------------------|-----------|------|----------------------------------------------------|
| Q_CilA_Avanza            | %Q0.0     | Bool | Electroválvula avance cilindro A                  |
| Q_CilB_Avanza            | %Q0.1     | Bool | Electroválvula avance cilindro B                  |
| Q_CilC_Avanza            | %Q0.2     | Bool | Electroválvula avance cilindro C                  |
| Q_Electroiman            | %Q0.3     | Bool | Activación del electroimán (retiene la pieza)      |
| Q_Lampara_Electroiman    | %Q0.4     | Bool | Lámpara indicadora de electroimán activo           |
| Q_Falla                  | %Q0.5     | Bool | Salida de alarma / falla de proceso                |

## HMI (KTP700)

Enlaza directamente a los tags de arriba (Tag Table) **y** a las variables
estáticas del DB de instancia `FB_Alimentador_DB` (p. ej.
`"FB_Alimentador_DB".HMI_Paso_Actual`, `"FB_Alimentador_DB".Falla_Activa`,
`"FB_Alimentador_DB".HMI_Tiempo_Transcurrido_s`) — no se necesita un DB
global aparte para exponer estado a la pantalla.
