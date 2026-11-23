# 03_servicios — Servicios

Desde la raíz del clon, en **todas** las terminales:

```bash
cd entregas_finales
```

## corrida única

```bash
rm -rf ./runtime/03_servicios && mkdir -p ./runtime/03_servicios/storage

/ruta/al/tp/bin/storage "$PWD/escenarios/03_servicios/configs/storage.config"
/ruta/al/tp/bin/placa "$PWD/escenarios/03_servicios/configs/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/03_servicios/configs/planificador.config" SERVICIOS.asm
/ruta/al/tp/bin/core "$PWD/escenarios/03_servicios/configs/core-1.config" 1
/ruta/al/tp/bin/core "$PWD/escenarios/03_servicios/configs/core-2.config" 2
```

Arrancar en ese orden, cada uno en su terminal y esperando que los servidores escuchen. No correr escenarios a la vez: comparten puertos.
