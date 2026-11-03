# 07_servicios_fifo — Colas FIFO de Loader, Labeler y Logger

Preparación docente para CP3. Consultar [alcance](../../docs/alcance.md) y [configuración](../../docs/configuracion.md).

| Parámetro | Valor |
| --- | --- |
| Planificación / Cores / cupo | RR / 2 / 3 |
| Memoria / página / marcos | 256 bytes / 64 bytes / 4 |
| Offload / reemplazo | 4096 bytes / LRU |
| Quantum RR | 2000 ms |
| Memoria / Offload / Loader | 500 / 1000 / 1500 ms |
| Job inicial | launcher_servicios.asm |

Configs completas: [Planificador](configs/planificador.config), [Placa](configs/placa.config), [Storage](configs/storage.config), [Core 1](configs/core-1.config), [Core 2](configs/core-2.config).

## Preparación y arranque

En todas las terminales, desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3` y mantener ese directorio de trabajo. Crear las carpetas de salida; los datos de entrada ya están versionados en `datos/`.

```bash
mkdir -p runtime/07_servicios_fifo/storage
```

Reemplazar `/ruta/al/tp` por la ubicación de los binarios. Arrancar en este orden, esperando que los servidores escuchen antes de iniciar sus clientes:

```bash
# Terminal 1
/ruta/al/tp/bin/storage "$PWD/escenarios/07_servicios_fifo/configs/storage.config"

# Terminal 2
/ruta/al/tp/bin/placa "$PWD/escenarios/07_servicios_fifo/configs/placa.config"

# Terminal 3
/ruta/al/tp/bin/planificador "$PWD/escenarios/07_servicios_fifo/configs/planificador.config" "launcher_servicios.asm"

# Terminal 4
/ruta/al/tp/bin/core "$PWD/escenarios/07_servicios_fifo/configs/core-1.config" 1

# Terminal 5
/ruta/al/tp/bin/core "$PWD/escenarios/07_servicios_fifo/configs/core-2.config" 2
```

El Job inicial y los parámetros INIT_JOB son relativos a PATH_INSTRUCCIONES. No anteponer `scripts/` al nombre del Job con esa config.

## Pasos y resultado esperado

El launcher crea dos Jobs. Cada uno hace LOAD_BATCH, REPORT, LABEL y REPORT sobre un rango de cuatro bytes que cruza una página.

| Job identificado por script | Primer REPORT | Entrada a LABEL | Segundo REPORT |
| --- | --- | --- | --- |
| servicios_a.asm | 48 6F 6C 61 (Hola) | ABCD | 41 42 43 44 |
| servicios_b.asm | 54 45 53 54 (TEST) | WXYZ | 57 58 59 5A |

Responder según el JID de la solicitud, aunque el orden de llegada cambie. Mantener el primer LABEL pendiente hasta ver al otro Job avanzar y solicitar su LABEL.

Cada servicio atiende una única petición a la vez y respeta su propia cola FIFO en el orden real de recepción. La espera de Labeler no debe frenar Loader ni Logger, ni mantener ocupado un Core. No imponer un orden fijo entre distintos servicios o entre los JID.

Correlacionar inicio/fin de servicios y estados EXEC → BLOCKED → READY. Los Page Faults previos también bloquean, pero todavía no deben encolar la syscall: se reintenta la misma instrucción después de cargar la página. Cada syscall de servicio se completa una sola vez. Los fragmentos traducidos abarcan 2+2 bytes y permanecen lockeados hasta completar la operación y deslockear antes de READY.

Se esperan dos reportes por Job, en ese orden por JID. No exigir una separación de 1500 ms exacta en los logs; ese retardo configura Loader y se agregan acceso a memoria y comunicación.

## Evidencia y repetición

Conservar logs separados por módulo/Core y el archivo `runtime/07_servicios_fifo/reportes.log`. Identificar los reportes por JID y por orden dentro de ese Job. Comparar solo los bytes pedidos; mayúsculas/minúsculas de hexadecimal y prefijos de log no alteran el resultado.

Detener todos los módulos antes de repetir o cambiar de escenario. Conservar las evidencias previas y usar un archivo de reportes nuevo para contar las emisiones de esta corrida. No truncar Offload ni borrar Storage mientras los módulos estén activos. No exigir tiempos de pared ni números de marco exactos.
