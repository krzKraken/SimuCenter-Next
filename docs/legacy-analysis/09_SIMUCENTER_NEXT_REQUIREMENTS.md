# Requisitos base de SimuCenter Next

## Producto

- **REQ-PROD-001:** SaaS multi-tenant para centros de simulación médica.
- **REQ-PROD-002:** soportar múltiples roles por usuario.
- **REQ-PROD-003:** dashboard orientado a trabajo y responsabilidad.
- **REQ-PROD-004:** planes mediante entitlements configurables.
- **REQ-PROD-005:** español e inglés desde el primer release.
- **REQ-PROD-006:** WCAG 2.2 AA como Definition of Done.

## Seguridad y privacidad

- **REQ-SEC-001:** cero acceso cross-tenant en aplicación, DB, cache, archivos y eventos.
- **REQ-SEC-002:** RLS default-deny para tablas tenant.
- **REQ-SEC-003:** permiso, scope y ownership por operación.
- **REQ-SEC-004:** costos de adquisición protegidos por permisos independientes.
- **REQ-SEC-005:** audit event con actor, tenant, acción, recurso, before/after permitido, IP y request ID.
- **REQ-SEC-006:** secretos, tokens y credenciales nunca se auditan.
- **REQ-SEC-007:** MFA obligatorio para superadmin antes de comercialización.
- **REQ-SEC-008:** baseline técnico OWASP ASVS 5.0.

## Datos

- **REQ-DATA-001:** PostgreSQL compartido con ownership explícito.
- **REQ-DATA-002:** exportación completa y portable de un tenant.
- **REQ-DATA-003:** importación/restauración individual versionada.
- **REQ-DATA-004:** gracia de 6 meses tras terminación, configurable por contrato/jurisdicción.
- **REQ-DATA-005:** políticas separadas para datos, auditoría, logs, archivos y backups.
- **REQ-DATA-006:** clasificación de PII, costos, académico y operacional.

## Arquitectura

- **REQ-ARCH-001:** monolito modular por dominios.
- **REQ-ARCH-002:** API-first con OpenAPI versionado.
- **REQ-ARCH-003:** HTTP para CRUD, SSE para notificaciones y WebSocket/MQTT solo donde sea bidireccional.
- **REQ-ARCH-004:** outbox transaccional para side effects.
- **REQ-ARCH-005:** jobs fuera del request para email, exportaciones, PDFs y procesamiento pesado.
- **REQ-ARCH-006:** despliegue portable con Docker, PostgreSQL, Redis y S3 compatible.

## Calidad y operación

- **REQ-NFR-001:** objetivo interno inicial de disponibilidad 99,9 %, medido antes de prometer SLA.
- **REQ-NFR-002:** p95 de operaciones estándar < 300 ms bajo carga objetivo acordada.
- **REQ-NFR-003:** objetivo inicial de 100 instituciones y decenas de miles de usuarios registrados sin rediseño estructural.
- **REQ-NFR-004:** RPO/RTO definidos y ensayados antes del primer contrato con SLA.
- **REQ-NFR-005:** logs, métricas, trazas, errores, auditoría, health y alertas correlacionados por request ID.
- **REQ-NFR-006:** CI bloquea lint, tipos, unit, integration, tenant isolation, migrations, SAST, dependencies, secrets, containers, a11y y contratos.

## Funcional foundation

- **REQ-FND-001:** Organizations, subscriptions, entitlements y settings.
- **REQ-FND-002:** Identity, local credentials, sessions, roles, permissions y scopes.
- **REQ-FND-003:** Files, audit, notifications, localization y observability.
- **REQ-FND-004:** primer slice de negocio: Equipment end-to-end.

## Fuera del primer alcance

Apps nativas, Moodle/LTI, SSO empresarial, monitor, IoT RCP, desfibrilador, debrief appliance y BI avanzada quedan DEFERRED; el diseño no debe impedirlos.

