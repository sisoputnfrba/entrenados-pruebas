# EntrenadOS — Pruebas del checkpoint 2 (10/10/2026)

Pseudocódigos, configuraciones completas y resultados esperados para instrucciones básicas, FIFO, RR y cambios de contexto. Sin memoria de datos, MMU, servicios ni persistencia.

## Orden de pruebas

| Escenario | Algoritmo | Cores | Cupo | Job inicial |
| --- | --- | ---: | ---: | --- |
| [01 · Instrucciones aisladas](escenarios/01_instrucciones/README.md) | FIFO | 1 | 1 | cada archivo individual |
| [02 · FIFO](escenarios/02_fifo_un_core/README.md) | FIFO | 1 | 5 | fifo.asm |
| [03 · RR](escenarios/03_rr_un_core/README.md) | RR | 1 | 5 | rr.asm |
| [04 · RR concurrente](escenarios/04_rr_dos_cores/README.md) | RR | 2 | 5 | rr.asm |
| [05 · Multiprogramación 1](escenarios/05_multiprogramacion/grado_1/README.md) | RR | 1 | 1 | rr.asm |
| [05 · Multiprogramación 2](escenarios/05_multiprogramacion/grado_2/README.md) | RR | 1 | 2 | rr.asm |

Las pruebas 01 se ejecutan aisladas, sin launcher. Los demás escenarios usan INIT_JOB para crear varios Jobs. Los scripts terminan con EXIT.

## Uso

Requiere Linux y los cuatro módulos del grupo compilados. Este repositorio contiene pruebas, no las implementaciones.

Desde la raíz de `entrenados-pruebas`, ejecutar `cd checkpoints/checkpoint_2` en cada terminal. Ese será el directorio de trabajo de todos los módulos; `$PWD` en los comandos debe apuntar allí.

Seguir el README del escenario: crear sus directorios de salida y arrancar los modulos en orden, esperando la escucha de los servidores. Usar directamente las configs de `escenarios/`. La [guía de configuración](docs/configuracion.md) explica cómo ajustar las rutas si se usa otro directorio de trabajo.

## Estructura

- `scripts/individuales/`: nueve Jobs reutilizables.
- `scripts/launchers/`: launchers FIFO y RR, con rutas relativas a scripts/.
- `escenarios/`: guía y configs completas para cada prueba; Core 2 solo donde corresponde.
- `resultados/`: [registros y FETCH](resultados/individuales.md), [FIFO](resultados/fifo.md), [RR](resultados/rr.md).
- `docs/configuracion.md`: [campos, puertos, restricciones y resolución de paths](docs/configuracion.md).
- `runtime/`: salidas locales, excluidas de Git.

## Tiempos

Pruebas aisladas: retardo de Placa de 10 ms. Demostraciones FIFO/RR y multiprogramación: 500 ms; quantum RR de 2000 ms. Una corrida completa con un Core puede durar unos 12 minutos. Ver [tiempos y alternativa rápida](docs/configuracion.md#tiempos-de-demostración).

## Integración en el repositorio de pruebas

Cada etapa conserva sus propios scripts, escenarios, resultados y salidas `runtime/`. Para las próximas etapas se usará `checkpoints/checkpoint_3/` y, para la integración final, `entregas_finales/`, sin cambiar las rutas de CP2. Ver el [mapa de migración y rutas externas](../../docs/migracion-checkpoint-2.md).
