# Análisis de seguridad

## Resumen

El repositorio muestra intención de seguridad superior a la de un MVP típico: CI con SAST, dependency audit, secret scanning y container scan; pruebas de aislamiento; validación de MIME; cuotas; audit log; rate limiting; scrubbing de observabilidad; tokens de activación hasheados y configuración que rechaza secretos débiles.

Sin embargo, **no debe venderse todavía como multi-tenant robusto internacional**. El principal riesgo no es una vulnerabilidad puntual evidente, sino la dependencia de controles de aplicación sobre una superficie de 480 operaciones.

## Hallazgos priorizados

| Severidad | Hallazgo | Evidencia | Acción |
|---|---|---|---|
| Crítica | Sin RLS en PostgreSQL | ausencia de políticas; `core/tenant_scope.py` solo ORM | RLS default-deny + pruebas SQL directas |
| Alta | Tokens access y refresh en `localStorage` | `frontend/src/store/auth.ts`, `api/authToken.ts` | sesión web con cookies HttpOnly/Secure/SameSite; rotación de refresh |
| Alta | Scope owner/all no cerrado | comentario y no-op permisivo en `core/permissions.py::scope_query` | ABAC/scopes explícitos, deny-by-default |
| Alta | Operaciones Core/bulk pueden omitir tenant | múltiples `db.execute()` | repositorios tenant-aware + revisión/test por sentencia |
| Alta | HS256 usa un secreto global | `core/security.py` | mantener solo si hay rotación formal; preferir claves asimétricas para ecosistema distribuido |
| Media | bcrypt en lugar de Argon2id | `core/security.py` | migración oportunista a Argon2id |
| Media | CSP y Permissions-Policy no visibles | middleware de headers | CSP estricta, Permissions-Policy y controles de frontend |
| Media | Auditoría no demostrada como inmutable | listeners ORM y tablas normales | append-only, permisos DB y sellado/exportación |
| Media | Dependencias muy recientes requieren control de compatibilidad | lockfiles y versiones | Renovate/Dependabot con ventanas y tests |
| Media | Archivos públicos pueden exponer metadata/PII según configuración | `S3_PUBLIC_BASE_URL` | objetos privados y URLs firmadas por defecto |

## Aspectos correctos a conservar

- tenant derivado del usuario recargado desde DB;
- refresh token no autoriza endpoints normales;
- impersonación no accede al panel superadmin;
- activación/reset usan tokens opacos almacenados por hash;
- configuración de producción rechaza secretos conocidos/cortos;
- validación de extensión, MIME real, tamaño y path traversal;
- tests dedicados a storage y aislamiento;
- Gitleaks y Trivy en CI.

## Baseline propuesto

- OWASP ASVS 5.0 como catálogo verificable;
- NIST SSDF para proceso;
- threat models STRIDE para Identity, Tenancy, Files, Procurement y Devices;
- matriz `permission + scope + resource ownership`;
- security regression tests por endpoint sensible;
- secretos en gestor externo, rotación y runbook;
- MFA al menos para superadmin antes de producción comercial;
- logs de seguridad con retención diferenciada y sin PII innecesaria.

## Veredicto

`[Seguro]` Hay una base útil de DevSecOps.  
`[Seguro]` La defensa multi-tenant no tiene aún una última barrera en base de datos.  
`[Probable]` Con RLS, auth web endurecida y scopes formales, el diseño compartido puede servir para 100+ instituciones sin una base por tenant.

