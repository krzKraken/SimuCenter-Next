# Deuda técnica priorizada

## P0 — antes de incorporar clientes reales

1. Implementar RLS y pruebas SQL directas de aislamiento.
2. Resolver scopes de autorización; eliminar el fallback permisivo de `scope_query`.
3. Sacar tokens de `localStorage` para la aplicación web.
4. Probar backup completo y construir export/import por tenant.
5. Definir branch protection y prohibir push directo a `main`.
6. Crear inventario de datos y clasificación de campos sensibles/costos.

## P1 — foundation de Next

1. Reorganizar por dominios, no por tipo técnico.
2. Introducir casos de uso y repositorios tenant-aware.
3. Baseline Alembic limpia.
4. Contratos OpenAPI versionados y clientes generados.
5. Entitlements separados de nombres de plan.
6. Auditoría append-only y eventos outbox genéricos.
7. i18n español/inglés desde el primer componente.
8. Design system sobre Radix/shadcn y WCAG 2.2 AA en DoD.
9. Política de testing con métricas y ownership.
10. SLO, RPO y RTO medidos.

## P2 — tras validar primeros clientes

1. PWA antes de apps nativas.
2. Integración Moodle/LTI cuando exista demanda contractual.
3. OpenTelemetry y trazas distribuidas si la operación lo justifica.
4. Data mart/warehouse para analytics avanzada.
5. SSO OIDC/SAML y SCIM para Enterprise.

## Métricas de deuda observadas

| Indicador | Valor |
|---|---:|
| Operaciones API decoradas | 480 |
| Routers backend | 45 |
| Revisiones Alembic | 162 |
| Archivos de pruebas backend | 102 |
| Pruebas frontend/E2E detectadas | 6 archivos |
| Páginas TSX | 229 |
| Coincidencias TODO/FIXME/HACK/pendiente/no-op/futuro | 281 |
| Archivo router más grande | 1.070 líneas |

Estas cifras no prueban mala calidad por sí solas. Sí muestran que el costo de seguir añadiendo funciones sin fronteras crecerá más rápido que el producto.

## Antiobjetivos

- no microservicios;
- no Kubernetes;
- no reescribir todo en otro lenguaje;
- no migrar 162 revisiones a Next;
- no construir móvil, IoT o debrief antes del core;
- no usar documentación generada como sustituto de pruebas ejecutables.

