# Modelo de datos de Platform Foundation

## Metadata

- **Referencia:** `SPEC-001`
- **Versión:** `1.0.0`
- **Estado:** `Approved`
- **Responsable:** `Product Owner`
- **Fecha:** `2026-09-20`
- **Última actualización:** `2026-09-20`

## Decisión de aplicabilidad

**Resultado: No aplica.** El alcance aprobado de SPEC-001 define una foundation
ejecutable, stateless y verificable, pero no crea ni procesa identidades,
sesiones, usuarios, roles, organizaciones, tenants ni datos de negocio. Por
tanto, no existe un modelo de datos persistente que diseñar en esta spec.

Crear un modelo vacío o entidades técnicas ficticias anticiparía decisiones no
autorizadas y podría confundirse con persistencia de aplicación. La
configuración externa, los logs, las métricas, los reportes de rendimiento,
los snapshots, la evidencia de CI y los archivos versionados del repositorio
son artefactos operativos o de ingeniería; no constituyen entidades
persistentes de dominio de esta spec.

No hay ninguna entidad. No hay ninguna tabla o colección persistente. No hay
ninguna relación, ningún índice, ninguna migración, ningún `tenant_id`, ninguna política RLS, ninguna auditoría de dominio ni ninguna política de retención de datos de dominio. El request ID es un identificador de
correlación de una solicitud, no una identidad persistente. Las traducciones y
los design tokens son recursos versionados de la interfaz, no entidades de
dominio. Ninguna base de datos es una dependencia obligatoria de readiness en
SPEC-001.

## Entidades

**No aplicable:** no se definen entidades porque la foundation no persiste
recursos de dominio.

## Ownership

**No aplicable:** no hay entidades ni reglas de ownership de dominio.

## tenant_id

**No aplicable:** SPEC-001 no crea ni procesa recursos tenant y no define
`tenant_id`.

## Relaciones

**No aplicable:** no existen relaciones entre entidades persistentes.

## Constraints

**No aplicable:** no existen tablas ni invariantes de persistencia que
respaldar con constraints.

## Índices

**No aplicable:** no existen tablas, colecciones ni consultas persistentes que
requieran índices.

## RLS

**No aplicable:** no existen tablas tenant, contexto tenant ni políticas
PostgreSQL RLS en esta spec. ADR-0002 es una decisión futura condicionada para
cuando una spec posterior introduzca datos tenant; no se implementa aquí.

## Auditoría

**No aplicable:** no existe auditoría de dominio, actor, recurso, tenant ni
before/after. Los logs operativos y la evidencia de CI no son auditoría de
dominio ni tablas de aplicación.

## Retención

**No aplicable:** no existe una política de retención de datos de dominio. La
retención de logs, métricas, reportes, snapshots o evidencia operativa se
decidirá en sus documentos de seguridad u operación cuando corresponda; no se
convierte por ello en persistencia de aplicación de SPEC-001.

## Migraciones

**No aplicable:** no hay esquema persistente, tablas ni migraciones de
aplicación que crear, actualizar o revertir.

## Condición de reapertura

Si durante el plan o la implementación aparece cualquier necesidad de
persistencia, tabla, entidad, identidad, sesión, usuario, organización,
tenant o dato de negocio:

1. se detiene ese trabajo;
2. se actualizan y aprueban `spec.md`, `acceptance.md`, `security.md` y
   `data-model.md`;
3. se aplica ADR-0002 cuando existan datos tenant;
4. se diseñan ownership, `tenant_id`, transacciones, constraints, índices,
   RLS, auditoría, retención y migraciones antes de implementar.

## Trazabilidad

- `spec.md`, **Alcance incluido**, **Reglas de negocio**, **Casos límite** y
  **Fuera de alcance** (`Organizations y tenants`, `Tablas tenant y políticas
  RLS`, identidad, sesiones y datos de negocio) excluyen la persistencia de
  dominio de SPEC-001.
- `spec.md`: `REQ-001-001`, `REQ-001-002` y `REQ-001-017` mantienen la
  foundation sin autenticación, tenancy, negocio y con procesamiento
  stateless; `REQ-001-018` y `REQ-001-019` tratan la persistencia solo como
  condición futura; `REQ-001-025` se refiere al catálogo, no a entidades.
- `AC-001-035` y `AC-001-037` registran que las métricas o reportes de
  persistencia no aplican mientras SPEC-001 no incorpore persistencia.
- `SEC-001-032` exige demostrar la ausencia de login, sesiones, usuarios,
  roles, tenants, tablas/RLS tenant y datos de negocio.
- `THREAT-001-023` cubre la introducción accidental de ese alcance;
  `THREAT-001-024` y `THREAT-001-025` son amenazas futuras no aplicables en
  esta spec.
- La sección **Aislamiento tenant** de `security.md` declara `tenant_id`, RLS,
  contexto transaccional y pruebas cross-tenant no aplicables.
- `docs/architecture/adr/ADR-0002-shared-postgresql-rls.md` es una decisión
  futura condicionada para datos tenant; no autoriza persistencia en
  SPEC-001 ni se implementa mediante este documento.

## Aprobación de aplicabilidad del modelo de datos

- **Estado:** `Aprobado`
- **Aprobado por:** `Product Owner`
- **Fecha:** `2026-09-20`
- **Comentario o referencia:** `Aprobación explícita de la no aplicabilidad del modelo de datos de SPEC-001 Platform Foundation durante la revisión SDD.`

La existencia de este archivo por sí sola no constituye aprobación; esta versión
sí cuenta con la aprobación explícita registrada en el bloque anterior.
