# ADR-0002: PostgreSQL compartido con aislamiento tenant mediante RLS

- **Estado:** Accepted
- **Fecha:** 2026-09-19

## Contexto

SimuCenter Next es un SaaS multi-tenant cuyo aislamiento debe ser una
invariante de datos y operar con deny by default. Los requisitos base definen
PostgreSQL compartido, ownership explícito, RLS para tablas tenant y capacidad
de exportar y restaurar un tenant individual.

`[Seguro]` El legacy usa `tenant_id`, filtros automáticos del ORM, un guard de
escritura e integridad tenant en algunas relaciones. Sin embargo, no contiene
PostgreSQL Row-Level Security. SQL directo, operaciones Core o bulk, jobs,
scripts y conexiones fuera de la sesión ORM pueden omitir el filtro tenant.

`[Seguro]` Un backup físico de una base compartida no resuelve por sí mismo la
restauración selectiva de un tenant. Una exportación actual tampoco permite
recuperar un estado anterior sin una copia histórica. La recuperación histórica
individual requiere copias lógicas históricas o restaurar un backup en un
entorno aislado y extraer el tenant, manteniendo consistencia con sus archivos.

## Problema que resuelve

Se necesita almacenar datos de múltiples instituciones con una barrera de
aislamiento en la base de datos, además de los controles de aplicación. El
diseño debe impedir accesos y referencias cross-tenant incluso ante consultas
mal filtradas, definir cómo operan procesos globales y permitir la portabilidad
y restauración lógica de un tenant.

## Decisión propuesta

Adoptar una base PostgreSQL compartida con las siguientes reglas:

1. Todo recurso tenant tiene `tenant_id` obligatorio y no nulo. Una tabla
   global exige justificación explícita, owner y revisión de seguridad.
2. Toda tabla tenant habilita PostgreSQL RLS con políticas default-deny. Las
   políticas usan `USING` para controlar la visibilidad de filas existentes y
   `WITH CHECK` para validar los valores resultantes de `INSERT` y `UPDATE`.
   La ausencia de contexto o política aplicable no devuelve ni modifica filas.
3. Se aplica `FORCE ROW LEVEL SECURITY` cuando corresponda para que el owner de
   la tabla también quede sujeto a las políticas durante la operación normal.
   `FORCE ROW LEVEL SECURITY` no restringe a superusuarios ni a roles con
   `BYPASSRLS`; esos privilegios deben quedar fuera del proceso normal de
   aplicación.
4. El rol ordinario de aplicación no es propietario de tablas, no es
   superusuario y no tiene `BYPASSRLS`. No puede asumir roles privilegiados,
   modificar el esquema, desactivar RLS ni ejecutar `TRUNCATE`.
5. El ownership y las migraciones usan un rol separado, con credenciales que no
   están disponibles para el proceso normal de aplicación.
6. El backend deriva el tenant de la identidad autenticada y sus membresías
   autorizadas; nunca confía únicamente en un `tenant_id` recibido del cliente.
   Para jobs, el tenant se establece desde un contexto de trabajo validado y
   autorizado.
7. El contexto tenant se establece dentro de cada transacción mediante una
   variable o mecanismo local a la transacción configurado por la capa de
   persistencia. Debe limpiarse automáticamente al terminar la transacción y
   fallar cerrado cuando esté ausente o sea inválido.
8. RLS basado en una variable de sesión protege frente a consultas mal
   filtradas, pero no garantiza aislamiento ante un backend comprometido o una
   ejecución SQL arbitraria capaz de cambiar esa variable. RLS complementa la
   autenticación, la autorización y la prevención de inyección SQL; no las
   sustituye.
9. Las relaciones entre recursos tenant usan FKs compuestas o constraints
   equivalentes que incluyen `tenant_id` e impiden referencias cross-tenant.
   PostgreSQL puede omitir RLS durante comprobaciones de integridad referencial,
   incluidas claves únicas, primarias y foráneas; por ello las constraints
   compuestas siguen siendo obligatorias, pero no se tratan como mecanismo de
   confidencialidad.
10. Las rutas superadmin y los procesos globales usan roles o rutas explícitas,
   de mínimo privilegio, con razón, autorización y auditoría. No reutilizan de
   forma implícita la conexión ordinaria de aplicación.
11. El aislamiento se prueba para `SELECT`, `INSERT`, `UPDATE`, `DELETE` y
   asociaciones usando tanto ORM como SQL directo. Las pruebas negativas
   cross-tenant son obligatorias e incluyen contexto ausente e inválido y la
   reutilización de conexiones entre tenants después de `COMMIT` y `ROLLBACK`,
   usando el rol real de aplicación. También comprueban que `INSERT` y `UPDATE`
   no pueden crear una fila para otro tenant ni cambiar el `tenant_id` de una
   fila existente, y que errores de unicidad, FK y otras constraints sensibles
   no permiten enumerar registros de otro tenant.
