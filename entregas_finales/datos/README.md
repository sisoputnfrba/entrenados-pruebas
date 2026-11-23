# Datos de entrada

Binarios listos para LOAD_BATCH. Los numéricos son uint32_t little-endian. No editarlos con un editor de texto.

| Archivo | Bytes | Contenido |
| --- | ---: | --- |
| PLANI_LARGO.bin | 8 | pesos=2, entrada=3 |
| PLANI_CORTO_1.bin | 8 | pesos=1, entrada=1 |
| PLANI_CORTO_2.bin | 8 | pesos=1, entrada=3 |
| MEM_MODELO_A.bin | 128 | 16 pesos + 16 ceros de estado |
| MEM_LOTE_A.bin | 64 | 16 entradas |
| MEM_MODELO_B.bin | 128 | 16 pesos + 16 ceros de estado |
| MEM_LOTE_B.bin | 64 | 16 entradas |
| SERV_1.bin | 96 | 96 bytes: 'LOTE-1-' + 89 bytes desde 0x10 |
| SERV_2.bin | 96 | 96 bytes: 'LOTE-2-' + 89 bytes desde 0x20 |
| SERV_3.bin | 96 | 96 bytes: 'LOTE-3-' + 89 bytes desde 0x30 |
| ST_MODELO.bin | 128 | 16 pesos + 16 ceros de estado |
| ST_LOTE_1.bin | 64 | 16 entradas |
| ST_LOTE_2.bin | 64 | 16 entradas |
| FS_64.bin | 64 | bytes 0x00..0x3F |
| FS_1600.bin | 1600 | 1600 bytes: (i·7) mod 256 |
| VERSION_1.bin | 512 | 512 bytes de texto: 'V1-BLOQUE-kk-' completado con '1' por bloque de 64 |
| VERSION_2.bin | 512 | 512 bytes de texto: 'V2-BLOQUE-kk-' completado con '2' por bloque de 64 |
| EST_CAPA_1_1.bin | 160 | 40 pesos |
| EST_CAPA_1_2.bin | 160 | 40 pesos |
| EST_ENTRADA_1.bin | 160 | 40 entradas |
| EST_CAPA_2_1.bin | 160 | 40 pesos |
| EST_CAPA_2_2.bin | 160 | 40 pesos |
| EST_ENTRADA_2.bin | 160 | 40 entradas |
| EST_CAPA_3_1.bin | 160 | 40 pesos |
| EST_CAPA_3_2.bin | 160 | 40 pesos |
| EST_ENTRADA_3.bin | 160 | 40 entradas |
| EST_CAPA_4_1.bin | 160 | 40 pesos |
| EST_CAPA_4_2.bin | 160 | 40 pesos |
| EST_ENTRADA_4.bin | 160 | 40 entradas |
| EST_CAPA_5_1.bin | 160 | 40 pesos |
| EST_CAPA_5_2.bin | 160 | 40 pesos |
| EST_ENTRADA_5.bin | 160 | 40 entradas |
| EST_CAPA_6_1.bin | 160 | 40 pesos |
| EST_CAPA_6_2.bin | 160 | 40 pesos |
| EST_ENTRADA_6.bin | 160 | 40 entradas |
