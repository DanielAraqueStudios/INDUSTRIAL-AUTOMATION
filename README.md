# Estación de Bombeo — Control Automático y Secuencial (PLC S7-1500)

Reporte técnico en formato IEEE (LaTeX) del diseño de control de una
estación de bombeo de cuatro bombas centrífugas en paralelo, sobre PLC
Siemens S7-1500 / TIA Portal, con estrategia de escalonamiento por
demanda, histéresis/temporización anti-cicleo, rotación *round-robin* y
gestión de fallas.

## Alcance

Cubre las tres actividades del taller:

1. **Análisis del proceso** — entradas, salidas, variables analógicas,
   actuadores, sensores y condiciones de seguridad.
2. **Diagrama P\&ID simplificado** de la estación (dibujado en TikZ,
   directamente en el documento).
3. **Programación de la secuencia de control** en dos lenguajes
   IEC 61131-3: Ladder (LAD) para enclavamientos/seguridad y SFC (S7-GRAPH)
   para el escalonamiento y rotación de bombas, con validación lógica
   frente a los escenarios especificados en el taller.

## Estructura del proyecto

`report.tex` es un archivo **único y autocontenido**: todo el contenido
(incluido el listado del Anexo, antes en un `.txt` externo) está embebido
en el propio `.tex`. La única carpeta relacionada es `images/`, y es
**opcional** — el documento compila igual de bien sin ella.

```
PUMPING_STATION/
├── report.tex                   Reporte completo (IEEEtran, dos columnas,
│                                 autocontenido: no depende de ningún otro
│                                 archivo .tex/.txt para compilar)
└── images/
    ├── README.md                 Especificación de las 7 capturas reales
    │                             esperadas (nombre de archivo, contenido,
    │                             sección del reporte que las consume)
    └── (se completa con capturas de TIA Portal / WinCC / PLCSIM)
```

### Subir a Overleaf

Sube `report.tex` y, si quieres que las figuras reales aparezcan en vez
de los recuadros de espacio reservado, la carpeta `images/` completa
(manteniendo el nombre `images/` — es el que usa `\imgslot` internamente).
No hay ningún otro archivo que subir.

## Requisitos

- Distribución LaTeX con soporte para PDF: TeX Live o MiKTeX (u Overleaf).
- Clase `IEEEtran` y paquetes `tikz`, `booktabs`, `siunitx`, `listings`,
  `etoolbox`, `babel[spanish]` — todos incluidos en una instalación
  estándar `texlive-full` / MiKTeX, o disponibles automáticamente en Overleaf.

## Compilación

```bash
cd PUMPING_STATION
pdflatex report.tex
pdflatex report.tex   # segunda pasada: referencias cruzadas y bibliografía
```

Alternativa con `latexmk`:

```bash
latexmk -pdf report.tex
```

> Nota: este reporte no se ha compilado en el entorno de desarrollo actual
> por no contar con una distribución LaTeX instalada. Se corrigieron tres
> errores de compilación detectados vía Overleaf: (1) una colisión de
> nombre de estilo TikZ (`step`, que choca con la clave nativa
> `/tikz/step`) en el diagrama SFC; (2) guiones bajos sin escapar en los
> nombres de archivo/etiquetas de `\imgslot` (§7–§9), que rompían la
> compilación al ser tipografiados vía `\texttt{}` fuera de modo
> matemático — por eso los archivos esperados en `images/` usan guiones
> (`-`), no guiones bajos; y (3) `babel[spanish]` activa `<` y `>` como
> shorthand para comillas angulares (`<<`/`>>`), lo que rompía cualquier
> uso de `<`/`>` como símbolo matemático (p. ej. `$Q<150$`) aunque
> estuviera entre llaves. `\shorthandoff{<>}` no basta si se llama justo
> tras cargar babel: los shorthands del idioma principal solo se activan
> en `\begin{document}`, así que babel los reactivaba después de mi
> primera corrección; quedó envuelto en `\AtBeginDocument{...}` para que
> se ejecute una vez babel ya activó el idioma. Se verificó el
> balance de llaves y de entornos `\begin`/`\end`, pero se recomienda una
> compilación de control antes de la entrega.

## Figuras: diseño propio vs. evidencia real

- El **P\&ID** (Actividad 2) y el **diagrama SFC** (Sección 7.3) están
  dibujados directamente en TikZ dentro de `report.tex`: son el diseño de
  ingeniería propio, no capturas de pantalla.
- La **evidencia del sistema construido** (árbol de bloques, editor LAD,
  editor S7-GRAPH, tabla de observación, tendencias PLCSIM, pantalla HMI)
  se referencia mediante la macro `\imgslot{archivo}{alto}{caption}{label}`,
  definida en el preámbulo de `report.tex`: si `images/<archivo>` existe,
  se inserta automáticamente; si no, se muestra un recuadro de espacio
  reservado con el nombre pendiente. El documento compila en ambos casos,
  sin necesidad de editar el `.tex` al agregar capturas — basta con
  guardar el PNG en `images/` con el nombre indicado en `images/README.md`.

## Estado y pendientes

- [x] Completar los integrantes reales del grupo (Daniel García Araque).
- [ ] Construir el proyecto en TIA Portal y capturar las 7 imágenes
      listadas en `images/README.md`.
- [ ] Ejecutar validación en PLCSIM sobre los escenarios de la Tabla de
      validación lógica del reporte.
- [ ] Compilación de control con `pdflatex`/`latexmk` antes de la entrega.

## Referencias normativas citadas

- IEC 61131-3 — Programmable Controllers, Part 3: Programming Languages.
- ANSI/ISA-5.1-2009 — Instrumentation Symbols and Identification.
- Siemens AG, *S7-1500, ET 200MP — System Manual*.
