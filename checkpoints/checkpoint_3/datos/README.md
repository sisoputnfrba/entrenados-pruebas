# Datos de entrada

Archivos binarios versionados y listos para LOAD_BATCH. No editarlos con un editor de texto ni agregar saltos de línea. PATH_DATOS apunta a esta carpeta.

| Archivo | Bytes | Contenido |
| --- | ---: | --- |
| hola.bin | 4 | 48 6F 6C 61 (Hola) |
| test.bin | 4 | 54 45 53 54 (TEST) |
| operandos.bin | 8 | uint32_t little-endian: 2, 3 |
| entrenamiento_a.bin | 20 | uint32_t little-endian: 100, 1, 0, 0, 2 |
| entrenamiento_b.bin | 20 | uint32_t little-endian: 200, 1, 0, 0, 4 |

Los fixtures numéricos asumen la VM Linux little-endian habitual. El enunciado define uint32_t pero no un orden de bytes de memoria: una plataforma con otro endianness requiere adaptar los fixtures y los hexadecimales esperados, conservando los mismos valores numéricos. No interpretar los bytes como texto decimal.

Inspección manual opcional con `od -An -tx1 datos/entrenamiento_a.bin`. No se necesita ejecutar un generador de datos.
