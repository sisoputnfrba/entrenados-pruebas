# 02_memoria — Memoria

Desde la raíz del clon, en **todas** las terminales:

```bash
cd entregas_finales
```

## LRU

```bash
rm -rf ./runtime/02_memoria_lru && mkdir -p ./runtime/02_memoria_lru/storage

/ruta/al/tp/bin/storage "$PWD/escenarios/02_memoria/configs/LRU/storage.config"
/ruta/al/tp/bin/placa "$PWD/escenarios/02_memoria/configs/LRU/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/02_memoria/configs/LRU/planificador.config" MEMORIA.asm
/ruta/al/tp/bin/core "$PWD/escenarios/02_memoria/configs/core-1.config" 1
/ruta/al/tp/bin/core "$PWD/escenarios/02_memoria/configs/core-2.config" 2
```

## CLOCK-M

```bash
rm -rf ./runtime/02_memoria_clockm && mkdir -p ./runtime/02_memoria_clockm/storage

/ruta/al/tp/bin/storage "$PWD/escenarios/02_memoria/configs/CLOCK-M/storage.config"
/ruta/al/tp/bin/placa "$PWD/escenarios/02_memoria/configs/CLOCK-M/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/02_memoria/configs/CLOCK-M/planificador.config" MEMORIA.asm
/ruta/al/tp/bin/core "$PWD/escenarios/02_memoria/configs/core-1.config" 1
/ruta/al/tp/bin/core "$PWD/escenarios/02_memoria/configs/core-2.config" 2
```

Arrancar en ese orden, cada uno en su terminal y esperando que los servidores escuchen. No correr escenarios a la vez: comparten puertos.
