# Configuración y ejecución manual

Las configs están completas en cada escenario. Desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3` en cada terminal. Ejecutar los módulos desde esa carpeta; los caminos relativos se resuelven desde allí, no desde la carpeta de la config.

| Componente | Puerto servidor | Clientes |
| --- | ---: | --- |
| Planificador | 8080 | Cores |
| Placa | 8081 | Planificador y Cores |
| Storage | 8082 | Planificador |

IPs locales: 127.0.0.1. No ejecutar dos escenarios simultáneamente. Para varias máquinas, adaptar IPs y rutas de cada servidor manualmente.

## Rutas

| Campo | Valor |
| --- | --- |
| PATH_INSTRUCCIONES | ./scripts/ |
| PATH_DATOS | ./datos/ |
| PATH_OFFLOAD | ./runtime/<escenario>/offload.dat |
| PATH_REPORTES | ./runtime/<escenario>/reportes.log |
| PATH_STORAGE | ./runtime/<escenario>/storage/ |

El Job inicial es un nombre relativo a PATH_INSTRUCCIONES, por ejemplo `mini_entrenamiento_a.asm`. INIT_JOB usa la misma raíz. LOAD_BATCH usa nombres relativos a PATH_DATOS.

Si se inicia desde otra carpeta, escribir rutas absolutas literales en los cinco campos. No usar $PWD, $HOME ni ~ dentro de las configs: su expansión no está garantizada por el parser. Los comandos de arranque sí pueden usar "$PWD" cuando la terminal está en `checkpoints/checkpoint_3/`.

Crear el directorio storage indicado; su carpeta padre contiene las demás salidas. No crear archivos offload.dat ni reportes.log vacíos por adelantado. Los fixtures de entrada están incluidos en el repo.

## Tiempos

| Parámetro | Milisegundos | Uso |
| --- | ---: | --- |
| RETARDO_MEMORIA | 500 | respuesta tras acceso a página |
| RETARDO_OFFLOAD | 1000 | cada lectura/escritura de respaldo |
| RETARDO_LOADER | 1500 | atención de LOAD_BATCH |
| RR_QUANTUM | 2000 | escenarios RR; FIFO lo ignora |
| RETARDO_ACCESO_BLOQUE | 100 | acceso a bloques de Storage |
| RETARDO_COMMIT | 100 | campo completo; journaling no se exige en CP3 |

RETARDO_MEMORIA está definido para acceder a una página; no se supone que cada FETCH de instrucciones tarde 500 ms. El tiempo de una instrucción de entrenamiento puede incluir varias traducciones, lecturas, escrituras y fallos; quantum no equivale a una cantidad fija de instrucciones. Los escenarios duran lo que requieran esas operaciones y la entrada manual de LABEL.

## Capacidad y campos

Memoria de 256 bytes / página de 64 = cuatro marcos. Todos son potencias de dos. Offload normal de 4096 bytes = 64 páginas; en 08 se limita a 256 = cuatro páginas para forzar el error. Las asignaciones comienzan en límite de página y reservan ceil(tamaño/64) páginas de respaldo.

En el mini entrenamiento hay ocho páginas virtuales, pero cada instrucción toca una única página. En la prueba de locking, LABEL retiene dos marcos y el Job de presión puede avanzar con los otros dos. En servicios, cada Job puede retener dos marcos: cuatro en total. No reducir arbitrariamente TAM_MEMORIA sin analizar estos conjuntos.

GRADO_MULTIPROGRAMACION incluye Jobs admitidos bloqueados, además de READY/EXEC; un bloqueo no libera el cupo. Las configs concurrentes usan 3 para admitir launcher y dos hijos. La prueba 08 usa 1 para demostrar liberación antes de admitir el hijo siguiente.

Se mantienen ESTIMACION_INICIAL=10000 y HRRN_ALFA=0.5, ALGORITMO_PLANIFICACION FIFO/RR y ALGORITMO_REEMPLAZO LRU/CLOCK-M. No se agregan claves no definidas para retardos de Core, Labeler o Logger.

Storage usa dos geometrías pequeñas, documentadas en 10 y 11. No reutilizar el mismo volumen con distinta geometría. Para una primera corrida limpia, apuntar a una carpeta nueva; para la prueba de persistencia normal, mantener la misma.

## Evidencia y reinicio

Los reportes van a un archivo distinto del log de Planificador. Preservar logs por Core y por corrida, y asociar cada JID con el script que creó. Al repetir, conservar los reportes previos y utilizar un nuevo PATH_REPORTES para no confundir append con duplicación de operaciones.

No borrar salidas de CP2. Las salidas de CP3 se ubican bajo runtime/, ya excluido de Git. No se necesita ejecutar scripts auxiliares de preparación.
