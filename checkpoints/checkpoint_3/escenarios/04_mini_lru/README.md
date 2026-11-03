# 04_mini_lru — Mini entrenamiento con LRU y Offload

Preparación docente para CP3. Consultar [alcance](../../docs/alcance.md) y [configuración](../../docs/configuracion.md).

| Parámetro | Valor |
| --- | --- |
| Planificación / Cores / cupo | FIFO / 1 / 1 |
| Memoria / página / marcos | 256 bytes / 64 bytes / 4 |
| Offload / reemplazo | 4096 bytes / LRU |
| Quantum RR | 2000 ms (ignorado por FIFO) |
| Memoria / Offload / Loader | 500 / 1000 / 1500 ms |
| Job inicial | mini_entrenamiento_a.asm |

Configs completas: [Planificador](configs/planificador.config), [Placa](configs/placa.config), [Storage](configs/storage.config), [Core 1](configs/core-1.config).

## Preparación y arranque

En todas las terminales, desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3` y mantener ese directorio de trabajo. Crear las carpetas de salida; los datos de entrada ya están versionados en `datos/`.

```bash
mkdir -p runtime/04_mini_lru/storage
```

Reemplazar `/ruta/al/tp` por la ubicación de los binarios. Arrancar en este orden, esperando que los servidores escuchen antes de iniciar sus clientes:

```bash
# Terminal 1
/ruta/al/tp/bin/storage "$PWD/escenarios/04_mini_lru/configs/storage.config"

# Terminal 2
/ruta/al/tp/bin/placa "$PWD/escenarios/04_mini_lru/configs/placa.config"

# Terminal 3
/ruta/al/tp/bin/planificador "$PWD/escenarios/04_mini_lru/configs/planificador.config" "mini_entrenamiento_a.asm"

# Terminal 4
/ruta/al/tp/bin/core "$PWD/escenarios/04_mini_lru/configs/core-1.config" 1
```

El Job inicial y los parámetros INIT_JOB son relativos a PATH_INSTRUCCIONES. No anteponer `scripts/` al nombre del Job con esa config.

## Pasos y resultado esperado

Ejecutar el mini entrenamiento de ocho páginas con cuatro marcos. En cada página, LOAD_BATCH inicializa 20 bytes y se ejecuta FORWARD → BACKWARD → UPDATE. Luego se recorren otra vez las ocho páginas con REPORT.

- ALLOC reserva 512 bytes de respaldo, sin cargar los ocho marcos por adelantado.
- El conjunto de ocho páginas supera los cuatro marcos: se requieren reemplazos y escrituras/lecturas de Offload. Las páginas modificadas deben conservar sus bytes al regresar.
- Los accesos de cada operación se limitan a una página. No se solicita lock simultáneo de las ocho.
- Con un Job, sin accesos adicionales a datos, al cargar la página relativa 4 las candidatas 0–3 están desbloqueadas y la menos recientemente usada es 0. Las siguientes cargas secuenciales desplazan 1, 2 y 3. No fijar números de marco físico.
- Cada uno de los ocho REPORT debe emitir el mismo bloque de veinte bytes indicado en [resultados](../../resultados/memoria.md). AX termina en 0 y BX en 0.
- FREE libera las ocho reservas, incluidas las páginas que quedaron solo en Offload. EXIT libera las estructuras restantes.

El recorrido de reportes verifica el contenido después del reemplazo. No considerar suficiente observar únicamente el log de una víctima.

## Evidencia y repetición

Conservar logs separados por módulo/Core y el archivo `runtime/04_mini_lru/reportes.log`. Identificar los reportes por JID y por orden dentro de ese Job. Comparar solo los bytes pedidos; mayúsculas/minúsculas de hexadecimal y prefijos de log no alteran el resultado.

Detener todos los módulos antes de repetir o cambiar de escenario. Conservar las evidencias previas y usar un archivo de reportes nuevo para contar las emisiones de esta corrida. No truncar Offload ni borrar Storage mientras los módulos estén activos. No exigir tiempos de pared ni números de marco exactos.
