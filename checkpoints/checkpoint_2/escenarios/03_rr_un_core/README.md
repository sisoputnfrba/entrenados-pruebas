# 03_rr_un_core

## Objetivo y configuración

A y B se desalojan por quantum y retoman sin perder PC ni registros. La selección respeta el orden real de READY.

| Parámetro | Valor |
| --- | --- |
| Algoritmo | RR |
| Cores | 1 |
| Multiprogramación | 5 |
| Quantum RR | 2000 ms |
| Retardo de respuesta de Placa | 500 ms |
| Job inicial por defecto | `launchers/rr.asm` |

Configs completas: [Planificador](configs/planificador.config), [Placa](configs/placa.config), [Storage](configs/storage.config), [Core 1](configs/core-1.config).

## Preparar

En cada terminal, partir de la raíz del clon `entrenados-pruebas` y entrar a CP2. Crear las carpetas de salida una sola vez antes del arranque:

```bash
# Desde la raíz de entrenados-pruebas, en cada terminal:
cd checkpoints/checkpoint_2
mkdir -p runtime/03_rr_un_core/datos runtime/03_rr_un_core/storage
```

Usar directamente las configs de `escenarios/03_rr_un_core/configs/`. Mantener todas las terminales en `checkpoints/checkpoint_2/` para que las rutas relativas funcionen. Si ya se está en esa carpeta, no repetir el comando `cd`. Para usar otra carpeta de trabajo, ajustar manualmente los paths según la [guía de configuración](../../docs/configuracion.md).

## Arrancar

Abrir 4 terminales. En cada una, ubicarse en la carpeta `checkpoints/checkpoint_2/` del repositorio de pruebas. Sustituir `/ruta/al/tp` por la carpeta que contiene los binarios compilados del grupo. Ejecutar en orden y esperar a que cada servidor informe que está escuchando antes de iniciar el siguiente módulo:

```bash
# Terminal 1
/ruta/al/tp/bin/storage "$PWD/escenarios/03_rr_un_core/configs/storage.config"

# Terminal 2
/ruta/al/tp/bin/placa "$PWD/escenarios/03_rr_un_core/configs/placa.config"

# Terminal 3
/ruta/al/tp/bin/planificador "$PWD/escenarios/03_rr_un_core/configs/planificador.config" "$PWD/scripts/launchers/rr.asm"

# Terminal 4
/ruta/al/tp/bin/core "$PWD/escenarios/03_rr_un_core/configs/core-1.config" 1
```

Los nombres y ubicaciones de los binarios corresponden a los ejemplos del enunciado; adaptar solo el path si el grupo compila en otra ubicación.

## Evidencia y aprobación

A y B se desalojan por quantum y retoman sin perder PC ni registros. La selección respeta el orden real de READY.

Planificador: creación NEW, admisión READY, dispatch EXEC y finalización EXIT. En RR observar el motivo «Desalojado por fin de quantum» y EXEC → READY → EXEC. Core: FETCH con JID/PC e instrucción ejecutada. Inspeccionar el contexto devuelto para validar todos los registros; el formato adicional de esa impresión no es obligatorio.

Comparar con [resultados individuales](../../resultados/individuales.md) y [criterios RR](../../resultados/rr.md). Todos terminan una sola vez por EXIT; no hay accesos a memoria de datos, page faults ni BLOCKED. No evaluar HRRN.

## Errores típicos

- «Connection refused»: comprobar orden de inicio y puertos 8080/8081/8082.
- Archivo no encontrado: comprobar PATH_INSTRUCCIONES y la resolución de INIT_JOB indicada en la [guía de configuración](../../docs/configuracion.md).
- RR sin desalojos: medir duración real y ajustar quantum según la guía; no imponer una cantidad fija de instrucciones por turno.
- Registros o PC distintos: comparar la traza por JID, no el orden global de logs.

## Finalizar y repetir

Detener todos los procesos antes de otro escenario: los puertos son compartidos entre escenarios. Conservar reportes, Storage y Offload existentes antes de repetir. Para conservar evidencias, copiar los logs producidos por cada módulo antes de repetir. Storage solo necesita conectividad en CP2: no usar su consola ni exigir formateo/persistencia para aprobar estas pruebas.
