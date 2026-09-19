# Modelo de datos de <FEATURE_NAME>

Referencia: `<SPEC_ID>`

## Entidades

| Entidad | Propósito | Datos principales | Clasificación |
|---|---|---|---|
| `<ENTITY>` | `<PURPOSE>` | `<FIELDS>` | `<CLASSIFICATION>` |

## Ownership

- Entidad: `<ENTITY>`
- Owner de dominio: `<DOMAIN>`
- Regla de ownership: `<OWNERSHIP_RULE>`

## tenant_id

- Entidades tenant-aware: `<ENTITIES_OR_NONE>`
- Obligatorio: `<YES_OR_JUSTIFIED_EXCEPTION>`
- Fuente autoritativa del tenant: `<SOURCE>`

## Relaciones

- `<ENTITY_A>` `<RELATIONSHIP>` `<ENTITY_B>` — `<CARDINALITY_AND_RULE>`

## Constraints

- `<CONSTRAINT_OR_NONE>`

## Índices

- `<INDEX_AND_PURPOSE_OR_NONE>`

## RLS

- Tabla: `<TABLE>`
- Política default-deny: `<POLICY>`
- Rol de aplicación sin bypass: `<ROLE>`
- Contexto tenant dentro de transacción: `<MECHANISM>`

## Auditoría

- Eventos auditados: `<EVENTS_OR_NONE>`
- Actor, tenant, acción, recurso, before/after permitido, IP y request ID:
  `<REQUIREMENT>`
- Append-only: `<REQUIREMENT>`

## Retención

- Datos: `<RETENTION_POLICY>`
- Auditoría: `<RETENTION_POLICY>`
- Archivos, logs y backups: `<RETENTION_POLICY>`

## Migraciones

- Migración nueva: `<MIGRATION_IDENTIFIER_OR_NONE>`
- Upgrade: `<UPGRADE_PLAN>`
- Downgrade o forward fix: `<ROLLBACK_PLAN>`
- Verificación de constraints y RLS: `<VERIFICATION>`
