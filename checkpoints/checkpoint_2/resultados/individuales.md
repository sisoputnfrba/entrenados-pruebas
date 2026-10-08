# Resultados esperados — Checkpoint 2

PC desde 0. Los registros finales y las secuencias de FETCH se aplican tanto a FIFO como a RR; en RR se concatenan los turnos de cada Job. El PC de EXIT identifica la instrucción de salida, no el PC guardado luego de finalizar.

## Resumen

| Archivo | Instrucciones (incluye EXIT) | PC de EXIT |
| --- | ---: | ---: |
| [01_noop.asm](../scripts/individuales/01_noop.asm) | 3 | 2 |
| [02_aritmetica.asm](../scripts/individuales/02_aritmetica.asm) | 8 | 7 |
| [03_jnz_cero.asm](../scripts/individuales/03_jnz_cero.asm) | 5 | 4 |
| [04_jnz_salto.asm](../scripts/individuales/04_jnz_salto.asm) | 4 | 5 |
| [05_loop.asm](../scripts/individuales/05_loop.asm) | 19 | 6 |
| [06_contexto_a.asm](../scripts/individuales/06_contexto_a.asm) | 814 | 17 |
| [07_contexto_b.asm](../scripts/individuales/07_contexto_b.asm) | 614 | 17 |
| [08_corto.asm](../scripts/individuales/08_corto.asm) | 3 | 2 |
| [09_registros_cero.asm](../scripts/individuales/09_registros_cero.asm) | 14 | 13 |

## 01_noop.asm

### Registros finales

| Registro | Valor |
| --- | ---: |
| AX | 0 |
| BX | 0 |
| CX | 0 |
| DX | 0 |
| P0 | 0 |
| P1 | 0 |
| P2 | 0 |
| P3 | 0 |
| P4 | 0 |
| P5 | 0 |
| E1 | 0 |
| E2 | 0 |
| E3 | 0 |

### Secuencia de FETCH (PC)

`0 → 1 → 2`

## 02_aritmetica.asm

### Registros finales

| Registro | Valor |
| --- | ---: |
| AX | 8 |
| BX | 0 |
| CX | 10 |
| DX | 0 |
| P0 | 0 |
| P1 | 0 |
| P2 | 0 |
| P3 | 0 |
| P4 | 0 |
| P5 | 0 |
| E1 | 0 |
| E2 | 0 |
| E3 | 0 |

### Secuencia de FETCH (PC)

`0 → 1 → 2 → 3 → 4 → 5 → 6 → 7`

## 03_jnz_cero.asm

### Registros finales

| Registro | Valor |
| --- | ---: |
| AX | 0 |
| BX | 7 |
| CX | 9 |
| DX | 0 |
| P0 | 0 |
| P1 | 0 |
| P2 | 0 |
| P3 | 0 |
| P4 | 0 |
| P5 | 0 |
| E1 | 0 |
| E2 | 0 |
| E3 | 0 |

### Secuencia de FETCH (PC)

`0 → 1 → 2 → 3 → 4`

## 04_jnz_salto.asm

### Registros finales

| Registro | Valor |
| --- | ---: |
| AX | 1 |
| BX | 0 |
| CX | 0 |
| DX | 42 |
| P0 | 0 |
| P1 | 0 |
| P2 | 0 |
| P3 | 0 |
| P4 | 0 |
| P5 | 0 |
| E1 | 0 |
| E2 | 0 |
| E3 | 0 |

### Secuencia de FETCH (PC)

`0 → 1 → 4 → 5`

## 05_loop.asm

### Registros finales

| Registro | Valor |
| --- | ---: |
| AX | 0 |
| BX | 15 |
| CX | 15 |
| DX | 0 |
| P0 | 0 |
| P1 | 0 |
| P2 | 0 |
| P3 | 0 |
| P4 | 0 |
| P5 | 0 |
| E1 | 0 |
| E2 | 0 |
| E3 | 0 |

### Secuencia de FETCH (PC)

`0 → 1 → 2 → 3 → 4 → 2 → 3 → 4 → 2 → 3 → 4 → 2 → 3 → 4 → 2 → 3 → 4 → 5 → 6`

## 06_contexto_a.asm

### Registros finales

| Registro | Valor |
| --- | ---: |
| AX | 0 |
| BX | 200 |
| CX | 1 |
| DX | 111 |
| P0 | 1001 |
| P1 | 1002 |
| P2 | 1003 |
| P3 | 1004 |
| P4 | 1005 |
| P5 | 1006 |
| E1 | 1007 |
| E2 | 1008 |
| E3 | 1009 |

### Secuencia de FETCH (PC)

1. Inicialización: `0 → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10 → 11 → 12`.
2. Loop: `13 → 14 → 15 → 16`, repetido **200 veces**.
3. Finalización: `17` (EXIT).

## 07_contexto_b.asm

### Registros finales

| Registro | Valor |
| --- | ---: |
| AX | 0 |
| BX | 1300 |
| CX | 2 |
| DX | 222 |
| P0 | 2001 |
| P1 | 2002 |
| P2 | 2003 |
| P3 | 2004 |
| P4 | 2005 |
| P5 | 2006 |
| E1 | 2007 |
| E2 | 2008 |
| E3 | 2009 |

### Secuencia de FETCH (PC)

1. Inicialización: `0 → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10 → 11 → 12`.
2. Loop: `13 → 14 → 15 → 16`, repetido **150 veces**.
3. Finalización: `17` (EXIT).

## 08_corto.asm

### Registros finales

| Registro | Valor |
| --- | ---: |
| AX | 77 |
| BX | 0 |
| CX | 0 |
| DX | 0 |
| P0 | 0 |
| P1 | 0 |
| P2 | 0 |
| P3 | 0 |
| P4 | 0 |
| P5 | 0 |
| E1 | 0 |
| E2 | 0 |
| E3 | 0 |

### Secuencia de FETCH (PC)

`0 → 1 → 2`

## 09_registros_cero.asm

### Registros finales

| Registro | Valor |
| --- | ---: |
| AX | 1 |
| BX | 2 |
| CX | 3 |
| DX | 4 |
| P0 | 5 |
| P1 | 6 |
| P2 | 7 |
| P3 | 8 |
| P4 | 9 |
| P5 | 10 |
| E1 | 11 |
| E2 | 12 |
| E3 | 13 |

### Secuencia de FETCH (PC)

`0 → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10 → 11 → 12 → 13`
