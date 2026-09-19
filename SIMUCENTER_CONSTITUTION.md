# Constitución de SimuCenter Next

## 1. Principios no negociables

1. **Tenant isolation is a data invariant.** Nunca depende solo de que el programador recuerde filtrar.
2. **Deny by default.** Ausencia de política, permiso o scope significa denegación.
3. **Domain ownership.** Cada dato y regla pertenece a un dominio claramente nombrado.
4. **API-first.** Web, móvil y dispositivos son clientes; no contienen la autoridad del negocio.
5. **Specifications before implementation.** Ningún agente implementa una feature sin requisitos y aceptación.
6. **Verification over confidence.** “La IA lo probó” no es evidencia; el pipeline reproducible sí.
7. **Evolution without speculation.** Se preparan contratos de extensión, no funcionalidades sin demanda.

## 2. Arquitectura

- monolito modular;
- dependencias entre dominios solo mediante interfaces/casos de uso/eventos aprobados;
- API → application → domain; infrastructure implementa puertos;
- routers sin lógica de negocio;
- tablas de otro dominio no se consultan directamente;
- ADR obligatorio para cambios estructurales.

## 3. Multi-tenancy y autorización

- `tenant_id` obligatorio salvo tablas globales justificadas;
- PostgreSQL RLS y rol de aplicación sin bypass;
- contexto tenant configurado dentro de transacción;
- permisos expresados como `resource.action` más scope;
- precios/costos son permisos independientes;
- superadmin y procesos globales requieren ruta explícita, razón y auditoría;
- cada feature incluye pruebas negativas cross-tenant.

## 4. Seguridad

- ASVS 5.0 baseline;
- Argon2id para nuevas contraseñas;
- cookies HttpOnly/Secure/SameSite para sesiones web;
- MFA de superadmin;
- secretos únicamente por configuración externa;
- uploads privados, validados y servidos por autorización o URL firmada;
- threat model antes de Identity, Files, Procurement, Devices o cambios de tenancy;
- vulnerabilidad crítica/alta explotable bloquea release.

## 5. Datos y migraciones

- migrations append-only después de release; nunca editar una aplicada;
- upgrade y downgrade revisados, aunque rollback productivo prefiera forward fix;
- constraints de DB respaldan invariantes críticas;
- soft delete no sustituye estados de negocio;
- export/import de tenant es una capacidad de plataforma probada;
- auditoría y outbox son append-only.

## 6. API y errores

- OpenAPI versionado y validado por diff;
- códigos de error estables y request ID;
- paginación obligatoria en colecciones no acotadas;
- idempotency key para mutaciones reintentables con efectos;
- compatibilidad hacia atrás o versión nueva para breaking changes.

## 7. Frontend

- TypeScript estricto;
- textos mediante i18n, nunca literales de producto dispersos;
- design tokens y componentes accesibles compartidos;
- permisos del frontend solo controlan UX; el backend siempre aplica autorización;
- WCAG 2.2 AA y navegación por teclado en aceptación;
- lógica compartible fuera de componentes visuales.

## 8. Testing

Cada cambio ejecuta pruebas proporcionales. Son obligatorias:

- unitarias de dominio;
- integración con PostgreSQL real;
- contratos API;
- autorización y aislamiento tenant;
- migraciones y constraints;
- E2E de journeys críticos;
- accesibilidad automatizada más revisión manual dirigida;
- seguridad, dependencias, secretos y contenedores.

Cada criterio `AC-*` debe apuntar a al menos una prueba. Los bugs de seguridad o regresión reciben prueba antes del cierre.

## 9. Observabilidad y operación

- request ID atraviesa API, DB, jobs, eventos y notificaciones;
- no registrar tokens, passwords, secretos ni cuerpos sensibles;
- health, readiness y métricas separadas;
- alertas con owner y runbook;
- restore drills periódicos;
- SLO medido antes de ofrecer SLA.

## 10. Git y desarrollo con IA

- `main` protegida, sin push directo;
- una feature branch por cambio y PR obligatoria;
- agentes leen constitución, ADR y spec antes de editar;
- no modifican fuera del scope autorizado;
- diffs pequeños y revisables;
- ninguna decisión arquitectónica fundamental se delega implícitamente a IA;
- documentación de usuario y técnica cambia en el mismo PR cuando corresponde.

## 11. Definition of Done

Una feature está terminada solo si:

1. requisitos y aceptación están trazados;
2. threat model actualizado si aplica;
3. código respeta fronteras;
4. permisos y scopes están definidos;
5. pruebas pasan en CI;
6. aislamiento tenant está demostrado;
7. observabilidad y auditoría son suficientes;
8. i18n y accesibilidad cumplen;
9. documentación está actualizada;
10. no existe deuda crítica conocida sin aceptación explícita.

## 12. Excepciones

Toda excepción requiere ADR con motivo, riesgo, responsable, fecha de expiración y plan de eliminación. “Es más rápido” no es justificación suficiente.

