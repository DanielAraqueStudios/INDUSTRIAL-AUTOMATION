# Decisión pendiente: ¿qué hace que una pieza vaya a Pos.1 o a Pos.2?

## Contexto (Problema 2 — Alimentador de piezas, AWL)

Según la Figura 2, el cilindro **A** baja diagonalmente y centra la pieza
(el electroimán la retiene), el cilindro **B** la asienta sobre la cinta de
transporte, y el cilindro **C** desplaza lateralmente todo el conjunto para
que la pieza caiga en **Pos.1** o en **Pos.2** de la cinta.

El enunciado dice: *"La secuencia de movimientos hasta la posición 1 está a
cargo de los cilindros A y B. Para alcanzar la posición 2, se aplican las
mismas secuencias que para la posición 1, aunque sumándose el cilindro C."*

Esto describe **dos rutas alternativas para piezas distintas** (no una
secuencia en cascada de una sola pieza pasando por dos posiciones). Falta
definir qué condición del proceso elige la ruta A+B (Pos.1) vs. A+B+C
(Pos.2) para cada pieza que llega.

## Opciones evaluadas

### 1. Alternancia automática por ciclo
- Cada vez que se dispara un ciclo nuevo (flanco de S1 + S2 detecta pieza),
  se invierte un bit de memoria (`Toggle_Posicion`) sin usar un contador IEC
  (solo un `NOT` sobre un bit, permitido porque no es CTU/CTD).
- 1ª pieza → Pos.1, 2ª pieza → Pos.2, 3ª → Pos.1, y así sucesivamente.
- **Ventaja**: no requiere sensores adicionales; encaja con la restricción de
  "no conteo" porque alternar un bit no es "contar".
- **Riesgo**: si el proceso se detiene a mitad de secuencia (Stop/Emergencia)
  y se reinicia, el bit de alternancia puede desincronizarse de qué posición
  está realmente libre en la cinta física.

### 2. Pos.1 siempre primero; Pos.2 solo si Pos.1 ya está ocupada
- Requiere un sensor adicional en la cinta (a nivel de Pos.1) que indique
  "posición ocupada por una placa".
- Si Pos.1 está libre → ruta A+B. Si Pos.1 está ocupada → ruta A+B+C
  (deposita en Pos.2).
- **Ventaja**: refleja el estado físico real de la cinta, más robusto ante
  paradas/reinicios que la opción 1.
- **Riesgo**: necesita un sensor que no aparece listado explícitamente en el
  enunciado ni en la Figura 2 (solo se ven "Pos.1"/"Pos.2" como marcas en la
  cinta, sin símbolo de sensor dibujado ahí).

### 3. Otra regla (a definir por el usuario/guía de laboratorio)
- Es posible que el diagrama de pasos (Grafcet) que acompaña a la Figura 2
  en la guía original defina explícitamente la condición de selección, y que
  no haya sido transcrita en el enunciado de texto que se compartió aquí.

## Estado
**Pendiente de decisión del usuario.** No se ha modificado el AWL del
Problema 2 (`src/Problema2_AWL.awl`, ahora estructurado como
`FUNCTION_BLOCK "FB_Alimentador"` — ver nota de arquitectura abajo) todavía
para esta lógica de selección de posición — sigue con el supuesto anterior
(A→B→C en cascada para la misma pieza), que se sabe **incorrecto** a la luz
de la Figura 2 y debe reescribirse una vez se resuelva esta decisión.

## Nota de arquitectura (2026-08-27)
`src/Problema2_AWL.awl` se reestructuró para seguir la plantilla de proyecto
TIA Portal preferida por el usuario:
- `src/TagTable_Problema2.md` → solo E/S físicas.
- `src/Problema2_AWL.awl` → `FUNCTION_BLOCK "FB_Alimentador"`, con TODA la
  lógica y variables internas como `VAR` (memoria estática de su DB de
  instancia `FB_Alimentador_DB`), en vez del DB global `DB_Global2` anterior
  (eliminado). Variables internas ahora se referencian con `#Tag` en vez de
  `"DB_Global2".Tag`.
- `src/OB1_Problema2.awl` → una sola línea, `CALL "FB_Alimentador_DB"`.
- HMI enlaza directo a `"FB_Alimentador_DB".<tag>` y a la Tag Table física.

Esto NO cambia la lógica de negocio ni resuelve la decisión Pos.1/Pos.2 de
arriba — solo reorganiza dónde vive cada variable.
