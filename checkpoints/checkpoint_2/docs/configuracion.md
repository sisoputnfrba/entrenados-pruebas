# Configuración de las pruebas

## Conectividad local

| Servidor | Puerto | Clientes |
| --- | ---: | --- |
| Planificador | 8080 | Core 1 / Core 2 |
| Placa | 8081 | Planificador y Cores |
| Storage | 8082 | Planificador |

IP de los clientes: 127.0.0.1. Estos valores son para una sola máquina. Para distribuir los procesos, reemplazar las IP por las de los servidores y usar rutas locales válidas en cada máquina. No ejecutar dos escenarios simultáneamente.

Los ejemplos independientes del enunciado repiten PUERTO_ESCUCHA=8080 en varios servidores. Esta batería corrige la colisión usando los puertos a los que ya apuntan sus clientes.

## Valores y restricciones

| Módulo | Campos | Criterio |
| --- | --- | --- |
| Planificador | ALGORITMO_PLANIFICACION | FIFO o RR según el escenario |
| Planificador | RR_QUANTUM | 2000 ms en escenarios 02–05; 20 ms en 01; FIFO lo ignora |
| Planificador | GRADO_MULTIPROGRAMACION | 1, 2 o 5; 5 permite el lanzador y cuatro hijos |
| Planificador | ESTIMACION_INICIAL=10000, HRRN_ALFA=0.5 | valores del ejemplo; no se ejecuta HRRN |
| Planificador | RETARDO_LOADER=500 | ms, servicio no utilizado |
| Placa | TAM_MEMORIA=4096, TAM_PAGINA=64 | potencias de 2; 64 marcos |
| Placa | TAM_OFFLOAD=65536 | múltiplo del tamaño de página |
| Placa | ALGORITMO_REEMPLAZO=CLOCK-M | valor permitido; no se accede a datos |
| Placa | RETARDO_MEMORIA, RETARDO_OFFLOAD=100 | memoria: 500 ms en 02–05 y 10 ms en 01; offload: 100 ms; el enunciado pide retardo para dar respuesta, pero no define un retardo de ciclo en Core |
| Storage | CANT_BLOQUES=1024, TAM_BLOQUE=64, BLOQUES_DIRECTORIO=64 | valores del ejemplo, bloque potencia de 2 y al menos 32 |
| Storage | RETARDO_ACCESO_BLOQUE=100, RETARDO_COMMIT=100 | ms; sin operaciones de persistencia en estas pruebas |
| Todos | LOG_LEVEL=INFO | compatible con los logs mínimos |

FAT: 1024 entradas de 4 bytes ocupan 4096 bytes = 64 bloques. Sumando 1 superbloque + 64 FAT + 64 directorio quedan 895 bloques de datos. No se agregan claves inventadas de formato, retardo de Core, logs o timeout.

## Rutas y preparación manual

Usar directamente las configs de cada escenario. Sus rutas relativas se resuelven desde la carpeta `checkpoints/checkpoint_2/`: todos los módulos deben arrancarse desde esa carpeta (no desde la raíz de `entrenados-pruebas`), aunque sus binarios estén en otro lugar.

Antes de arrancar, crear los directorios de datos y Storage indicados en el README del escenario. `offload.dat` y `reportes.log` son salidas de los módulos; no crearlos vacíos por adelantado. Conservar los datos y las evidencias existentes antes de repetir.

Los pseudocódigos están en `scripts/individuales/` y `scripts/launchers/`. `PATH_INSTRUCCIONES=./scripts/` es la raíz común: los launchers usan `INIT_JOB individuales/<archivo>.txt`, relativo a esa raíz, no a la carpeta del launcher. No modificar los saltos JNZ.

Los comandos de arranque pasan el Job inicial como ruta absoluta mediante `"$PWD/scripts/..."`. Placa debe aceptar esa ruta sin anteponer `PATH_INSTRUCCIONES`. El enunciado no detalla cómo normalizar paths; si la implementación solo acepta nombres relativos, pasar `launchers/fifo.txt`, `launchers/rr.txt` o `individuales/<archivo>.txt` como Job inicial, conservando la misma raíz de instrucciones.

### Si se arranca desde otra carpeta

Editar manualmente los siguientes campos con rutas absolutas del clon y del escenario elegido. Por ejemplo, para un clon en `/home/utnso/entrenados-pruebas/checkpoints/checkpoint_2` y el escenario `03_rr_un_core`:

| Módulo | Campo | Ruta absoluta de ejemplo |
| --- | --- | --- |
| Planificador | PATH_DATOS | `/home/utnso/entrenados-pruebas/checkpoints/checkpoint_2/runtime/03_rr_un_core/datos/` |
| Planificador | PATH_REPORTES | `/home/utnso/entrenados-pruebas/checkpoints/checkpoint_2/runtime/03_rr_un_core/reportes.log` |
| Placa | PATH_INSTRUCCIONES | `/home/utnso/entrenados-pruebas/checkpoints/checkpoint_2/scripts/` |
| Placa | PATH_OFFLOAD | `/home/utnso/entrenados-pruebas/checkpoints/checkpoint_2/runtime/03_rr_un_core/offload.dat` |
| Storage | PATH_STORAGE | `/home/utnso/entrenados-pruebas/checkpoints/checkpoint_2/runtime/03_rr_un_core/storage/` |

Escribir las rutas literales en las configs. Crear las carpetas de destino antes del arranque. En los comandos del README, reemplazar también `$PWD` por la ruta absoluta de `checkpoints/checkpoint_2/` si la terminal está en otra carpeta.

## Quantum y medición

2000 ms es el quantum inicial de las demostraciones RR, con RETARDO_MEMORIA=500 ms para observar las trazas. No garantiza una cantidad exacta de instrucciones por turno. La red y el retardo de Placa influyen en la duración; verificar que A/B se desalojen y retomen. Si no ocurre, ajustar el quantum después de medir. No añadir un retardo de Core no definido. Para la prueba sin desalojos, usar un quantum mayor que la duración completa observada de todos los Jobs.


## Tiempos de demostración

El escenario 01 conserva RETARDO_MEMORIA=10 ms para las pruebas aisladas. Los escenarios 02–05 usan 500 ms y RR_QUANTUM=2000 ms (ignorado por FIFO). Los demás retardos se mantienen: Loader 500 ms, Offload 100 ms y Storage 100 ms por acceso a bloque y por commit.

Con una respuesta de FETCH de 500 ms por instrucción, A (814 instrucciones) requiere aproximadamente 407 segundos de ejecución y B (614) unos 307 segundos, sin contar esperas, red ni cambios de contexto. Una ejecución completa de un Core ronda 12 minutos. Para una pasada rápida, editar manualmente RETARDO_MEMORIA a 10 ms y, en RR, RR_QUANTUM a 20 ms.
