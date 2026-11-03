# 10 — Formateo y comandos locales de Storage

Ejecutar solo Storage con su [config completa](configs/storage.config). No requiere Core, Placa ni Planificador.

## Preparación

Desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3`. Usar una carpeta vacía para la primera ejecución. Si ya existe un volumen de una corrida anterior, conservarlo y cambiar PATH_STORAGE por una carpeta nueva. El nombre real del archivo de volumen lo decide cada grupo.

```bash
mkdir -p runtime/10_storage_directorio/storage
/ruta/al/tp/bin/storage "$PWD/escenarios/10_storage_directorio/configs/storage.config"
```

La ausencia del archivo de volumen debe disparar el formateo automático. No hay comando FORMAT definido.

## Geometría esperada

| Región | Bloques | Detalle |
| --- | --- | --- |
| Superbloque | 0 | Firma FAT32_TRAIN en campo de 11 bytes; 64/32/1 en sus campos uint32_t; padding cero |
| FAT | 1–2 | 32 entradas × 4 bytes = 128 bytes |
| Directorio | 3 | 64 bytes / entrada de 32 bytes = 2 entradas |
| Datos | 4–31 | 28 bloques libres |
| Volumen | 32 bloques | 2048 bytes totales |

FAT de bloques 0–3 = 0x0FFFFFFE; bloques de datos libres = 0. Directorio inicialmente libre. No fijar cuál bloque de datos se asigna primero. Puede inspeccionarse el volumen con herramientas del sistema, además de INFO/LS; no exigir un nombre de archivo no especificado.

## Secuencia manual por stdin

```text
INFO
LS
WRITE cp3_a 000102030405060708090A0B0C0D0E0F10111213
LS
CAT cp3_a 20
WRITE cp3_a A1B2
LS
CAT cp3_a 2
WRITE cp3_b 11223344
LS
WRITE cp3_c 5566
LS
RM cp3_b
WRITE cp3_c 5566
CAT cp3_c 2
LS
```

Cada WRITE tiene un único argumento de hexadecimales, sin prefijo 0x; si el parser local documenta otro separador, adaptar solo su representación, conservando los mismos bytes. El máximo utilizado es 20 bytes.

| Punto | Resultado |
| --- | --- |
| Inicio | INFO muestra la geometría; LS sin entradas ocupadas |
| Primer WRITE a | tamaño 20; CAT = 00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F 10 11 12 13 |
| Sobrescritura a | una sola entrada cp3_a, tamaño 2; CAT = A1 B2 |
| WRITE b | dos entradas ocupadas; cp3_b tamaño 4, contenido 11 22 33 44 |
| Primer WRITE c | error de directorio lleno; a y b conservan tamaño/contenido; no fuga de bloques |
| RM b y nuevo WRITE c | b desaparece; c ocupa una entrada, tamaño 2; CAT = 55 66 |

Detener Storage normalmente y reiniciarlo con la misma config, sin borrar su carpeta. LS y CAT deben conservar a y c; no debe reformatearse un volumen existente. Después ejecutar RM cp3_a y RM cp3_c: vuelven a quedar libres las dos entradas y los 28 bloques de datos.

No se exige recuperación ante kill -9, journal ni solicitudes de red de checkpoints en esta prueba CP3. WRITE local de hasta 20 bytes no puede cruzar un bloque de tamaño mínimo 32; no presentar esta prueba como cobertura de archivos multibloque.
