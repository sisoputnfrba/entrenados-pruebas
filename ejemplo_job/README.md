# Job de Entrenamiento - EntrenadOS

Este archivo contiene un script de ejemplo que simula un trabajo de entrenamiento (Job) para el sistema operativo distribuido del TP EntrenadOS.

## Descripcion
Este Job realiza una iteracion completa de un entrenamiento sobre una capa de 32 palabras, reservando un total de 640 bytes de memoria. El script ilustra el flujo logico que el Planificador y el Core deben ser capaces de procesar:

1. **Reserva de Memoria**: Uso de `ALLOC` para configurar el espacio de trabajo.
2. **Carga de Datos**: Uso de `LOAD_BATCH` para obtener los datos de entrenamiento.
3. **Carga de Modelos**: Uso de `LOAD_CHECKPOINT` para recuperar el estado del modelo.
4. **Ciclo de Entrenamiento**: Secuencia de operaciones `FORWARD`, `BACKWARD` y `UPDATE`.
5. **Persistencia y Finalizacion**: Uso de `REPORT` y `SAVE_CHECKPOINT` antes de finalizar.

## Como usar este script?
1. Asegurate de que el path a este archivo este configurado correctamente en el archivo de configuracion del `Planificador`.
2. Al ejecutar el `Planificador`, puedes pasar este archivo como argumento del `Job Inicial` (JID 0).
3. Este script es una excelente base para verificar el correcto funcionamiento del **Ciclo de Instruccion** (Fetch, Decode, Execute) y la **MMU** (traduccion de direcciones).
