# Migración de las pruebas del checkpoint 3

Origen: `mesaglio/entrenados-checkpoint-2`, carpeta `checkpoint-3/`, commit `ed76810`.
Destino: `sisoputnfrba/entrenados-pruebas`, carpeta `checkpoints/checkpoint_3/`.
Rama de integración: `feat/incorporar-checkpoint-3`, basada en `main` (`3fee8ac`).

Se sigue el [criterio usado para CP2](migracion-checkpoint-2.md): cada etapa es autocontenida y se ejecuta desde su propia carpeta. CP2 y el ejemplo de entrenamiento conservan el contenido de la base productiva. Esta incorporación se mantiene en una rama separada; no se integra en `main`.

## Mapa de archivos

| Origen dentro de `checkpoint-3/` | Destino dentro de `checkpoints/checkpoint_3/` |
| --- | --- |
| `README.md`, `docs/`, `resultados/` | Mismos documentos, con rutas y referencias adaptadas |
| `escenarios/` | Mismos 11 escenarios y 41 configs; solo cambian las rutas |
| `scripts/*.txt` | `scripts/*.asm`: 14 Jobs y launchers |
| `datos/` | README y 5 fixtures binarios conservados byte a byte |
| `../runtime/checkpoint-3/<escenario>/` (generado) | `runtime/<escenario>/` (generado, ignorado por `**/runtime/`) |

Se trasladan los 76 archivos versionados. Las instrucciones se conservan; en los launchers solo cambia la extensión de los argumentos de `INIT_JOB`. Los resultados esperados y el alcance de las pruebas se mantienen. El índice raíz enlaza CP3 y este documento.

## Directorio de trabajo y resolución de rutas

Desde la raíz del clon, en cada terminal:

```bash
cd checkpoints/checkpoint_3
```

- `PATH_INSTRUCCIONES=./scripts/`; Job inicial e `INIT_JOB` usan nombres relativos a esa carpeta, por ejemplo `launcher_lock.asm`.
- `PATH_DATOS=./datos/`; `LOAD_BATCH` conserva los nombres de los archivos `.bin`.
- `PATH_OFFLOAD`, `PATH_REPORTES` y `PATH_STORAGE` usan `./runtime/<escenario>/`.
- Los comandos pasan configs absolutas con `"$PWD/escenarios/..."`; `$PWD` debe ser la carpeta de CP3.
- Los README preparan `runtime/<escenario>/storage/`; su padre contiene Offload y reportes. No crear los archivos de salida vacíos por adelantado.
- El enlace de regreso a CP2 ahora apunta a `../checkpoint_2/README.md`.

El cambio de directorio es necesario: los caminos de las configs se resuelven desde el directorio de trabajo del proceso. No arrancar desde la raíz del clon ni desde `escenarios/<nombre>/configs/`. No usar variables de shell dentro de las configs.

## Configuración por escenario

Los valores originales se conservan. Memoria, página, Offload y geometría de Storage están expresados en bytes, salvo cantidades de bloques y entradas.

| Escenario | Planificación / Cores / multiprogramación | RAM / página | Offload / reemplazo | Storage: bloques / tamaño bloque / bloques directorio |
| --- | --- | --- | --- | --- |
| 01_reserva_liberacion | FIFO / 1 / 1 | 256 / 64 | 4096 / LRU | 32 / 64 / 1 |
| 02_aritmetica | FIFO / 1 / 1 | 256 / 64 | 4096 / LRU | 32 / 64 / 1 |
| 03_cruce_pagina | FIFO / 1 / 1 | 256 / 64 | 4096 / LRU | 32 / 64 / 1 |
| 04_mini_lru | FIFO / 1 / 1 | 256 / 64 | 4096 / LRU | 32 / 64 / 1 |
| 05_mini_clock_m | FIFO / 1 / 1 | 256 / 64 | 4096 / CLOCK-M | 32 / 64 / 1 |
| 06_page_locking | RR / 2 / 3 | 256 / 64 | 4096 / LRU | 32 / 64 / 1 |
| 07_servicios_fifo | RR / 2 / 3 | 256 / 64 | 4096 / LRU | 32 / 64 / 1 |
| 08_sin_offload | FIFO / 1 / 1 | 256 / 64 | 256 / LRU | 32 / 64 / 1 |
| 09_aislamiento | RR / 2 / 3 | 256 / 64 | 4096 / CLOCK-M | 32 / 64 / 1 |
| 10_storage_directorio | Solo Storage | — | — | 32 / 64 / 1 |
| 11_storage_bloques | Solo Storage | — | — | 8 / 32 / 4 |

Quantum RR=2000 ms (FIFO lo ignora); memoria=500 ms, Offload=1000 ms, Loader=1500 ms; acceso a bloque y commit=100 ms. Se mantienen ESTIMACION_INICIAL=10000 y HRRN_ALFA=0.5. El escenario `07_servicios_fifo` usa RR para los Jobs y FIFO para las colas de servicios: se conserva esa distinción del origen.

Los escenarios 06, 07 y 09 admiten launcher y dos hijos con grado 3. El 08 usa grado 1 y Offload de cuatro páginas para provocar agotamiento y verificar recuperación. Los escenarios 10 y 11 ejecutan solo Storage, con volúmenes separados y geometrías distintas. No reutilizar una carpeta de volumen entre ellos.

## Dependencias y ejecución

Se requieren Linux y los binarios compilados de cada grupo. Reemplazar `/ruta/al/tp/bin/{storage,placa,planificador,core}` por sus ubicaciones reales. Las IP locales son `127.0.0.1` y los puertos compartidos son 8080/8081/8082; adaptar para varias máquinas y no ejecutar escenarios simultáneamente. LABEL requiere entrada manual en Planificador.

Los binarios de entrada están incluidos y no necesitan generarse. Se conserva la referencia externa al enunciado del origen; no se modifica su interpretación en esta migración. Ver [alcance](../checkpoints/checkpoint_3/docs/alcance.md) y [configuración](../checkpoints/checkpoint_3/docs/configuracion.md).

## Validación

Se compararon los 76 archivos con el origen: 41 configs idénticas salvo las rutas, 14 scripts idénticos salvo las extensiones de INIT_JOB y 5 binarios idénticos. Se verificaron los enlaces Markdown locales y sus anclajes, los Jobs iniciales, los 8 INIT_JOB, los 8 LOAD_BATCH y los destinos de salto JNZ.

Se ejecutaron los comandos de preparación de los 11 escenarios en una copia temporal y se comprobaron los argumentos de sus 41 comandos de arranque: configs existentes, directorio de trabajo, Job inicial, número de Core y carpetas de entrada/salida. Las 29 rutas de salida quedan cubiertas por el ignore global. Las configs conservan sus parámetros de planificación, capacidad, reemplazo, retardos, puertos y geometría. CP2 y el ejemplo no presentan diferencias respecto de la base productiva.

Esta validación comprueba la migración y las rutas de ejecución. La ejecución integrada y los resultados de memoria, servicios y Storage requieren los módulos de los grupos; no se declara una corrida real del TP.
