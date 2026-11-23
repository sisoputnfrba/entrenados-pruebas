# 05_journal — Journal

Desde la raíz del clon, en **todas** las terminales:

```bash
cd entregas_finales
```

## A

```bash
rm -rf ./runtime/05_journal_a && mkdir -p ./runtime/05_journal_a/storage

/ruta/al/tp/bin/storage "$PWD/escenarios/05_journal/configs/A/storage.config"
/ruta/al/tp/bin/placa "$PWD/escenarios/05_journal/configs/A/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/05_journal/configs/A/planificador.config" JOURNAL.asm
/ruta/al/tp/bin/core "$PWD/escenarios/05_journal/configs/core-1.config" 1
```

## B

```bash
rm -rf ./runtime/05_journal_b && mkdir -p ./runtime/05_journal_b/storage

/ruta/al/tp/bin/storage "$PWD/escenarios/05_journal/configs/B/storage.config"
/ruta/al/tp/bin/placa "$PWD/escenarios/05_journal/configs/B/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/05_journal/configs/B/planificador.config" JOURNAL.asm
/ruta/al/tp/bin/core "$PWD/escenarios/05_journal/configs/core-1.config" 1
```

Arrancar en ese orden, cada uno en su terminal y esperando que los servidores escuchen. No correr escenarios a la vez: comparten puertos.

## Después del `kill -9`

En cada corte, con el Storage muerto en el momento indicado en el Documento de Pruebas:

1. Cortar el resto de los módulos (Ctrl+C). **No borrar `runtime/`**.
2. Levantar sólo el Storage con la misma config: tiene que loguear la recuperación. Ejecutar
   `LS` y `CAT modelo 16` en su consola.
3. Cortarlo y volver a levantarlo: `LS` y `CAT` dan lo mismo.
4. Sin cortar el Storage, levantar Placa, Planificador y Core con el script de verificación del
   corte:

```bash
/ruta/al/tp/bin/placa "$PWD/escenarios/05_journal/configs/A/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/05_journal/configs/A/planificador.config" JOURNAL_VERIFICAR_A.asm
/ruta/al/tp/bin/core "$PWD/escenarios/05_journal/configs/core-1.config" 1
```

Para el corte B, usar `configs/B/` y `JOURNAL_VERIFICAR_B.asm`.
