# Migración de las pruebas del checkpoint 2

Origen: `mesaglio/entrenados-checkpoint-2`, commit `a57ae0a`.
Destino: `sisoputnfrba/entrenados-pruebas`, carpeta `checkpoints/checkpoint_2/`.
Rama de integración: `feat/incorporar-checkpoint-2`.

## Mapa de archivos

| Origen | Destino |
| --- | --- |
| `README.md` | [checkpoints/checkpoint_2/README.md](../checkpoints/checkpoint_2/README.md) |
| `docs/configuracion.md` | [checkpoints/checkpoint_2/docs/configuracion.md](../checkpoints/checkpoint_2/docs/configuracion.md) |
| `escenarios/` | `checkpoints/checkpoint_2/escenarios/` |
| `scripts/individuales/` | `checkpoints/checkpoint_2/scripts/individuales/` |
| `scripts/launchers/` | `checkpoints/checkpoint_2/scripts/launchers/` |
| `resultados/` | `checkpoints/checkpoint_2/resultados/` |
| `.gitignore` | `checkpoints/checkpoint_2/.gitignore` más la regla global `**/runtime/` |
| `runtime/` (no versionado) | `checkpoints/checkpoint_2/runtime/` (generado durante la ejecución) |

Se preservan los 49 archivos versionados del origen, la estructura interna, las extensiones `.txt`, las instrucciones y las configs. El ejemplo de entrenamiento existente permanece en `ejemplo_job/`.

## Resolución de referencias internas

- Enlaces Markdown: relativos al documento que los contiene. Al mover juntos los directorios internos, conservan sus destinos.
- Configs y comandos con `$PWD`: directorio de trabajo `checkpoints/checkpoint_2/`. Cada terminal debe ubicarse allí antes de arrancar; los README incluyen el cambio de directorio desde la raíz del clon.
- `PATH_INSTRUCCIONES=./scripts/`: apunta a `checkpoints/checkpoint_2/scripts/`.
- `INIT_JOB individuales/<archivo>.txt`: relativo a `PATH_INSTRUCCIONES`, no al launcher ni a la raíz de `entrenados-pruebas`.
- `PATH_DATOS`, `PATH_REPORTES`, `PATH_OFFLOAD` y `PATH_STORAGE`: salidas en `checkpoints/checkpoint_2/runtime/<escenario>/`; los README preparan las carpetas necesarias.
- Job inicial: los comandos pasan una ruta absoluta con `$PWD/scripts/...`. La guía explica la alternativa relativa para implementaciones que la requieran.

## Rutas externas y dependencias

| Referencia | Uso y adaptación |
| --- | --- |
| `/ruta/al/tp/bin/{storage,placa,planificador,core}` | Marcador externo al repositorio. Cada grupo debe reemplazarlo por la ubicación de sus binarios compilados; estos no se incluyen. |
| `/home/utnso/entrenados-pruebas/checkpoints/checkpoint_2/...` | Ejemplos de rutas absolutas en la guía. Reemplazar por la ubicación real del clon si se ejecuta desde otra carpeta. |
| `127.0.0.1`, puertos `8080`, `8081`, `8082` | Conectividad local entre módulos. Para varias máquinas, adaptar IP y rutas locales según la guía. |
| `lote1`, `modelo7` en `ejemplo_job/job_entrenamiento.asm` | Identificadores lógicos de datos y checkpoint, no rutas a archivos de CP2. Su provisión depende de los servicios del TP; el ejemplo no se usa para aprobar CP2. |

Los archivos del origen no incluyen enlaces HTTP(S), rutas a otro repositorio ni dependencias de archivos externos de entrada. Las menciones al enunciado son referencias textuales. Los binarios externos y la conectividad se validan al ejecutar con la implementación de cada grupo.

## Próximas publicaciones

Agregar CP3 en `checkpoints/checkpoint_3/` y las pruebas finales en `entregas_finales/`, conservando un directorio de trabajo y un `runtime/` propios por etapa. Actualizar el índice raíz cuando sus archivos estén disponibles. No reutilizar las salidas de CP2 ni modificar sus rutas para incorporar otra etapa.

## Validación de la migración

Se verificaron los 49 archivos trasladados, 81 enlaces Markdown (incluidos sus anclajes), 31 referencias de arranque con `$PWD`, 25 configs y las 7 referencias `INIT_JOB` de los launchers contra la raíz de instrucciones de los 6 escenarios. Las 24 rutas de salida tienen sus carpetas de preparación documentadas y quedan ignoradas por Git. Los Jobs, launchers y configs coinciden byte a byte con el origen; el ejemplo existente también se conserva.

Una simulación de las instrucciones de los 9 Jobs individuales confirmó la cantidad de instrucciones ejecutadas, el PC de EXIT y todos los registros finales contra las tablas de resultados. Esto valida los archivos de prueba, no la implementación de los módulos: la ejecución integrada requiere Linux y los binarios de cada grupo.

Se ejecutó además la preparación de los 6 escenarios y sus 25 comandos de arranque en una copia temporal, con binarios simulados que comprueban el directorio de trabajo, los argumentos, las configs, los Jobs y las carpetas de salida. Se corrigió en las 6 guías la indicación anterior de mantener las terminales en la raíz del repositorio.
