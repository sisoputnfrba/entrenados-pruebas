# 08_sin_offload — Offload lleno y recuperación de recursos

Preparación docente para CP3. Consultar [alcance](../../docs/alcance.md) y [configuración](../../docs/configuracion.md).

| Parámetro | Valor |
| --- | --- |
| Planificación / Cores / cupo | FIFO / 1 / 1 |
| Memoria / página / marcos | 256 bytes / 64 bytes / 4 |
| Offload / reemplazo | 256 bytes / LRU |
| Quantum RR | 2000 ms (ignorado por FIFO) |
| Memoria / Offload / Loader | 500 / 1000 / 1500 ms |
| Job inicial | launcher_offload.asm |

Configs completas: [Planificador](configs/planificador.config), [Placa](configs/placa.config), [Storage](configs/storage.config), [Core 1](configs/core-1.config).

## Preparación y arranque

En todas las terminales, desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3` y mantener ese directorio de trabajo. Crear las carpetas de salida; los datos de entrada ya están versionados en `datos/`.

```bash
mkdir -p runtime/08_sin_offload/storage
```

Reemplazar `/ruta/al/tp` por la ubicación de los binarios. Arrancar en este orden, esperando que los servidores escuchen antes de iniciar sus clientes:

```bash
# Terminal 1
/ruta/al/tp/bin/storage "$PWD/escenarios/08_sin_offload/configs/storage.config"

# Terminal 2
/ruta/al/tp/bin/placa "$PWD/escenarios/08_sin_offload/configs/placa.config"

# Terminal 3
/ruta/al/tp/bin/planificador "$PWD/escenarios/08_sin_offload/configs/planificador.config" "launcher_offload.asm"

# Terminal 4
/ruta/al/tp/bin/core "$PWD/escenarios/08_sin_offload/configs/core-1.config" 1
```

El Job inicial y los parámetros INIT_JOB son relativos a PATH_INSTRUCCIONES. No anteponer `scripts/` al nombre del Job con esa config.

## Pasos y resultado esperado

La config tiene Offload de 256 bytes (cuatro páginas). Multiprogramación=1.

1. El launcher crea `sin_offload.asm` y `recuperacion_offload.asm`; termina sin bloquear esperando su admisión.
2. El primer hijo pide ALLOC de 256 bytes: reserva las cuatro páginas sin ocupar RAM.
3. ALLOC de 1 byte necesita otra página completa. Debe fallar por falta de respaldo y finalizar ese Job, aunque existan marcos físicos libres. No se alcanza su EXIT normal.
4. La finalización libera las cuatro reservas de la primera asignación y cualquier estructura parcial de la fallida.
5. El siguiente hijo debe ser admitido y poder reservar de nuevo los 256 bytes completos. LOAD_BATCH/REPORT producen `48 6F 6C 61`.
6. Ese hijo finaliza por EXIT sin FREE explícito: se libera todo igualmente. INFO/TLS deben volver al estado vacío.

Esta es una prueba de falta de recursos definida por el enunciado; no usa punteros inválidos, doble FREE ni errores sintácticos.

## Evidencia y repetición

Conservar logs separados por módulo/Core y el archivo `runtime/08_sin_offload/reportes.log`. Identificar los reportes por JID y por orden dentro de ese Job. Comparar solo los bytes pedidos; mayúsculas/minúsculas de hexadecimal y prefijos de log no alteran el resultado.

Detener todos los módulos antes de repetir o cambiar de escenario. Conservar las evidencias previas y usar un archivo de reportes nuevo para contar las emisiones de esta corrida. No truncar Offload ni borrar Storage mientras los módulos estén activos. No exigir tiempos de pared ni números de marco exactos.
