# 01_reserva_liberacion — Reserva, demanda y liberación

Preparación docente para CP3. Consultar [alcance](../../docs/alcance.md) y [configuración](../../docs/configuracion.md).

| Parámetro | Valor |
| --- | --- |
| Planificación / Cores / cupo | FIFO / 1 / 1 |
| Memoria / página / marcos | 256 bytes / 64 bytes / 4 |
| Offload / reemplazo | 4096 bytes / LRU |
| Quantum RR | 2000 ms (ignorado por FIFO) |
| Memoria / Offload / Loader | 500 / 1000 / 1500 ms |
| Job inicial | reserva_liberacion.asm |

Configs completas: [Planificador](configs/planificador.config), [Placa](configs/placa.config), [Storage](configs/storage.config), [Core 1](configs/core-1.config).

## Preparación y arranque

En todas las terminales, desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3` y mantener ese directorio de trabajo. Crear las carpetas de salida; los datos de entrada ya están versionados en `datos/`.

```bash
mkdir -p runtime/01_reserva_liberacion/storage
```

Reemplazar `/ruta/al/tp` por la ubicación de los binarios. Arrancar en este orden, esperando que los servidores escuchen antes de iniciar sus clientes:

```bash
# Terminal 1
/ruta/al/tp/bin/storage "$PWD/escenarios/01_reserva_liberacion/configs/storage.config"

# Terminal 2
/ruta/al/tp/bin/placa "$PWD/escenarios/01_reserva_liberacion/configs/placa.config"

# Terminal 3
/ruta/al/tp/bin/planificador "$PWD/escenarios/01_reserva_liberacion/configs/planificador.config" "reserva_liberacion.asm"

# Terminal 4
/ruta/al/tp/bin/core "$PWD/escenarios/01_reserva_liberacion/configs/core-1.config" 1
```

El Job inicial y los parámetros INIT_JOB son relativos a PATH_INSTRUCCIONES. No anteponer `scripts/` al nombre del Job con esa config.

## Pasos y resultado esperado

1. Ejecutar el Job aislado. ALLOC de 65 bytes reserva dos páginas; ALLOC de 64, una. Todas las bases devueltas deben ser múltiplos de 64; no exigir una base concreta ni contigüidad entre asignaciones.
2. Las reservas no cargan páginas en RAM. Antes de LOAD_BATCH no debe aparecer una subida a memoria por esas reservas. FREE P0 libera el respaldo de dos páginas sin generar Page Fault.
3. La nueva reserva de 65 bytes debe tener respaldo disponible. No exigir que reutilice la misma dirección lógica.
4. LOAD_BATCH accede a los primeros cuatro bytes de P2: la página ausente produce Page Fault sin avanzar el PC; tras cargarla se reintenta la instrucción y luego se bloquea por Loader.
5. REPORT produce exactamente una vez `48 6F 6C 61`. FREE P1 y FREE P2 liberan respaldo y marcos; no producen un bloqueo de planificación.
6. Al finalizar, INFO/TLS de Placa deben volver a su estado vacío: ningún Job, ningún frame ocupado/lockeado y ninguna reserva de Offload.

No se exige que la memoria recién reservada esté inicializada en cero. El script solo observa bytes previamente cargados. El pico de respaldo es de tres páginas (192 bytes), mientras que el máximo de bytes pedidos simultáneamente es 129; no confundir ambas magnitudes en estadísticas.

## Evidencia y repetición

Conservar logs separados por módulo/Core y el archivo `runtime/01_reserva_liberacion/reportes.log`. Identificar los reportes por JID y por orden dentro de ese Job. Comparar solo los bytes pedidos; mayúsculas/minúsculas de hexadecimal y prefijos de log no alteran el resultado.

Detener todos los módulos antes de repetir o cambiar de escenario. Conservar las evidencias previas y usar un archivo de reportes nuevo para contar las emisiones de esta corrida. No truncar Offload ni borrar Storage mientras los módulos estén activos. No exigir tiempos de pared ni números de marco exactos.
