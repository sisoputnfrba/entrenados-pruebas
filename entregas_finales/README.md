# EntrenadOS — Pruebas de las Entregas Finales (28/11, 12/12 y 19/12)

Pseudocódigos, datos de entrada y configuraciones completas de las seis pruebas del
[Documento de Pruebas Finales](https://faq.utnso.com.ar/entrenados-pruebas).

| Prueba | Qué cubre | Cores | Job inicial |
| --- | --- | ---: | --- |
| [01 · Planificación](escenarios/01_planificacion/README.md) | FIFO, RR y HRRN sobre los mismos Jobs | 1 | PLANIFICACION.asm |
| [02 · Memoria](escenarios/02_memoria/README.md) | Entrenamiento concurrente con LRU y CLOCK-M | 2 | MEMORIA.asm |
| [03 · Servicios](escenarios/03_servicios/README.md) | Loader, Labeler y Logger con colas FIFO y page locking | 2 | SERVICIOS.asm |
| [04 · Storage](escenarios/04_storage/README.md) | Checkpoints entre Jobs, directorio lleno, falta de espacio y reutilización | 1 | ST_ENTRENAR.asm / FS_A.asm |
| [05 · Journal](escenarios/05_journal/README.md) | `kill -9` antes y después del COMMIT, y recuperación | 1 | JOURNAL.asm |
| [06 · Estabilidad General](escenarios/06_estabilidad/README.md) | 13 Jobs, HRRN, CLOCK-M, Cores que entran y salen | 4 | ESTABILIDAD.asm |

## Uso

Requiere Linux y los cuatro módulos del grupo compilados. Desde la raíz del clon, en **todas** las
terminales:

```bash
cd entregas_finales
```

Las configs de `escenarios/` usan rutas relativas a ese directorio: `PATH_INSTRUCCIONES=./scripts/`,
`PATH_DATOS=./datos/` y las salidas en `./runtime/<prueba>/`, que Git ignora. El Job inicial y los
argumentos de `INIT_JOB` son nombres relativos a `PATH_INSTRUCCIONES`. Las IPs son `127.0.0.1` y los
puertos 8080 (Planificador), 8081 (Placa) y 8082 (Storage). Para correr en varias máquinas,
ajustar las IPs de las configs de Planificador y Core.

## Estructura

- `scripts/`: los Jobs, en `.asm`. Una instrucción por línea; los `JNZ` saltan a números de
  instrucción contando desde 0.
- `datos/`: [entradas binarias](datos/README.md) para LOAD_BATCH, en uint32_t little-endian.
- `escenarios/<prueba>/`: comandos de arranque y configs completas, una carpeta por variante.
