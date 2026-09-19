# Análisis de base de datos

## Estado observado

La documentación incluida reporta 86 tablas, 85 con `tenant_id` y 59 con `deleted_at` en un corte anterior. El paquete actual contiene 162 migraciones Alembic, por lo que ese inventario debe regenerarse contra `Base.metadata` y el esquema migrado antes de considerarlo definitivo.

No se pudo levantar PostgreSQL ni ejecutar Alembic porque el paquete fue sanitizado (`REDACTED_FOR_SHARING`) y el entorno de análisis no tenía dependencias instaladas. La sintaxis de los 465 archivos Python sí compiló correctamente.

## Fortalezas

- UUID como identificadores principales.
- `tenant_id` extendido por casi todo el modelo.
- numerosos índices por tenant y constraints de integridad;
- FKs compuestas tenant/recurso en módulos endurecidos;
- soft delete normalizado progresivamente con `deleted_at`;
- migraciones con `upgrade`/`downgrade` y verificación `alembic check` en CI;
- pruebas específicas de integridad, concurrencia y aislamiento.

## Riesgos críticos

### 1. Ausencia de RLS

No se encontraron `ENABLE ROW LEVEL SECURITY`, `CREATE POLICY` ni políticas equivalentes. Un defecto en SQL directo, Core SQL, job o script puede cruzar tenants aunque el ORM normal esté protegido.

### 2. Operaciones Core/bulk fuera del guard ORM

Existen `db.execute()` sobre tablas de asociación y actualizaciones masivas en cursos, usuarios, tickets e inventario. El listener `before_flush` protege entidades ORM, no garantiza el alcance de cada sentencia Core. Cada operación debe incorporar `tenant_id` en el `WHERE` y tener test negativo cross-tenant.

### 3. Cadena de migraciones excesivamente fragmentada

162 revisiones antes de tener datos productivos relevantes elevan el costo de bootstrap, revisión y downgrade. Para Next conviene una línea base limpia; no trasladar esta cadena completa.

### 4. Soft delete no uniforme

La migración progresiva deja coexistencia entre `activo`, `deleted_at` y estados de negocio. En Next deben distinguirse explícitamente vigencia, lifecycle de negocio y eliminación lógica.

### 5. Restore de tenant no resuelto

El backup físico compartido no sirve para restaurar un cliente individual. Se necesita un formato exportable con manifest, versión de esquema, checksums, archivos y orden de dependencias.

## Diseño de datos recomendado para Next

1. PostgreSQL compartido con `tenant_id NOT NULL` para recursos tenant.
2. RLS con default-deny y `FORCE ROW LEVEL SECURITY` donde aplique.
3. Contexto tenant transaccional (`SET LOCAL`) configurado por la capa de persistencia.
4. Rol de aplicación sin `BYPASSRLS`; rol separado y auditado para migraciones/jobs globales.
5. FKs compuestas o constraints equivalentes que impidan referencias cross-tenant.
6. Índices empezando por `tenant_id` en consultas de alta cardinalidad.
7. `outbox_events` y `audit_events` append-only.
8. baseline inicial limpia y política de migraciones forward-compatible.
9. export/import de tenant probado automáticamente.

## Pruebas obligatorias

- aislamiento para SELECT/INSERT/UPDATE/DELETE y asociaciones;
- pruebas RLS con SQL directo, no solo ORM;
- migración desde base vacía y desde versión anterior;
- `alembic check` sin drift;
- export → borrar tenant → importar → comparar conteos/checksums;
- restore con archivos faltantes o manifest incompatible;
- concurrencia en inventario, agenda y evaluación.

