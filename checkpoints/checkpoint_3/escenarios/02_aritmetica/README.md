# 02_aritmetica — FORWARD, BACKWARD y UPDATE

Preparación docente para CP3. Consultar [alcance](../../docs/alcance.md) y [configuración](../../docs/configuracion.md).

| Parámetro | Valor |
| --- | --- |
| Planificación / Cores / cupo | FIFO / 1 / 1 |
| Memoria / página / marcos | 256 bytes / 64 bytes / 4 |
| Offload / reemplazo | 4096 bytes / LRU |
| Quantum RR | 2000 ms (ignorado por FIFO) |
| Memoria / Offload / Loader | 500 / 1000 / 1500 ms |
| Job inicial | aritmetica.asm |

Configs completas: [Planificador](configs/planificador.config), [Placa](configs/placa.config), [Storage](configs/storage.config), [Core 1](configs/core-1.config).

## Preparación y arranque

En todas las terminales, desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3` y mantener ese directorio de trabajo. Crear las carpetas de salida; los datos de entrada ya están versionados en `datos/`.

```bash
mkdir -p runtime/02_aritmetica/storage
```

Reemplazar `/ruta/al/tp` por la ubicación de los binarios. Arrancar en este orden, esperando que los servidores escuchen antes de iniciar sus clientes:

```bash
# Terminal 1
/ruta/al/tp/bin/storage "$PWD/escenarios/02_aritmetica/configs/storage.config"

# Terminal 2
/ruta/al/tp/bin/placa "$PWD/escenarios/02_aritmetica/configs/placa.config"

# Terminal 3
/ruta/al/tp/bin/planificador "$PWD/escenarios/02_aritmetica/configs/planificador.config" "aritmetica.asm"

# Terminal 4
/ruta/al/tp/bin/core "$PWD/escenarios/02_aritmetica/configs/core-1.config" 1
```

El Job inicial y los parámetros INIT_JOB son relativos a PATH_INSTRUCCIONES. No anteponer `scripts/` al nombre del Job con esa config.

## Pasos y resultado esperado

Ejecutar aislado con `entrenamiento_a.bin`. Los cinco uint32_t iniciales son pesos=100, entrada=1, salida=0, gradiente=0 y estado=2.

| Instrucción | Cálculo | Escrituras esperadas |
| --- | --- | --- |
| FORWARD | AX = 0 + 100 × 1 = 100 | salida = 100 |
| BACKWARD | AX = 100 − 100 × 1 = 0 | gradiente = 0 |
| UPDATE | estado = (2 + 100) / 2 = 51; pesos = 100 − 51 = 49 | estado = 51; pesos = 49 |

UPDATE usa como gradiente la salida de FORWARD (P2); el gradiente de BACKWARD queda por separado en P3. Esto permite observar las dos operaciones sin que una tape el resultado de la otra.

REPORT debe emitir una vez los veinte bytes: `31 00 00 00 01 00 00 00 64 00 00 00 00 00 00 00 33 00 00 00`. AX termina en 0; al finalizar UPDATE, E1=100, E2=51, E3=49. Las otras instrucciones no deben alterar esos registros de entrenamiento.

Solo se consumen operandos leídos: FORWARD/BACKWARD no necesitan leer la salida antes de escribirla. Cada instrucción se ejecuta una única vez a pesar de sus posibles reintentos por Page Fault. Después de FREE y EXIT, comprobar liberación de recursos.

## Evidencia y repetición

Conservar logs separados por módulo/Core y el archivo `runtime/02_aritmetica/reportes.log`. Identificar los reportes por JID y por orden dentro de ese Job. Comparar solo los bytes pedidos; mayúsculas/minúsculas de hexadecimal y prefijos de log no alteran el resultado.

Detener todos los módulos antes de repetir o cambiar de escenario. Conservar las evidencias previas y usar un archivo de reportes nuevo para contar las emisiones de esta corrida. No truncar Offload ni borrar Storage mientras los módulos estén activos. No exigir tiempos de pared ni números de marco exactos.
