# 09_aislamiento — Reemplazo global y aislamiento entre Jobs

Preparación docente para CP3. Consultar [alcance](../../docs/alcance.md) y [configuración](../../docs/configuracion.md).

| Parámetro | Valor |
| --- | --- |
| Planificación / Cores / cupo | RR / 2 / 3 |
| Memoria / página / marcos | 256 bytes / 64 bytes / 4 |
| Offload / reemplazo | 4096 bytes / CLOCK-M |
| Quantum RR | 2000 ms |
| Memoria / Offload / Loader | 500 / 1000 / 1500 ms |
| Job inicial | launcher_aislamiento.asm |

Configs completas: [Planificador](configs/planificador.config), [Placa](configs/placa.config), [Storage](configs/storage.config), [Core 1](configs/core-1.config), [Core 2](configs/core-2.config).

## Preparación y arranque

En todas las terminales, desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3` y mantener ese directorio de trabajo. Crear las carpetas de salida; los datos de entrada ya están versionados en `datos/`.

```bash
mkdir -p runtime/09_aislamiento/storage
```

Reemplazar `/ruta/al/tp` por la ubicación de los binarios. Arrancar en este orden, esperando que los servidores escuchen antes de iniciar sus clientes:

```bash
# Terminal 1
/ruta/al/tp/bin/storage "$PWD/escenarios/09_aislamiento/configs/storage.config"

# Terminal 2
/ruta/al/tp/bin/placa "$PWD/escenarios/09_aislamiento/configs/placa.config"

# Terminal 3
/ruta/al/tp/bin/planificador "$PWD/escenarios/09_aislamiento/configs/planificador.config" "launcher_aislamiento.asm"

# Terminal 4
/ruta/al/tp/bin/core "$PWD/escenarios/09_aislamiento/configs/core-1.config" 1

# Terminal 5
/ruta/al/tp/bin/core "$PWD/escenarios/09_aislamiento/configs/core-2.config" 2
```

El Job inicial y los parámetros INIT_JOB son relativos a PATH_INSTRUCCIONES. No anteponer `scripts/` al nombre del Job con esa config.

## Pasos y resultado esperado

El launcher crea dos mini entrenamientos de ocho páginas cada uno, con dos Cores, cuatro marcos físicos globales y Offload suficiente para las dieciséis páginas (1024 bytes).

- Ambos Jobs deben avanzar, bloquear por Page Fault/Loader/Logger y conservar sus registros al ser replanificados.
- La selección de víctimas es global: no asignar una cuota fija de marcos por Job. Una víctima puede pertenecer al otro JID si no está lockeada.
- Los mismos números de página lógica en distintos Jobs no representan la misma memoria.
- El Job A debe producir ocho reportes del patrón A; el Job B, ocho del patrón B descritos en [resultados](../../resultados/memoria.md). No mezclar contenidos ni contextos.
- Offload serializa sus lecturas/escrituras incluso con fallos de ambos Cores. Revisar intervalos o instrumentación del grupo si los logs mínimos no exponen principio y fin; el orden global de logs por sí solo no demuestra ausencia de concurrencia.
- No exigir un Core concreto, un orden total de EXIT ni una víctima concreta en CLOCK-M.
- Al terminar ambos, no quedan marcos, locks ni reservas de respaldo.

## Evidencia y repetición

Conservar logs separados por módulo/Core y el archivo `runtime/09_aislamiento/reportes.log`. Identificar los reportes por JID y por orden dentro de ese Job. Comparar solo los bytes pedidos; mayúsculas/minúsculas de hexadecimal y prefijos de log no alteran el resultado.

Detener todos los módulos antes de repetir o cambiar de escenario. Conservar las evidencias previas y usar un archivo de reportes nuevo para contar las emisiones de esta corrida. No truncar Offload ni borrar Storage mientras los módulos estén activos. No exigir tiempos de pared ni números de marco exactos.
