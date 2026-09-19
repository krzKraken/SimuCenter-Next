# Inventario de funcionalidades

## Superficies de usuario

- Portal público: login, activación, recuperación y restablecimiento.
- Superadmin: instituciones, usuarios globales, plantillas de roles, auditoría y métricas.
- Portal institucional: dashboard condicionado por responsabilidades y módulos operativos.
- Portal estudiante: inicio, simulaciones, preparación, evaluaciones, competencias e historial.

## Funciones implementadas por área

### Plataforma

- autenticación JWT con access/refresh;
- revocación de refresh token en Redis;
- impersonación superadmin con restricciones;
- tenant suspendido, vencido o eliminado;
- RBAC granular y roles múltiples;
- auditoría automática de cambios ORM;
- SSE autenticado y aislado;
- cuotas de almacenamiento, campus, salas y usuarios;
- almacenamiento local/S3 y ciclo pending/confirmed/purge;
- correo transaccional mediante outbox;
- push FCM opcional;
- healthcheck de base y storage;
- logs estructurados, request ID y Sentry.

### Negocio

- proveedores, contactos y categorías;
- marcas, modelos, protocolos, accesorios y documentos;
- equipos, estados, mantenimiento y cronogramas;
- ubicaciones jerárquicas y salas;
- contratos;
- insumos, ubicaciones de inventario, ingresos, egresos, ajustes y kardex;
- solicitudes y órdenes de compra, proformas, subórdenes y backorders;
- tickets/bitácoras de equipo e infraestructura;
- escenarios clínicos con paciente, estados, participantes y recursos;
- solicitudes de simulación, conflictos, agendamiento y preparación;
- sesiones, asistencia, actividades, población, tandas y cobertura;
- estructura académica, estudiantes y matrículas;
- competencias, instrumentos versionados, evaluación y resultados;
- portal y outcomes académicos;
- reportes de gastos, inventario y compras.

## Funciones incompletas o preparadas

- `scope_query()` declara que el alcance owner/ver-todas está “PREPARADO” y mantiene comportamiento permisivo cuando el permiso no existe: deuda funcional y de autorización.
- FCM está cableado pero deshabilitado por defecto.
- OpenTelemetry no aparece implementado; la observabilidad es logs/Sentry.
- Moodle/LTI, apps móviles, IoT, monitor, desfibrilador y debrief no están implementados.
- No existe mecanismo de exportación/restauración de un tenant como producto.
- No existe sistema general de entitlements; `tenant.plan` y cuotas están en el modelo actual.
- Internacionalización completa no está implantada; la UI contiene texto español directo.
- WCAG 2.2 AA no está demostrado por una suite automatizada integral.

## Matriz KEEP / REDESIGN / DEFER

| Clasificación | Elementos |
|---|---|
| KEEP | reglas de negocio verificadas, pruebas de aislamiento, esquemas de inventario/compras/simulación, outbox, cuotas, file lifecycle, auditoría conceptual |
| REDESIGN | límites de dominio, tenancy con RLS, auth web, permisos con scope, entitlements, export tenant, eventos/outbox genérico, API contracts |
| MERGE | patrones duplicados de tickets/bitácoras; clientes API repetitivos; utilidades de tablas/formularios |
| REMOVE | campos y compatibilidad legacy cuando la equivalencia esté probada; planes hardcodeados; rutas duplicadas obsoletas |
| DEFER | móvil nativo, Moodle/LTI, IoT, monitor, desfibrilador, debrief appliance, BI avanzado, SSO empresarial |
| UNKNOWN | reglas comerciales exactas de planes, retención legal por país, SLA contractual, volumen real por tenant |

