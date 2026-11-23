# 06_estabilidad — Estabilidad General

Desde la raíz del clon, en **todas** las terminales:

```bash
cd entregas_finales
```

## corrida única

```bash
rm -rf ./runtime/06_estabilidad && mkdir -p ./runtime/06_estabilidad/storage

/ruta/al/tp/bin/storage "$PWD/escenarios/06_estabilidad/configs/storage.config"
/ruta/al/tp/bin/placa "$PWD/escenarios/06_estabilidad/configs/placa.config"
/ruta/al/tp/bin/planificador "$PWD/escenarios/06_estabilidad/configs/planificador.config" ESTABILIDAD.asm
/ruta/al/tp/bin/core "$PWD/escenarios/06_estabilidad/configs/core-1.config" 1
/ruta/al/tp/bin/core "$PWD/escenarios/06_estabilidad/configs/core-2.config" 2
/ruta/al/tp/bin/core "$PWD/escenarios/06_estabilidad/configs/core-3.config" 3
/ruta/al/tp/bin/core "$PWD/escenarios/06_estabilidad/configs/core-4.config" 4
```

Arrancar en ese orden, cada uno en su terminal y esperando que los servidores escuchen. No correr escenarios a la vez: comparten puertos.

## Los Cores

Del bloque de arriba, al principio se arrancan sólo los Cores 1 y 2. Los comandos de los Cores 3
y 4 son para conectarlos durante la corrida, según las actividades del Documento de Pruebas:
mientras haya Jobs, conectar uno nuevo, finalizar uno mientras su Job ejecuta `FORWARD` o
`BACKWARD` y volver a conectar uno finalizado.
