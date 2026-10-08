# Resultados RR

La duración real de una instrucción depende del grupo. RR_QUANTUM es tiempo en ms, no un contador de instrucciones.

- A y B deben tener varios turnos con EXEC → READY por quantum y READY → EXEC posterior.
- Encolar el Job desalojado al final de READY; no exigir una rotación fija durante la creación de los hijos.
- Concatenar FETCH por JID a través de sus turnos. Debe coincidir con [individuales.md](individuales.md).
- Mantener PC y todos los registros; cada Job debe terminar una sola vez por EXIT. Las interrupciones pendientes no pueden afectar al siguiente Job.
- Con dos Cores: ejecución exclusiva de cada JID, progreso simultáneo cuando haya Jobs disponibles y contextos preservados si migran. No exigir un Core concreto ni orden total de EXIT.
- Grado 1: hijos en NEW hasta liberar el cupo; INIT_JOB no espera admisión. Grado 2: como máximo dos Jobs en READY/EXEC, con admisión FIFO.
