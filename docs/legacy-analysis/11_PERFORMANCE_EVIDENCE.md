# Evidencia histórica de rendimiento del legacy

## Propósito y límites

Este documento registra evidencia histórica sanitizada para orientar requisitos
de medición, rendimiento y escalabilidad de SimuCenter Next. Los datos proceden
de una ejecución instrumentada y de muestras parciales; no deben interpretarse
como una prueba completa de capacidad del legacy ni como evidencia de capacidad
de SimuCenter Next.

Los archivos JSONL crudos no se incorporan al repositorio porque contienen
identificadores, stack traces y detalles internos. En particular, este documento
no reproduce UUID, `user_id`, `tenant_id` ni trazas de ejecución.

## [Seguro] Hechos directamente observados

### Ejecución instrumentada

- Concurrencia: 20.
- Duración: 60 segundos.
- Think time: 250 ms.
- Requests: 2122.
- Throughput: 35.37 req/s.
- Errores: 0.
- Latencia p50: 218.73 ms.
- Latencia p95: 702.17 ms.
- Latencia p99: 844.68 ms.
- Pico de conexiones PostgreSQL: 20 de 100.
- CPU y RSS reportaron cero. Esa instrumentación no es válida para concluir
  ausencia de saturación.

### Muestra `c04-pool20-request.jsonl`

La muestra contiene 500 registros HTTP. Todos los registros observados tienen
status 200.

- Promedio: 348.53 ms.
- p50: 281 ms.
- p95: 927 ms.
- p99: 1098 ms.
- Máximo: 1212 ms.
- Promedio de consultas por request: 8.718.
- Promedio de tiempo SQL acumulado por request: 110.87 ms.

Esta muestra no demuestra por sí sola el resultado completo de una prueba.

### Muestra `c05-request-150u.jsonl`

El artefacto identifica la muestra como correspondiente a 150 usuarios y
contiene 500 registros HTTP. Todos los registros observados tienen status 200.

- Promedio: 1393.96 ms.
- p50: 1375 ms.
- p95: 2319 ms.
- p99: 6131 ms.
- Máximo: 7478 ms.
- Promedio de consultas por request: 8.692.
- Promedio de tiempo SQL acumulado por request: 359.15 ms.

Los artefactos revisados no incluyen un resumen completo del escenario, la
duración, el think time ni la tasa total de errores de esa ejecución.

### Consultas lentas

- `c04-slow-150u`: 19 registros, promedio de 612.68 ms y máximo de 776 ms.
- `c05-slow-150u`: 122 registros, promedio de 1594.84 ms, p95 de 5166 ms,
  p99 de 5651 ms y máximo de 6010 ms.

### Agotamiento del pool

- Se observaron 25 excepciones durante aproximadamente 30 segundos.
- SQLAlchemy reportó un `QueuePool` de tamaño 5, overflow 5 y timeout de
  adquisición de 2 segundos.
- Las excepciones ocurrieron en varias rutas GET.

### Volumen de logging

- Railway reportó 37 advertencias por alcanzar 500 logs por segundo por
  réplica.
- Los valores reportados como descartados suman aproximadamente 29 811. No son
  una cantidad exacta de eventos únicos porque pueden corresponder a ventanas o
  reportes acumulados.
- `c04-backend-500.jsonl` se refiere al límite de 500 logs por segundo; no
  demuestra una prueba de 500 usuarios.

### Densidad y estimaciones de carga

- Las muestras sitúan la densidad aproximada entre 8.7 y 13 consultas por
  request.
- 500 usuarios con una acción cada cinco segundos equivalen aproximadamente a
  100 req/s.
- 5000 usuarios con el mismo patrón equivalen aproximadamente a 1000 req/s.
- Extrapolar consultas por segundo a partir de estas cifras solo ofrece una
  estimación de orden de magnitud; no predice la capacidad del sistema.

## [Probable] Causas contribuyentes

El agotamiento del pool, la cantidad de consultas por request y la contención
son causas contribuyentes probables de la degradación observada. La evidencia
disponible no permite atribuirles por separado una proporción causal ni excluir
otros factores.

## [No determinado] Aspectos sin evidencia concluyente

Con los artefactos disponibles no se puede determinar:

- el uso real ni la saturación de CPU;
- el uso real ni la saturación de memoria;
- la presencia y el efecto de locks;
- los planes SQL ejecutados;
- la adecuación o ausencia de índices;
- la capacidad real para 500 usuarios;
- el efecto exacto de variar workers o réplicas.

Estas incógnitas requieren instrumentación válida y una prueba reproducible con
escenario, entorno, recursos y resultados completos.