12. `tenant_id` no puede modificarse mediante operaciones ordinarias del
   producto. Cualquier transferencia excepcional de datos entre tenants exige
   un procedimiento administrativo separado, autorizado, auditado y probado.
13. Los filtros tenant de la aplicación pueden mantenerse como defensa
   adicional, pero no sustituyen RLS ni las constraints de integridad.
14. La exportación actual e importación/restauración individual de un tenant se
    implementan como una capacidad lógica, versionada y probada, separada del
    backup y restore físico de PostgreSQL. Una exportación actual no recupera un
    estado anterior; la recuperación histórica individual requiere copias
    lógicas históricas o restaurar un backup en un entorno aislado y extraer el
    tenant, incluyendo consistencia con sus archivos. La estrategia concreta,
    el RPO y el RTO se definirán en una spec posterior.

`[No determinado]` Los nombres concretos de roles, la variable de contexto
tenant, el inventario de tablas globales, el formato de exportación, la
estrategia histórica, el RPO y el RTO se definirán en specs y diseño de datos
aprobados.

## Alternativas consideradas

| Alternativa | Evaluación | Resultado |
|---|---|---|
| Base compartida con `tenant_id` y RLS | Ofrece eficiencia operativa y una barrera de DB compatible con el objetivo inicial, siempre que roles, políticas, constraints y pruebas sean estrictos. | Propuesta |
| Base separada por tenant | Puede ofrecer aislamiento lógico y administrativo entre bases, pero varias bases pueden compartir servidor y recursos. El aislamiento de infraestructura requiere decisiones adicionales. También multiplica aprovisionamiento, migraciones, pooling, observabilidad y costo operativo para la escala inicial. | Diferida; posible para requisitos futuros específicos |
| Schema por tenant | Separa namespaces, pero complica migraciones, búsqueda, pooling y operación; tampoco equivale por sí solo a aislamiento de privilegios. | Descartada para el diseño base |
| Filtrado exclusivamente en la aplicación | Es simple al inicio, pero depende de que cada consulta, job y script filtre correctamente; el legacy demuestra esta superficie de riesgo. | Rechazada |

## Consecuencias positivas

- Defensa en profundidad frente a consultas que omiten filtros tenant.
- Operación, migraciones y observabilidad centralizadas para la escala inicial.
- Constraints de base respaldan la integridad de relaciones tenant.
- El mismo conjunto de políticas protege ORM y SQL directo bajo el rol normal.
- La aplicación conserva un modelo de datos compartido portable.
- La exportación actual y la restauración lógica atienden la portabilidad
  individual sin confundirlas con recuperación histórica ni con recuperación
  física de toda la plataforma.

## Consecuencias negativas y costos

- Cada tabla y operación tenant requiere políticas, constraints, índices y
  pruebas específicas.
- RLS aumenta la complejidad de debugging, migraciones, jobs y tareas globales.
- Una política incorrecta puede bloquear operaciones válidas o exponer datos.
- El contexto transaccional exige disciplina en pooling y límites de
  transacción.
- Los índices suelen necesitar `tenant_id`, con costo adicional de escritura y
  almacenamiento.
- Exportar y restaurar un tenant con relaciones, archivos y eventos requiere un
  producto lógico adicional al backup físico.
- La recuperación histórica individual requiere conservar copias lógicas
  históricas o restaurar backups en entornos aislados, y comprobar consistencia
  con los archivos.
- Todos los tenants comparten capacidad de cómputo y almacenamiento, por lo que
  deben controlarse noisy neighbors y cuotas.

## Riesgos

- Una tabla tenant nueva podría quedar sin RLS o sin `FORCE ROW LEVEL SECURITY`
  cuando corresponda.
- Un rol operativo con privilegios excesivos podría evadir las políticas.
- El contexto tenant podría configurarse fuera de la transacción o reutilizarse
  incorrectamente en una conexión del pool.
- Jobs globales podrían convertirse en una vía de acceso no auditada.
- FKs simples podrían permitir asociaciones cross-tenant aunque RLS oculte las
  filas relacionadas.
- Las comprobaciones de integridad referencial que omiten RLS podrían exponer
  canales de información mediante diferencias de existencia, unicidad, claves
  foráneas u otras constraints sensibles.
