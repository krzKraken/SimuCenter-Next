# Instrucciones para agentes de SimuCenter Next

Estas instrucciones aplican a todo agente que lea, diseñe, implemente, pruebe,
revise o documente cambios en este repositorio. La Constitución del proyecto es
la autoridad superior para las decisiones arquitectónicas y de seguridad.

## Orden obligatorio de lectura

La lectura obligatoria depende de la etapa solicitada. La ausencia de
documentos de etapas posteriores es normal y no bloquea las etapas anteriores:

1. **Scaffolding del repositorio:** `AGENTS.md`,
   `SIMUCENTER_CONSTITUTION.md` y los ADR relevantes si existen.
2. **Redacción de una especificación:** los documentos anteriores, el contexto
   legacy relevante y `spec.md` si ya existe.
3. **Redacción de `acceptance.md` y `security.md`:** `spec.md` debe existir y
   estar aprobado.
4. **Creación de `plan.md`:** `spec.md`, `acceptance.md` y `security.md` deben
   existir y estar aprobados.
5. **Creación de `tasks.md`:** `plan.md` debe existir y estar aprobado.
6. **Implementación:** deben existir y estar aprobados `spec.md`,
   `acceptance.md`, `security.md`, `plan.md` y `tasks.md`.

El agente solo debe detenerse cuando falte un documento obligatorio para la
etapa solicitada o cuando no pueda determinar qué etapa está autorizada. Los
documentos de `docs/legacy-analysis/` son referencia de contexto cuando la
tarea lo requiera; no sustituyen la especificación activa.

## Specification-Driven Development

- No implementar una funcionalidad sin una especificación aprobada.
- No crear un plan antes de que los requisitos estén aprobados.
- No crear tareas antes de que el plan esté aprobado.
- No cambiar requisitos silenciosamente durante la implementación. Todo cambio
  requiere actualizar la especificación y su aprobación correspondiente.
- Detenerse y preguntar cuando exista una ambigüedad material que pueda cambiar
  el comportamiento, la seguridad, el alcance, la arquitectura o la aceptación.
- Relacionar requisitos, criterios de aceptación, tareas y pruebas mediante IDs
  estables. Cada criterio `AC-*` debe apuntar al menos a una prueba.
- Mantener el alcance autorizado de la especificación activa; una decisión
  arquitectónica estructural requiere un ADR.

## Límites del trabajo

- El proyecto anterior y sus análisis son referencia de solo lectura.
- No copiar módulos completos del legacy. Conservar reglas, contratos o
  nomenclatura útil solo mediante una reimplementación revisada por dominio.
- No modificar archivos fuera del alcance de la especificación activa.
- Preservar cambios ajenos existentes en el árbol de trabajo.
- Mantener los diffs pequeños, revisables y acompañados de la documentación que
  corresponda.

## Arquitectura

- El sistema es un monolito modular por dominios.
- Las dependencias siguen `API → application → domain`.
- `infrastructure` implementa las interfaces o puertos definidos por el
  dominio.
- Los routers exponen transporte, validación y composición; no contienen
  lógica de negocio.
- Un dominio no consulta directamente las tablas de otro dominio. La
  colaboración ocurre mediante interfaces, casos de uso o eventos aprobados.
- La API es la autoridad del negocio; los clientes no sustituyen autorización,
  validación ni invariantes del backend.
- Los side effects transaccionales usan outbox y el trabajo pesado o diferido
  se ejecuta fuera del request cuando la especificación lo exige.

## Multi-tenancy y seguridad

- Aplicar deny by default: la ausencia de política, permiso o scope deniega.
- `tenant_id` es obligatorio salvo una tabla global justificada y documentada.
- PostgreSQL RLS es obligatorio para tablas tenant, con políticas default-deny y
  un rol de aplicación sin bypass.
- Configurar el contexto tenant dentro de la transacción y mantener el
  aislamiento en aplicación, DB, cache, archivos, jobs y eventos.
- Las pruebas cross-tenant negativas son obligatorias para cada feature y deben
  cubrir, cuando aplique, SQL directo, operaciones bulk, jobs y archivos.
- Los permisos se expresan como `resource.action` junto con scope y ownership.
  Costos y precios requieren permisos independientes.
- No usar `localStorage` para tokens de sesión web. Usar cookies
  `HttpOnly`, `Secure` y `SameSite` conforme a la especificación de seguridad.
- No registrar contraseñas, tokens, secretos, credenciales ni cuerpos sensibles.
- Usar Argon2id para contraseñas nuevas.
- Mantener secretos fuera del código y obtenerlos mediante configuración externa.
- Actualizar el threat model antes de cambios en Identity, Files, Procurement,
  Devices o tenancy.
- Vulnerabilidades críticas o altas explotables bloquean el release.

## Git y colaboración

- `main` está protegida: no hacer push directo.
- Usar una rama y un PR por especificación o por un grupo acotado de tareas
  relacionadas.
- No hacer commit ni push sin autorización explícita del usuario.
- No reescribir, descartar ni sobrescribir cambios ajenos.
- No editar una migración ya aplicada; las migraciones posteriores a release
  son append-only y sus upgrade/downgrade deben revisarse.

## Verificación y Definition of Done

- No declarar una tarea terminada sin ejecutar las pruebas proporcionales al
  cambio.
- Verificar, según corresponda, dominio, integración con PostgreSQL real,
  contratos API, autorización, aislamiento tenant, migraciones, constraints,
  E2E de journeys críticos, accesibilidad, seguridad, dependencias, secretos y
  contenedores.
- Reportar los comandos ejecutados.
- Reportar por separado las pruebas pasadas, fallidas y no ejecutadas, con la
  razón de cualquier prueba omitida.
- Indicar los riesgos restantes, la deuda crítica conocida y cualquier
  limitación de la verificación.
- Una feature solo está terminada cuando requisitos y aceptación están
  trazados, permisos y scopes están definidos, el aislamiento tenant está
  demostrado, la observabilidad y auditoría son suficientes, la documentación
  está actualizada y las comprobaciones requeridas pasan en CI.

## Convención de incertidumbre

Etiquetar afirmaciones, decisiones y hallazgos cuando su nivel de certeza sea
relevante:

- `[Seguro]`: verificado directamente en los documentos, código, pruebas o
  ejecución indicada.
- `[Probable]`: respaldado por evidencia, pero dependiente de configuración,
  despliegue o una comprobación pendiente.
- `[Suposición]`: hipótesis adoptada para avanzar y que requiere validación.
- `[No determinado]`: información insuficiente para concluir.

No presentar una suposición como hecho. Cuando una incertidumbre pueda afectar
la seguridad, el aislamiento tenant, el alcance o la aceptación, detenerse y
pedir resolución antes de continuar.
