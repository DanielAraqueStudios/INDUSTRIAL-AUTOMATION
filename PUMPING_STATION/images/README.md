# Capturas reales pendientes

Coloca aquí las capturas de pantalla reales a medida que construyas el
proyecto en TIA Portal / WinCC / PLCSIM. `report.tex` las inserta
automáticamente (macro `\imgslot`) en cuanto detecta el archivo con el
nombre exacto indicado; mientras el archivo no exista, se muestra un
recuadro de espacio reservado y el documento sigue compilando sin errores.

| Archivo esperado | Contenido | Sección del reporte |
|---|---|---|
| `01-project-tree.png` | Árbol del proyecto TIA Portal (OB/FB/FC/DB) | §7.1 Arquitectura de bloques |
| `02-lad-networks-1-3.png` | Editor LAD: Networks 1–3 (enclavamientos/seguridad) | §7.2 Lógica LAD |
| `03-lad-network-4.png` | Editor LAD: Network 4 replicada B1–B4 | §7.2 Lógica LAD |
| `04-graph-sequence.png` | Editor S7-GRAPH: secuencia S0–S4 como construida | §7.3 Secuencia SFC/GRAPH |
| `05-watch-rotacion.png` | Watch table en línea: `idx_lider`, `orden[]` | §7.4 Algoritmo de rotación |
| `06-plcsim-trend.png` | Tendencia PLCSIM de los escenarios de validación | §8 Validación lógica |
| `07-hmi-runtime.png` | Pantalla de proceso HMI (WinCC runtime) | §9 HMI |

> Los nombres de archivo usan guiones (`-`), no guiones bajos (`_`): un
> guion bajo sin escapar en un nombre de archivo que `report.tex` typesets
> vía `\texttt{}` rompe la compilación (LaTeX interpreta `_` fuera de modo
> matemático como inicio de subíndice). No renombres estos archivos con `_`.

Formato recomendado: PNG, ancho ≥1200 px (se escala automáticamente al
ancho de columna). No es necesario recortar a una relación de aspecto
específica.
