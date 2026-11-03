# 05_mini_clock_m — Mini entrenamiento con CLOCK-M y Offload

Preparación docente para CP3. Consultar [alcance](../../docs/alcance.md) y [configuración](../../docs/configuracion.md).

| Parámetro | Valor |
| --- | --- |
| Planificación / Cores / cupo | FIFO / 1 / 1 |
| Memoria / página / marcos | 256 bytes / 64 bytes / 4 |
| Offload / reemplazo | 4096 bytes / CLOCK-M |
| Quantum RR | 2000 ms (ignorado por FIFO) |
| Memoria / Offload / Loader | 500 / 1000 / 1500 ms |
| Job inicial | mini_entrenamiento_a.asm |

Configs completas: [Planificador](configs/planificador.config), [Placa](configs/placa.config), [Storage](configs/storage.config), [Core 1](configs/core-1.config).

## Preparación y arranque

En todas las terminales, desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3` y mantener ese directorio de trabajo. Crear las carpetas de salida; los datos de entrada ya están versionados en `datos/`.

```bash
mkdir -p runtime/05_mini_clock_m/storage
```

Reemplazar `/ruta/al/tp` por la ubicación de los binarios. Arrancar en este orden, esperando que los servidores escuchen antes de iniciar sus clientes:

```bash
# Terminal 1
/ruta/al/tp/bin/storage "$PWD/escenarios/05_mini_clock_m/configs/storage.config"

# Terminal 2
/ruta/al/tp/bin/placa "$PWD/escenarios/05_mini_clock_m/configs/placa.config"

# Terminal 3
/ruta/al/tp/bin/planificador "$PWD/escenarios/05_mini_clock_m/configs/planificador.config" "mini_entrenamiento_a.asm"

# Terminal 4
/ruta/al/tp/bin/core "$PWD/escenarios/05_mini_clock_m/configs/core-1.config" 1
```

El Job inicial y los parámetros INIT_JOB son relativos a PATH_INSTRUCCIONES. No anteponer `scripts/` al nombre del Job con esa config.

## Pasos y resultado esperado

Ejecutar el mismo mini entrenamiento y usar los mismos datos y resultados que en [LRU](../04_mini_lru/README.md), cambiando únicamente el algoritmo de reemplazo a CLOCK-M.

Debe haber reemplazos, recuperación del contenido modificado, ocho reportes correctos y liberación completa. Ninguna víctima puede estar lockeada. Observar el comportamiento de los bits de uso/modificación y del puntero a partir de las estructuras o instrumentación del grupo.

No imponer una secuencia universal de víctimas: el enunciado no detalla la posición inicial del puntero ni todos los desempates de CLOCK-M. Para revisar su elección, registrar el estado de bits/puntero inmediatamente anterior y aplicar la variante de Clock Modificado que corresponda. Esa instrumentación adicional no es un formato de log obligatorio.

## Evidencia y repetición

Conservar logs separados por módulo/Core y el archivo `runtime/05_mini_clock_m/reportes.log`. Identificar los reportes por JID y por orden dentro de ese Job. Comparar solo los bytes pedidos; mayúsculas/minúsculas de hexadecimal y prefijos de log no alteran el resultado.

Detener todos los módulos antes de repetir o cambiar de escenario. Conservar las evidencias previas y usar un archivo de reportes nuevo para contar las emisiones de esta corrida. No truncar Offload ni borrar Storage mientras los módulos estén activos. No exigir tiempos de pared ni números de marco exactos.
