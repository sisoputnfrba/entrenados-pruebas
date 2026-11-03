# 03_cruce_pagina — Acceso uint32_t entre páginas y reintento

Preparación docente para CP3. Consultar [alcance](../../docs/alcance.md) y [configuración](../../docs/configuracion.md).

| Parámetro | Valor |
| --- | --- |
| Planificación / Cores / cupo | FIFO / 1 / 1 |
| Memoria / página / marcos | 256 bytes / 64 bytes / 4 |
| Offload / reemplazo | 4096 bytes / LRU |
| Quantum RR | 2000 ms (ignorado por FIFO) |
| Memoria / Offload / Loader | 500 / 1000 / 1500 ms |
| Job inicial | cruce_pagina.asm |

Configs completas: [Planificador](configs/planificador.config), [Placa](configs/placa.config), [Storage](configs/storage.config), [Core 1](configs/core-1.config).

## Preparación y arranque

En todas las terminales, desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3` y mantener ese directorio de trabajo. Crear las carpetas de salida; los datos de entrada ya están versionados en `datos/`.

```bash
mkdir -p runtime/03_cruce_pagina/storage
```

Reemplazar `/ruta/al/tp` por la ubicación de los binarios. Arrancar en este orden, esperando que los servidores escuchen antes de iniciar sus clientes:

```bash
# Terminal 1
/ruta/al/tp/bin/storage "$PWD/escenarios/03_cruce_pagina/configs/storage.config"

# Terminal 2
/ruta/al/tp/bin/placa "$PWD/escenarios/03_cruce_pagina/configs/placa.config"

# Terminal 3
/ruta/al/tp/bin/planificador "$PWD/escenarios/03_cruce_pagina/configs/planificador.config" "cruce_pagina.asm"

# Terminal 4
/ruta/al/tp/bin/core "$PWD/escenarios/03_cruce_pagina/configs/core-1.config" 1
```

El Job inicial y los parámetros INIT_JOB son relativos a PATH_INSTRUCCIONES. No anteponer `scripts/` al nombre del Job con esa config.

## Pasos y resultado esperado

1. ALLOC reserva 128 bytes (dos páginas de 64). LOAD_BATCH escribe 2 y 3 en los primeros ocho bytes.
2. P2 apunta a base+62. El destino uint32_t de FORWARD ocupa dos bytes de la primera página y dos de la segunda.
3. Antes del FORWARD, solo la primera página fue accedida. Al traducir el destino debe producirse un Page Fault de la segunda página.
4. La instrucción no debe cambiar AX, E1/E2/E3 ni ningún byte antes de completar todas las traducciones. El PC permanece en 8 durante el Page Fault y se reintenta FORWARD.
5. Tras resolverlo, AX=6 y el destino contiene `06 00 00 00`. REPORT atraviesa el mismo límite y debe emitir esos cuatro bytes exactamente una vez.

La MMU debe producir dos fragmentos (2 bytes + 2 bytes) para el destino, usando los marcos obtenidos; no asumir marcos físicamente contiguos. Para una base lógica b, las páginas son b/64 y b/64+1. No debe quedar lock retenido después de devolver el contexto por Page Fault, finalizar FORWARD o finalizar REPORT. Los cuatro frames de la config permiten resolver el conjunto de páginas sin forzar un bloqueo por falta de víctimas.

## Evidencia y repetición

Conservar logs separados por módulo/Core y el archivo `runtime/03_cruce_pagina/reportes.log`. Identificar los reportes por JID y por orden dentro de ese Job. Comparar solo los bytes pedidos; mayúsculas/minúsculas de hexadecimal y prefijos de log no alteran el resultado.

Detener todos los módulos antes de repetir o cambiar de escenario. Conservar las evidencias previas y usar un archivo de reportes nuevo para contar las emisiones de esta corrida. No truncar Offload ni borrar Storage mientras los módulos estén activos. No exigir tiempos de pared ni números de marco exactos.
