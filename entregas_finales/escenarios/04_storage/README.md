# 04_storage — Storage

Desde la raíz del clon, en **todas** las terminales:

```bash
cd entregas_finales
```

## checkpoints

```bash
rm -rf ./runtime/04_storage_checkpoints && mkdir -p ./runtime/04_storage_checkpoints/storage

/ruta/al/tp/bin/storage "$PWD/escenarios/04_storage/configs/checkpoints/storage.config"
/ruta/al/tp/bin/placa "$PWD/escenarios/04_storage/configs/checkpoints/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/04_storage/configs/checkpoints/planificador.config" ST_ENTRENAR.asm
/ruta/al/tp/bin/core "$PWD/escenarios/04_storage/configs/core-1.config" 1
```

## fs_lleno

```bash
rm -rf ./runtime/04_storage_fs_lleno && mkdir -p ./runtime/04_storage_fs_lleno/storage

/ruta/al/tp/bin/storage "$PWD/escenarios/04_storage/configs/fs_lleno/storage.config"
/ruta/al/tp/bin/placa "$PWD/escenarios/04_storage/configs/fs_lleno/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/04_storage/configs/fs_lleno/planificador.config" FS_A.asm
/ruta/al/tp/bin/core "$PWD/escenarios/04_storage/configs/core-1.config" 1
```

Arrancar en ese orden, cada uno en su terminal y esperando que los servidores escuchen. No correr escenarios a la vez: comparten puertos.

## Al terminar cada corrida

Sin cortar el Storage, ejecutar `LS` en su consola. Después cortarlo y volver a levantarlo con la
misma config: `LS` tiene que mostrar lo mismo.