- Una política `USING` o `WITH CHECK` incompleta podría permitir visibilidad,
  inserción o actualización cross-tenant.
- La API podría propagar errores crudos de PostgreSQL y permitir inferir la
  existencia de registros de otro tenant.
- Las políticas RLS podrían degradar planes de consulta si no existen índices
  adecuados.
- Una exportación incompleta podría aparentar un restore correcto sin incluir
  archivos, auditoría o dependencias.
- Confundir una exportación actual con una copia histórica podría impedir
  recuperar el estado solicitado.
- `[No determinado]` Aún no están definidos la estrategia histórica, RPO/RTO,
  formato de exportación, orden de dependencias ni tratamiento exacto de
  auditoría durante un restore.

## Restricciones que impone

- No se crea una tabla tenant sin `tenant_id NOT NULL`, RLS default-deny,
  constraints e índices revisados.
- Toda política tenant debe usar `USING` para visibilidad y `WITH CHECK` para
  validar `INSERT` y `UPDATE`.
- `tenant_id` es inmutable en las operaciones ordinarias del producto. Las
  transferencias excepcionales usan un procedimiento administrativo separado,
  autorizado, auditado y probado.
- El rol ordinario de aplicación no puede ser propietario de tablas, asumir
  roles privilegiados, modificar el esquema, desactivar RLS, ejecutar
  `TRUNCATE`, ser superusuario o tener `BYPASSRLS`.
- Ownership y migraciones usan un rol separado cuyas credenciales no están
  disponibles al proceso normal de aplicación.
- Toda operación tenant ocurre dentro de una transacción con contexto tenant
  válido.
- No se permite una ruta global implícita; debe estar autorizada, justificada y
  auditada.
- Las migraciones deben verificar políticas, roles, constraints, upgrade y
  downgrade o estrategia de forward fix.
- Las pruebas de integración usan PostgreSQL real e incluyen ORM y SQL directo.
- Las pruebas cubren `INSERT` y `UPDATE` cross-tenant, intentos de cambiar
  `tenant_id`, y enumeración mediante errores de unicidad, FK y otras
  constraints sensibles.
- La API no devuelve errores crudos de PostgreSQL ni detalles que permitan
  inferir la existencia de registros de otro tenant.
- Ningún filtro ORM se acepta como control único de aislamiento.
- Backup físico, exportación actual, recuperación histórica y
  exportación/restauración individual se diseñan, operan y prueban como
  capacidades diferentes. La estrategia concreta, RPO y RTO quedan para una
  spec posterior.

## Criterios que obligarían a reconsiderarlo

- Regulación, contrato o residencia de datos exige aislamiento físico por
  tenant.
- Un tenant requiere claves, mantenimiento, versión o ciclo de restore
  independientes.
- La carga de un tenant afecta de forma sostenida los SLO de los demás y no se
  corrige con cuotas, índices, particionamiento o aislamiento de recursos.
- El volumen o número de tenants vuelve inmanejables las políticas, índices o
  mantenimiento de la base compartida.
- Los objetivos de RPO/RTO o restore individual no pueden cumplirse con la
  arquitectura compartida.
- Una limitación comprobada de PostgreSQL impide aplicar RLS a una capacidad
  requerida con seguridad y rendimiento aceptables.

## Referencias

- `SIMUCENTER_CONSTITUTION.md`: secciones 1, 3, 5, 8, 9, 11 y 12.
- `docs/legacy-analysis/01_CURRENT_ARCHITECTURE.md`: multi-tenancy actual y
  ausencia de RLS.
- `docs/legacy-analysis/04_DATABASE_ANALYSIS.md`: riesgos de DB, diseño
  recomendado y pruebas obligatorias.
- `docs/legacy-analysis/06_SECURITY_ANALYSIS.md`: ausencia de RLS, operaciones
  Core/bulk y baseline de seguridad.
- `docs/legacy-analysis/08_KEEP_REDESIGN_REMOVE.md`: filtro ORM como defensa
  adicional y eliminación del diseño sin RLS.
- `docs/legacy-analysis/09_SIMUCENTER_NEXT_REQUIREMENTS.md`:
  `REQ-SEC-001`, `REQ-SEC-002`, `REQ-DATA-001`, `REQ-DATA-002`,
  `REQ-DATA-003`, `REQ-NFR-004` y `REQ-NFR-006`.

## Aprobación pendiente

La existencia de este ADR no constituye aprobación.

- **Estado de aprobación:** Aprobado
- **Aprobado por:** Product Owner
- **Fecha:** 2026-09-19
- **Comentario o referencia:** Aprobación explícita durante la revisión SDD.
