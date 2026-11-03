# 11 — Agotamiento de bloques de datos

Ejecutar solo Storage con su [config completa](configs/storage.config), sobre un volumen nuevo y separado del escenario 10.

Desde la raíz del clon ejecutar `cd checkpoints/checkpoint_3` y mantener ese directorio de trabajo.

```bash
mkdir -p runtime/11_storage_bloques/storage
/ruta/al/tp/bin/storage "$PWD/escenarios/11_storage_bloques/configs/storage.config"
```

Si ya existe el volumen, conservarlo y apuntar PATH_STORAGE a otra carpeta vacía antes de comenzar.

## Geometría

CANT_BLOQUES=8, TAM_BLOQUE=32, BLOQUES_DIRECTORIO=4. Volumen de 256 bytes: superbloque 0; FAT 1 (8 × 4 = 32 bytes); directorio 2–5 (cuatro entradas); datos 6–7 (dos bloques). FAT reservada 0–5 = 0x0FFFFFFE.

## Secuencia manual

```text
INFO
LS
WRITE cp3_a 01020304
WRITE cp3_b 05060708
WRITE cp3_c 090A0B0C
LS
CAT cp3_a 4
CAT cp3_b 4
RM cp3_a
WRITE cp3_c 090A0B0C
LS
CAT cp3_c 4
RM cp3_b
RM cp3_c
LS
```

Los primeros dos archivos consumen ambos bloques de datos aunque solo usen 4 bytes cada uno. El tercer WRITE falla por falta de espacio en FAT/datos, con dos entradas de directorio todavía disponibles. No debe dejar una entrada ocupada ni modificar a/b.

Después de RM a, WRITE c tiene éxito y CAT devuelve 09 0A 0B 0C. Al eliminar b y c, los dos bloques de datos y las cuatro entradas quedan libres. Inspeccionar la FAT si LS no permite probar que los bloques se liberaron.

Los comandos son locales; no se espera finalizar ningún Job por estos errores, porque no hay Jobs ejecutando. No simular kill -9 ni evaluar recuperación de journal.
