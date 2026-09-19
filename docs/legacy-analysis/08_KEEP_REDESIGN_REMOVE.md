# Matriz de disposición del sistema actual

| Elemento | Decisión | Motivo |
|---|---|---|
| FastAPI, React, PostgreSQL, Redis | KEEP | adecuados para carga y equipo previstos |
| Railway inicial | KEEP | reduce DevOps; mantener portabilidad por estándares |
| Monolito | KEEP | escala suficiente; menor complejidad operativa |
| Estructura horizontal models/schemas/routers/services | REDESIGN | límites de dominio débiles |
| Reglas de negocio y pruebas actuales | KEEP como especificación | contienen aprendizaje difícil de reconstruir |
| Filtro tenant ORM | KEEP como defensa adicional | útil, pero insuficiente solo |
| PostgreSQL sin RLS | REMOVE | no ofrece defensa en profundidad |
| RBAC granular | KEEP + REDESIGN | conservar catálogo; añadir scopes y costos separados |
| `scope_query` permisivo | REMOVE | comportamiento fail-open |
| JWT access/refresh | REDESIGN | cookies seguras web; arquitectura extensible OIDC |
| bcrypt | REDESIGN | migración a Argon2id |
| Outbox de correo | KEEP | patrón correcto; generalizar a eventos |
| SSE administrativo | KEEP | apropiado para actualizaciones server→client |
| WebSocket para todo | REMOVE/NO IMPLEMENTAR | reservar para monitor/IoT bidireccional |
| Storage S3 compatible | KEEP | portable y adecuado |
| URLs públicas por defecto | REDESIGN | privado/presigned para documentos sensibles |
| Auditoría ORM | KEEP + REDESIGN | centralizar y hacer append-only |
| 162 migraciones | REMOVE en Next | crear baseline limpia |
| CI actual | KEEP + EXPAND | añadir contract, a11y, RLS, coverage y OpenAPI diff |
| UI actual | KEEP como referencia | reconstruir sobre design system e i18n |
| Entidades hardware futuras | DEFER con contratos | diseñar identidad de dispositivo, no funcionalidad |
| Moodle, móvil, IoT, monitor, debrief | DEFER | no son foundation |

## Regla de migración

Nada se copia por conveniencia. Cada pieza entra en Next solo si tiene:

1. requisito identificado;
2. owner de dominio;
3. contrato/API;
4. threat model cuando sea sensible;
5. pruebas de aceptación;
6. aislamiento tenant verificado;
7. documentación de usuario cuando cambia flujo visible.

