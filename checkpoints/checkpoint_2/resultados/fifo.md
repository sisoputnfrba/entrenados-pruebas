# Resultados FIFO

Con `02_fifo_un_core`: JID 0 crea A, B y corto y finaliza. Identificar los JID por creación, sin fijar números de hijos.

Orden de ejecución de los hijos: `06_contexto_a` → `07_contexto_b` → `08_corto`. Cada hijo finaliza antes de iniciar el siguiente. No hay desalojo por quantum. Estados de cada hijo: NEW → READY → EXEC → EXIT.

Valores finales y FETCH: [individuales.md](individuales.md). A termina con AX=0 y BX=200; B con AX=0 y BX=1300; corto con AX=77. El quantum configurado en FIFO no produce interrupciones.
