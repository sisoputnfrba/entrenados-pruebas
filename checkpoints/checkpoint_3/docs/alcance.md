# Alcance docente del checkpoint 3

Fuente: [TP 2C2026 — EntrenadOS](https://docs.google.com/document/d/1UzKDYRO03_UXn1QLn9d7thgEToC9QdpQTtJlLdFoqrQ/edit), versión visible 1.0, consultada el 25/10/2026. Secciones: Descripción de las entregas / Check de Control Obligatorio 3; Core / MMU y Execute; Planificador / Page Fault, Syscalls y Servicios; Placa / Operaciones y Reemplazo; Storage / Estructura interna y Comandos locales.

El CP3 está fechado para el **31/10**. Esta batería es una propuesta docente preparada antes de la entrega. Los resultados son oráculos derivados del enunciado; todavía requieren una corrida contra los binarios de los grupos.

## Cobertura

| Requisito | Escenarios |
| --- | --- |
| ALLOC/FREE, páginas bajo demanda, respaldo en Offload | 01, 08 |
| Traducción MMU y fragmentación entre páginas | 03, 06, 07 |
| Page Fault sin avance de PC ni efectos parciales | 03; también en todos los accesos a páginas ausentes |
| FORWARD, BACKWARD, UPDATE y registros E1/E2/E3 | 02, 04, 05, 09 |
| Reemplazo LRU y datos modificados recuperados | 04 |
| Reemplazo CLOCK-M y datos modificados recuperados | 05, 09 |
| Page locking durante instrucciones/syscalls y deslockeo | 03, 06, 07, 09 |
| Loader, Labeler y Logger bloqueantes con FIFO por servicio | 06, 07 |
| Concurrencia, aislamiento y reemplazo global | 06, 09 |
| Serialización de operaciones de Offload | 09 |
| Finalización por falta de Offload y liberación de recursos | 08 |
| Formateo, INFO, LS, CAT, WRITE y RM locales de Storage | 10, 11 |
| Directorio lleno, bloques agotados y reutilización | 10, 11 |
| Conservación del volumen tras reinicio normal | 10 |

Los escenarios 02–05 son aislados para reducir ambigüedad; 06–09 usan launchers donde hace falta coexistencia o admisión sucesiva. El Job inicial de 08 es un launcher pero multiprogramación=1: los hijos se crean en NEW sin bloquear al padre.
