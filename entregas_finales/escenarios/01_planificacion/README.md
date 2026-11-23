# 01_planificacion — Planificación

Desde la raíz del clon, en **todas** las terminales:

```bash
cd entregas_finales
```

## FIFO

```bash
rm -rf ./runtime/01_planificacion_fifo && mkdir -p ./runtime/01_planificacion_fifo/storage

/ruta/al/tp/bin/storage "$PWD/escenarios/01_planificacion/configs/FIFO/storage.config"
/ruta/al/tp/bin/placa "$PWD/escenarios/01_planificacion/configs/FIFO/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/01_planificacion/configs/FIFO/planificador.config" PLANIFICACION.asm
/ruta/al/tp/bin/core "$PWD/escenarios/01_planificacion/configs/core-1.config" 1
```

## RR

```bash
rm -rf ./runtime/01_planificacion_rr && mkdir -p ./runtime/01_planificacion_rr/storage

/ruta/al/tp/bin/storage "$PWD/escenarios/01_planificacion/configs/RR/storage.config"
/ruta/al/tp/bin/placa "$PWD/escenarios/01_planificacion/configs/RR/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/01_planificacion/configs/RR/planificador.config" PLANIFICACION.asm
/ruta/al/tp/bin/core "$PWD/escenarios/01_planificacion/configs/core-1.config" 1
```

## HRRN

```bash
rm -rf ./runtime/01_planificacion_hrrn && mkdir -p ./runtime/01_planificacion_hrrn/storage

/ruta/al/tp/bin/storage "$PWD/escenarios/01_planificacion/configs/HRRN/storage.config"
/ruta/al/tp/bin/placa "$PWD/escenarios/01_planificacion/configs/HRRN/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/01_planificacion/configs/HRRN/planificador.config" PLANIFICACION.asm
/ruta/al/tp/bin/core "$PWD/escenarios/01_planificacion/configs/core-1.config" 1
```

Arrancar en ese orden, cada uno en su terminal y esperando que los servidores escuchen. No correr escenarios a la vez: comparten puertos.
