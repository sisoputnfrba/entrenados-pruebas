# 06_page_locking — Page locking durante LABEL y presión de memoria

Preparación docente para CP3. Consultar [alcance](../../docs/alcance.md) y [configuración](../../docs/configuracion.md).

| Parámetro | Valor |
| --- | --- |
| Planificación / Cores / cupo | RR / 2 / 3 |
| Memoria / página / marcos | 256 bytes / 64 bytes / 4 |
| Offload / reemplazo | 4096 bytes / LRU |
| Quantum RR | 2000 ms |
| Memoria / Offload / Loader | 500 / 1000 / 1500 ms |
| Job inicial | launcher_lock.asm |

Configs completas: [Planificador](configs/planificador.config), [Placa](configs/placa.config), [Storage](configs/storage.config), [Core 1](configs/core-1.config), [Core 2](configs/core-2.config).

## Preparación y arranque

En todas las terminales, desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3` y mantener ese directorio de trabajo. Crear las carpetas de salida; los datos de entrada ya están versionados en `datos/`.

```bash
mkdir -p runtime/06_page_locking/storage
```

Reemplazar `/ruta/al/tp` por la ubicación de los binarios. Arrancar en este orden, esperando que los servidores escuchen antes de iniciar sus clientes:

```bash
# Terminal 1
/ruta/al/tp/bin/storage "$PWD/escenarios/06_page_locking/configs/storage.config"

# Terminal 2
/ruta/al/tp/bin/placa "$PWD/escenarios/06_page_locking/configs/placa.config"

# Terminal 3
/ruta/al/tp/bin/planificador "$PWD/escenarios/06_page_locking/configs/planificador.config" "launcher_lock.asm"

# Terminal 4
/ruta/al/tp/bin/core "$PWD/escenarios/06_page_locking/configs/core-1.config" 1

# Terminal 5
/ruta/al/tp/bin/core "$PWD/escenarios/06_page_locking/configs/core-2.config" 2
```

El Job inicial y los parámetros INIT_JOB son relativos a PATH_INSTRUCCIONES. No anteponer `scripts/` al nombre del Job con esa config.

## Pasos y resultado esperado

1. El launcher crea un Job de LABEL y otro de mini entrenamiento. Identificarlos por el nombre de archivo en sus logs; no fijar JID concretos.
2. Cuando Labeler pida cuatro caracteres para `label_lock.asm`, mantener la entrada pendiente hasta observar reemplazos del otro Job.
3. LABEL apunta a base+62 y ocupa dos páginas. Sus marcos deben seguir lockeados mientras el usuario no responda; el Job está BLOCKED y el Core queda disponible.
4. El otro Job debe avanzar con los dos marcos restantes, generando presión y reemplazos entre páginas no lockeadas. Ninguna de las dos páginas de LABEL puede ser víctima.
5. Responder `ABCD` y Enter. Los cuatro bytes de datos son `41 42 43 44`; Enter confirma la entrada y no forma parte del tamaño solicitado.
6. Al finalizar el servicio, Planificador solicita deslockeo antes de READY. REPORT de ese Job muestra `41 42 43 44`. Los ocho reportes del otro Job coinciden con los resultados del mini entrenamiento.

Durante la espera, INFO de Placa debe permitir observar al menos los dos frames retenidos por LABEL; otros locks pueden ser transitorios. Si el Job de presión ya terminó antes de que aparezca la solicitud, repetir iniciando Core 2 cuando Labeler esté esperando. Conservar la entrada pendiente solo el tiempo necesario para observar progreso y reemplazos.

## Evidencia y repetición

Conservar logs separados por módulo/Core y el archivo `runtime/06_page_locking/reportes.log`. Identificar los reportes por JID y por orden dentro de ese Job. Comparar solo los bytes pedidos; mayúsculas/minúsculas de hexadecimal y prefijos de log no alteran el resultado.

Detener todos los módulos antes de repetir o cambiar de escenario. Conservar las evidencias previas y usar un archivo de reportes nuevo para contar las emisiones de esta corrida. No truncar Offload ni borrar Storage mientras los módulos estén activos. No exigir tiempos de pared ni números de marco exactos.
