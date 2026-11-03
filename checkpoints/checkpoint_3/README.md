# Checkpoint 3 — Memoria, paginación, servicios y Storage

Preparación docente para el **31/10/2026**, basada en el [enunciado](https://docs.google.com/document/d/1UzKDYRO03_UXn1QLn9d7thgEToC9QdpQTtJlLdFoqrQ/edit). Incluye pseudocódigos ejecutables por el TP, datos binarios de entrada, configs completas y resultados esperados.

Leer primero [alcance y cobertura](docs/alcance.md) y [configuración manual](docs/configuracion.md). Estas pruebas están preparadas para ejecutar; no se presenta una corrida real del TP como ya realizada.

## Orden sugerido

| Escenario | Objetivo | Ejecución |
| --- | --- | --- |
| [01_reserva_liberacion](escenarios/01_reserva_liberacion/README.md) | Reserva, demanda y liberación | Job aislado |
| [02_aritmetica](escenarios/02_aritmetica/README.md) | FORWARD, BACKWARD y UPDATE | Job aislado |
| [03_cruce_pagina](escenarios/03_cruce_pagina/README.md) | Acceso uint32_t entre páginas y reintento | Job aislado |
| [04_mini_lru](escenarios/04_mini_lru/README.md) | Mini entrenamiento con LRU y Offload | Job aislado |
| [05_mini_clock_m](escenarios/05_mini_clock_m/README.md) | Mini entrenamiento con CLOCK-M y Offload | Job aislado |
| [06_page_locking](escenarios/06_page_locking/README.md) | Page locking durante LABEL y presión de memoria | Launcher |
| [07_servicios_fifo](escenarios/07_servicios_fifo/README.md) | Colas FIFO de Loader, Labeler y Logger | Launcher |
| [08_sin_offload](escenarios/08_sin_offload/README.md) | Offload lleno y recuperación de recursos | Launcher |
| [09_aislamiento](escenarios/09_aislamiento/README.md) | Reemplazo global y aislamiento entre Jobs | Launcher |
| [10_storage_directorio](escenarios/10_storage_directorio/README.md) | Formateo y comandos locales: directorio lleno | Consola local de Storage |
| [11_storage_bloques](escenarios/11_storage_bloques/README.md) | Comandos locales: bloques de datos agotados | Consola local de Storage |

## Archivos

- `scripts/`: Jobs y launchers de CP3. Una instrucción por línea; saltos con PC desde cero.
- `datos/`: [fixtures binarios y su contenido](datos/README.md), listos para LOAD_BATCH.
- `escenarios/<nombre>/`: pasos, resultados observables y configs de los módulos necesarios.
- `resultados/memoria.md`: [valores y hexadecimales esperados](resultados/memoria.md).
- `docs/`: alcance respecto de CP4, capacidad, rutas y retardos.
- `runtime/`: salidas locales excluidas de Git.

Desde la raíz del clon, ejecutar en todas las terminales:

```bash
cd checkpoints/checkpoint_3
```

Mantener ese directorio de trabajo al usar los comandos de cada escenario. Las pruebas con LABEL requieren entrada manual en la terminal del Planificador. No hace falta correr generadores ni validadores auxiliares.

Las pruebas previas de [checkpoint 2](../checkpoint_2/README.md#orden-de-pruebas) conservan sus archivos y rutas.
