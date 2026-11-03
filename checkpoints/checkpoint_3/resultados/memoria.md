# Resultados de memoria y entrenamiento

Comparar datos por JID, sin fijar direcciones físicas ni bases lógicas devueltas por ALLOC.

| Caso | Resultado observable |
| --- | --- |
| Reserva/liberación | un REPORT: 48 6F 6C 61 |
| Aritmética | un REPORT del patrón A |
| Cruce de página | AX=6; un REPORT: 06 00 00 00 |
| Mini LRU / CLOCK-M | ocho REPORT del patrón A, recuperados después de reemplazos |
| Aislamiento | ocho REPORT A para el Job A y ocho REPORT B para el Job B |
| Offload lleno | finalización por error del primer hijo; un REPORT 48 6F 6C 61 del segundo |

## Patrón A

Valores iniciales: [100, 1, 0, 0, 2].
FORWARD: AX=100 y salida=100.
BACKWARD: AX=0 y gradiente=0.
UPDATE usando salida como gradiente: estado=(2+100)/2=51; pesos=100−51=49.
Valores finales: **[49, 1, 100, 0, 51]**.

Bytes: `31 00 00 00 01 00 00 00 64 00 00 00 00 00 00 00 33 00 00 00`.

## Patrón B

Valores iniciales: [200, 1, 0, 0, 4].
FORWARD: AX=200 y salida=200.
BACKWARD: AX=0 y gradiente=0.
UPDATE usando salida como gradiente: estado=(4+200)/2=102; pesos=200−102=98.
Valores finales: **[98, 1, 200, 0, 102]**.

Bytes: `62 00 00 00 01 00 00 00 C8 00 00 00 00 00 00 00 66 00 00 00`.

## Reintentos y estados

En un Page Fault, el Job hace EXEC → BLOCKED → READY y vuelve a ejecutar el mismo PC. No se modifica memoria ni registros antes de resolver todas las traducciones. Si hay varios fallos para una misma instrucción, puede repetirse el PC varias veces; no exigir un número universal de reintentos en escenarios concurrentes.

Una syscall bloqueante ya atendida debe continuar en la siguiente instrucción al volver a EXEC. ALLOC, FREE e INIT_JOB no bloquean ni liberan el Core. La finalización libera recursos incluso si el Job no llegó a FREE.

En todos los casos se observan solo rangos inicializados. La reserva de Offload se redondea por asignación a páginas completas; no se presume inicialización a cero ni reutilización de una dirección concreta.
