# EntrenadOS - Suite de Tests y Jobs

Este repositorio centraliza los Jobs, configuraciones y resultados esperados para validar el Trabajo Práctico EntrenadOS de Sistemas Operativos (UTN FRBA, 2C2026).

## Pruebas disponibles

- [Checkpoint 2: planificación y ejecución básica](checkpoints/checkpoint_2/README.md). Incluye instrucciones aisladas, FIFO, RR, cambios de contexto y multiprogramación.
- [Job de entrenamiento de ejemplo](ejemplo_job/README.md). Usa memoria y servicios; no forma parte de las pruebas de CP2.

## Organización y próximas entregas

```text
checkpoints/
  checkpoint_2/
    docs/
    escenarios/
    resultados/
    scripts/
    runtime/          # Salidas locales; ignoradas por Git
  checkpoint_3/       # Próxima publicación
  checkpoint_4/       # Publicación posterior
entregas_finales/     # Próximas pruebas de integración final
ejemplo_job/
```

Las carpetas de futuras entregas se crearán al publicar sus pruebas. El checkpoint 1 corresponde a conexiones y serialización y aún no tiene archivos en este repositorio.

Cada etapa conserva su estructura y sus propias configuraciones, scripts, resultados y salidas. Las pruebas se ejecutan desde la carpeta de la etapa elegida, según su README. Por ejemplo, desde la raíz del clon:

```bash
cd checkpoints/checkpoint_2
```

Mantener ese directorio de trabajo en todas las terminales de los módulos. Las rutas relativas de las configs se resuelven desde allí; los argumentos de `INIT_JOB` se resuelven desde `PATH_INSTRUCCIONES`. No ejecutar escenarios simultáneamente: comparten puertos.

## Cómo usar estos archivos

Todos los pseudocódigos, incluidos los Jobs y launchers de CP2 y el ejemplo de entrenamiento, se publican en `.asm`. Mantener los nombres indicados en cada prueba: las referencias de `INIT_JOB` y los comandos de arranque usan esa extensión.

Cada escenario de CP2 incluye configs completas y comandos de preparación y arranque. Requiere Linux y los módulos del grupo compilados; ajustar la ruta de sus binarios según la [guía de configuración](checkpoints/checkpoint_2/docs/configuracion.md).

Consultar los resultados esperados y conservar las evidencias de cada ejecución antes de repetir.

La incorporación de CP2 y sus referencias están documentadas en el [mapa de migración](docs/migracion-checkpoint-2.md).
