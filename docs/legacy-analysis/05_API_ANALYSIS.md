# Análisis de API

## Superficie

Se detectaron 480 decoradores HTTP en 45 archivos de router. La mayoría vive bajo `/api/v1`; existen además `/`, `/health` y el stream `/api/v1/events`. La API cubre CRUD, workflows, reportes, archivos, administración global y portal estudiante.

## Patrones positivos

- prefijo versionado `/api/v1`;
- Pydantic para contratos de entrada/salida;
- dependencias reutilizables de autenticación y permisos;
- paginación en listados relevantes;
- códigos 201/204 usados en múltiples operaciones;
- rate limiting general y límite especial de login;
- `X-Account-Status` expuesto para bloqueo de tenant;
- request ID y handler de error central;
- OpenAPI inherente a FastAPI.

## Problemas estructurales

1. **Routers como capa de aplicación.** Varios routers superan 600–1.000 líneas y mezclan HTTP, autorización, queries, transacciones, reglas y side effects.
2. **Contratos no gobernados.** No hay evidencia de OpenAPI congelado, semantic diff o clientes generados; frontend mantiene clientes manuales.
3. **Idempotencia desigual.** Operaciones como generar órdenes, tandas, confirmaciones o archivos necesitan política explícita de idempotency keys.
4. **Autorización inconsistente por diseño heredado.** Hay dependencias granulares, pero algunos endpoints aún usan roles/tipos o lógica local.
5. **Scopes de datos incompletos.** La existencia de `scope_query()` en modo permisivo confirma que owner/assigned/all no está resuelto uniformemente.
6. **Errores de dominio.** Las respuestas dependen de `HTTPException` local; falta un catálogo estable de códigos de error independiente del texto.

## Convención recomendada para Next

```text
/api/v1/{domain-resources}
headers: Authorization, X-Request-ID, Idempotency-Key cuando aplique
error: { code, message, details, request_id }
pagination: cursor preferido para colecciones crecientes
concurrency: ETag/If-Match o version en agregados sensibles
```

Cada endpoint debe mapearse a un caso de uso y no consultar tablas de otro dominio directamente. Los contratos OpenAPI serán fuente para generar clientes web/móvil y contract tests.

## Gates de aceptación

- cada operación tiene permiso y scope declarados;
- todos los IDs se validan dentro del tenant;
- errores tienen código estable;
- mutaciones críticas son idempotentes o documentan por qué no;
- no hay lógica de negocio sustantiva en routers;
- pruebas de contrato frontend/backend;
- OpenAPI diff bloquea breaking changes accidentales.

