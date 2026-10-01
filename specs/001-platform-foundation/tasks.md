# Tareas de Platform Foundation

Referencia: `SPEC-001`

## Metadata

- **SPEC:** `SPEC-001 Platform Foundation`
- **Versión:** `1.0.2`
- **Estado documental:** `Approved`
- **Responsable:** `Equipo de desarrollo`
- **Fecha:** `2026-09-30`

## Alcance y límites conservados

Esta versión realinea el trabajo de `plan.md` 1.0.1, aprobado por el Product
Owner el `2026-10-01`, tras la decisión arquitectónica del `2026-09-30`
registrada en `ADR-0003`, aceptado y aprobado independientemente el
`2026-10-01`. SPEC-001 estaba en `Plan Approved` inmediatamente antes de la
aprobación independiente de `tasks.md` 1.0.2; mediante este acto vuelve a
`Ready for Implementation` solo como disponibilidad documental.
La versión `1.0.0` de tareas fue aprobada
explícitamente por el Product Owner el `2026-09-25` y `SPEC-001` avanzó a
`Ready for Implementation`. Después, `TASK-001-001` se inició parcialmente
bajo autorización y produjo únicamente como trabajo parcial local
`docs/supply-chain/resolved-versions.md` y
`docs/supply-chain/provenance-notes.md`. Ambos archivos siguen incompletos y
sin seguimiento, quedan excluidos del futuro commit documental y no forman
parte de `tasks.md` 1.0.2 ni del conjunto versionado aprobado. No constituyen
evidencia final ni satisfacen el criterio de cierre; sus rutas siguen previstas
para `TASK-001-001`, y completarlos e incorporarlos requieren reanudar esa tarea
mediante autorización posterior e independiente. `TASK-001-001` no se completó:
sigue en `Pendiente`. Ninguna tarea posterior fue ejecutada,
ningún elemento de la checklist de cierre está marcado y ninguna evidencia
parcial satisface un criterio de cierre. La implementación permanece pausada;
ninguna tarea queda autorizada para ejecución y reanudar `TASK-001-001`
exige autorización posterior independiente.

- Solo existen dos operaciones HTTP de aplicación: `GET /health` y `GET /ready`.
  Ninguna tarea crea una tercera operación, una ruta de métricas, una ruta de
  diagnóstico, documentación dinámica ni una ruta de prueba en producción.
- Exclusiones de alcance conservadas: identidad, autenticación, sesiones,
  usuarios, roles, permisos de producto, organizaciones, tenancy, tablas o
  políticas RLS, cualquier base de datos, migraciones, outbox, jobs de dominio,
  datos de negocio, navegación de negocio, formularios complejos, tablas,
  dashboards, datos simulados, correo, push, notificaciones, aplicaciones
  móviles, Moodle, IoT, despliegue productivo definitivo, SLA, demostración de
  capacidad de 500/1000/2500/5000 usuarios, SLO global de latencia y
  dimensionamiento definitivo de infraestructura.
- Las observaciones del POC del borde no demuestran conformidad productiva.
  `UC` y `UR` siguen siendo comportamiento contratado **no observado**, y su
  verificación pertenece a la prueba de implementación del borde.
- Ninguna tarea instala dependencias, escribe código ni altera aprobaciones por
  el hecho de estar enumerada aquí.
- Envoy es el único límite público; `web-static` NGINX, interno y sin puerto
  publicado, sirve Vite e Inter. La topología central tiene cuatro servicios.
  El fallback SPA nunca puede capturar `/ready` ni añadir una operación API.

## Reglas de ownership de evidencia

Cada `TEST-*` y cada `SEC-TEST-*` tiene **exactamente un** responsable final de
evidencia. Las tareas distinguen cinco momentos que no se confunden entre sí:

1. **Creación de la prueba:** escribir el caso, el fixture y su arnés.
2. **Implementación del comportamiento:** el código o la configuración que la
   prueba ejercita.
3. **Ejecución preliminar:** ejecuciones locales o parciales durante el
   desarrollo, que no cierran la evidencia.
4. **Ejecución final:** la ejecución sobre el entorno completo del que depende
   realmente el criterio de aceptación.
5. **Ownership de la evidencia final:** la tarea que responde por esa ejecución
   final y por su registro.

Una tarea marcada `La matriz asigna el cierre a otra tarea` puede crear la
prueba y ejecutarla de forma preliminar, pero no cierra su evidencia.

## Tareas

### Fase 1: Base reproducible, inventario y contratos versionados

### TASK-001-001 — Resolver versiones, hashes, digests y procedencia iniciales

- **Descripción:** Resolver y registrar las versiones exactas, los hashes de distribución aplicables, los digests OCI de `linux/amd64` aplicables y la procedencia de Python 3.14, Node 24.21 LTS, pnpm 12.5, uv, FastAPI, Pydantic, Uvicorn, Ruff, Pyright, pytest, React, TypeScript, Vite, Tailwind, Storybook, Vitest, Testing Library, Playwright, axe-core, i18next, react-i18next, la tipografía Inter, OpenTelemetry SDK, OTLP y Collector, k6, OCI, Compose, Envoy, **NGINX oficial Debian 13 `trixie` para `web-static`**, Gitleaks, Trivy, Syft, ESLint y Prettier. Fijar las bases aprobadas `docker.io/library/python:3.14.7-slim-trixie` para builder/runtime backend y fixtures, `docker.io/library/node:24.21.0-trixie-slim` para builder Vite y catálogo, y `mcr.microsoft.com/playwright:v1.63.0-noble` para browser CI, cada una por hijo `linux/amd64`; resolver la versión estable exacta de `docker.io/library/nginx:<VERSION_ESTABLE_EXACTA>-trixie` y sus digests de índice e hijo. Ningún tarball de lenguaje sustituye la fijación de su base OCI. Esta tarea es el control temprano de resolución: no inventa workflows ni acciones CI futuras y no cierra la prueba negativa final de inventario.
- **Plataforma OCI:** `linux/amd64` es la única plataforma inicial soportada por SPEC-001. Todo digest por plataforma exigido aquí corresponde a `linux/amd64`; `linux/arm64` y las demás plataformas quedan fuera del alcance inicial y requieren una decisión y cambio posteriores. Para una imagen multiarch se registrarán por separado la referencia o tag de origen, el digest del índice OCI cuando exista y el digest del manifiesto hijo `linux/amd64`, junto con fuente oficial, fecha y comando reproducible de resolución; el tag de origen no se usará como fijación inmutable.
- **Política de acciones CI:** Ninguna acción puede usar una referencia flotante. La tarea que introduzca o modifique cada workflow seleccionará sus acciones, fijará cada una por SHA completo de commit y registrará su procedencia. `TASK-001-001` no selecciona acciones que todavía no existen; la prueba negativa final de inventario conserva su owner aprobado.
- **Rutas previstas:** `docs/supply-chain/resolved-versions.md`; `docs/supply-chain/provenance-notes.md`
- **Referencias:** `REQ-001-010`, `REQ-001-014`, `NFR-001-008`, `NFR-001-009`, `AC-001-020`, `AC-001-029`, `SEC-001-021`, `SEC-001-022`
- **Dependencias:** Ninguna
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `SEC-TEST-001-029`
- **Verificación requerida:** Comprobaciones documentales reproducibles de `docs/supply-chain/resolved-versions.md` y `docs/supply-chain/provenance-notes.md`: ninguna fila queda con procedencia ausente, artefacto vacío, hash falso o divergente, referencia flotante, `latest`, rango abierto, tag mutable como fijación, «no aplica» sin evidencia, hijo `linux/amd64` ausente, ID duplicado o ausente ni campo irresuelto. Incluir casos negativos que provoquen cada fallo, también para las bases OCI aprobadas. La versión ejercitada en el POC del borde no se registra como versión de entrega. La evidencia será consumida después por `G0` y `G1`; esos gates aún no existen en este punto del DAG y ejecutarlos no es precondición de cierre de esta tarea. Sus tareas de implementación verificarán ambos archivos.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** Cada tecnología aplicable de la matriz del plan nuevamente aprobado, salvo las acciones CI concretas aún no introducidas por un workflow, queda registrada con versión exacta, hash de distribución cuando exista un artefacto descargable aplicable, digest OCI del manifiesto `linux/amd64` cuando exista una imagen aplicable, procedencia oficial, fecha y método reproducible. Quedan incluidas Python/Node como bases OCI además de sus tarballs, NGINX estable `trixie` y Playwright de CI; Storybook y fixtures reutilizan las bases fijadas. Cada «no aplica» identifica el tipo de evidencia evaluado —hash de distribución, digest OCI u otro campo exigido—, la razón técnica concreta, una fuente oficial o una comprobación reproducible sobre el artefacto oficial, su URL o identificador de procedencia cuando corresponda, el comando o método de inspección cuando sea aplicable y la fecha de comprobación. Sin evidencia verificable suficiente, el elemento queda pendiente y la tarea no puede cerrarse. Ambos archivos superan la validación documental reproducible y los casos negativos anteriores; no contienen referencias flotantes. Cualquier elemento pendiente o realmente bloqueado mantiene la tarea abierta: «pendiente bloqueante» no satisface el cierre.
- **Estado:** `Pendiente`

### TASK-001-002 — Crear el workspace pnpm raíz y los scripts reproducibles

- **Descripción:** Configurar el monorepo con workspace pnpm, `packageManager` exacto y los scripts normativos que CI limitará a orquestar, sin crear paquetes, dominios ni interfaces sin consumidor.
- **Rutas previstas:** `pnpm-workspace.yaml`; `package.json`; `scripts/dev/bootstrap.sh`; `scripts/dev/verify.sh`; `tooling/tsconfig.base.json`
- **Referencias:** `REQ-001-003`, `REQ-001-011`, `NFR-001-001`, `AC-001-005`, `AC-001-021`, `SEC-001-019`, `SEC-001-031`
- **Dependencias:** `TASK-001-001`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-021`
- **Verificación requerida:** `G2`, `G3`; un checkout limpio descubre `apps/backend`, `apps/frontend` y `packages/ui` sin pasos manuales no documentados.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** los scripts raíz ejecutan las mismas órdenes que usará CI y ningún paquete queda sin consumidor.
- **Estado:** `Pendiente`

### TASK-001-003 — Fijar el proyecto Python del backend

- **Descripción:** Crear el proyecto Python del backend con instalación frozen por `uv.lock`, integridad por hashes y las herramientas Ruff, Pyright y pytest en las versiones resueltas, además de la estructura de directorios de pruebas por nivel.
- **Rutas previstas:** `apps/backend/pyproject.toml`; `apps/backend/uv.lock`; `apps/backend/tests/conftest.py`; `apps/backend/tests/{unit,integration,api,contract,security}/`
- **Referencias:** `REQ-001-011`, `NFR-001-001`, `AC-001-021`, `SEC-001-019`, `SEC-001-031`
- **Dependencias:** `TASK-001-001`, `TASK-001-002`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-021`
- **Verificación requerida:** `G2`, `G3`; la instalación frozen reproduce el entorno y rechaza una resolución implícita de la última versión.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** `uv.lock` resuelve con integridad y la suite vacía arranca en local y en CI con el mismo comando.
- **Estado:** `Pendiente`

### TASK-001-004 — Fijar el proyecto frontend y el paquete compartido

- **Descripción:** Crear el proyecto React/TypeScript/Vite y el paquete `packages/ui` con instalación frozen por `pnpm-lock.yaml`, TypeScript estricto y las herramientas de lint, formato y pruebas resueltas.
- **Rutas previstas:** `apps/frontend/package.json`; `apps/frontend/vite.config.ts`; `apps/frontend/tsconfig.json`; `packages/ui/package.json`; `packages/ui/tsconfig.json`; `pnpm-lock.yaml`
- **Referencias:** `REQ-001-011`, `NFR-001-001`, `AC-001-021`, `SEC-001-019`, `SEC-001-031`
- **Dependencias:** `TASK-001-001`, `TASK-001-002`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-021`
- **Verificación requerida:** `G2`, `G3`; la instalación frozen y la build son reproducibles y no incorporan una librería visual completa alternativa.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el workspace resuelve `packages/ui` como única fuente visual compartida del frontend.
- **Estado:** `Pendiente`

### TASK-001-005 — Crear el inventario versionado de recursos y su validador temprano

- **Descripción:** Crear el inventario versionado de dependencias de producción, desarrollo y prueba, acciones de CI, **bases OCI de Python, Node, NGINX y Playwright, además de Envoy, Collector y k6 y las imágenes derivadas de backend, edge y `web-static`**, addons del catálogo, fuentes, iconos y demás recursos visuales, con procedencia, uso y forma de fijación, más el validador que CI ejecutará. Storybook y fixtures declaran reutilización de bases, sin fingir «no aplica» para las imágenes que ejecutan. Es el control temprano de resolución; la prueba negativa final pertenece a otra tarea.
- **Rutas previstas:** `docs/supply-chain/resource-inventory.md`; `tooling/supply-chain/inventory.schema.json`; `scripts/ci/verify-resource-inventory`
- **Referencias:** `REQ-001-010`, `REQ-001-014`, `REQ-001-022`, `REQ-001-023`, `REQ-001-025`, `NFR-001-002`, `NFR-001-009`, `NFR-001-020`, `AC-001-020`, `AC-001-029`, `SEC-001-021`, `SEC-001-022`
- **Dependencias:** `TASK-001-001`, `TASK-001-002`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `SEC-TEST-001-029`
- **Verificación requerida:** `G0`, `G1`; el validador falla ante una entrada sin procedencia, sin forma de fijación o sin uso declarado.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el inventario declara su versión y el validador se ejecuta con el mismo comando en local y en CI.
- **Estado:** `Pendiente`

### TASK-001-006 — Versionar el esquema de eventos de log

- **Descripción:** Crear el JSON Schema cerrado de los registros permitidos, con `additionalProperties: false`, obligatorios `timestamp` UTC, `level`, `event` y `component`, `request_id` obligatorio cuando el registro pertenece al ciclo de una solicitud y restringido al patrón de `ValidRequestId`, y enumeraciones cerradas de nivel, resultado, plantilla de ruta y motivo de reducción o fallo.
- **Rutas previstas:** `contracts/observability/log-event.schema.json`
- **Referencias:** `REQ-001-009`, `NFR-001-005`, `NFR-001-007`, `AC-001-017`, `SEC-001-007`, `SEC-001-008`
- **Dependencias:** `TASK-001-002`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-017`
- **Verificación requerida:** `G4`; un registro conforme valida y un campo fuera del esquema falla, sin degradarse a una comprobación ad hoc.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el archivo existe, es un JSON Schema válido, está versionado y es el que la suite usa realmente.
- **Estado:** `Pendiente`

### TASK-001-007 — Redactar la documentación de arranque, verificación y troubleshooting

- **Descripción:** Documentar prerrequisitos, configuración externa, arranque de backend y frontend, comprobación de health y readiness, ejecución de pruebas, controles equivalentes a CI y resolución de problemas frecuentes, sin secretos reales. Esta tarea redacta; la validación desde un checkout limpio pertenece a la tarea de cierre documental.
- **Rutas previstas:** `docs/operations/getting-started.md`; `docs/operations/verification.md`; `docs/operations/troubleshooting.md`
- **Referencias:** `REQ-001-003`, `REQ-001-015`, `NFR-001-001`, `NFR-001-010`, `AC-001-004`, `AC-001-005`, `SEC-001-001`, `SEC-001-002`
- **Dependencias:** `TASK-001-003`, `TASK-001-004`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-004`, `TEST-001-005`
- **Verificación requerida:** `G0`, `G7`; el contenido cubre los siete apartados exigidos y no incluye ningún valor secreto utilizable.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** cada comando documentado existe en `scripts/` y coincide con el que ejecuta CI.
- **Estado:** `Pendiente`

### Fase 2: Backend, configuración y operaciones contratadas

### TASK-001-008 — Validar la configuración externa y su fallo cerrado

- **Descripción:** Implementar con Pydantic la validación tipada de campos obligatorios, tipos, rangos, longitudes, enumeraciones y relaciones entre presupuestos antes del bind operativo, con carga de secretos desde archivos restringidos, rechazo de entradas inesperadas dentro del namespace y diagnóstico que solo nombra campo y causa sanitizada.
- **Rutas previstas:** `apps/backend/src/platform_foundation/infrastructure/config/settings.py`; `apps/backend/src/platform_foundation/infrastructure/config/validators.py`; `apps/backend/tests/integration/test_configuration.py`; `apps/backend/tests/security/test_configuration_hostile.py`
- **Referencias:** `REQ-001-004`, `NFR-001-006`, `AC-001-006`, `AC-001-007`, `AC-001-008`, `SEC-001-001`, `SEC-001-002`, `SEC-001-030`
- **Dependencias:** `TASK-001-003`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-006`, `TEST-001-007`, `TEST-001-008`, `SEC-TEST-001-001`, `SEC-TEST-001-002`
- **Verificación requerida:** `G4`; ausencia, formato inválido, exceso de longitud, caracteres maliciosos, destino arbitrario, debug productivo y límites no acotados fallan cerrado sin reproducir el valor.
- **Evidencia final a cargo:** `TEST-001-006`, `TEST-001-007`, `TEST-001-008`, `SEC-TEST-001-001`, `SEC-TEST-001-002`
- **Criterio observable de cierre:** ningún componente queda listo con configuración obligatoria ausente o inválida y ningún diagnóstico reproduce un valor sensible.
- **Estado:** `Pendiente`

### TASK-001-009 — Crear la estructura modular del backend

- **Descripción:** Crear las capas `api`, `application`, `domain`, `infrastructure` y `bootstrap` con dependencias `API → application → domain`, infrastructure implementando puertos y bootstrap preparado para componer el ciclo de vida, sin lógica de negocio en routers y sin entidades ficticias. Esta tarea es **scaffolding**: deja la estructura necesaria para implementar después las operaciones contratadas y **no registra ni expone todavía ninguna operación**. La composición, el registro de rutas y el arranque real del proceso pertenecen a la tarea de integración del backend.
- **Rutas previstas:** `apps/backend/src/platform_foundation/api/`; `apps/backend/src/platform_foundation/application/`; `apps/backend/src/platform_foundation/domain/`; `apps/backend/src/platform_foundation/infrastructure/`; `apps/backend/src/platform_foundation/bootstrap/`; `apps/backend/tests/unit/test_layer_boundaries.py`
- **Referencias:** `REQ-001-001`, `AC-001-001`, `SEC-001-001`, `SEC-001-032`
- **Dependencias:** `TASK-001-003`, `TASK-001-008`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-001`
- **Verificación requerida:** `G2`; el control de imports respeta ADR-0001 y ninguna capa importa en una dirección prohibida.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** las cinco capas existen con sus fronteras verificables, ninguna contiene lógica de negocio ni entidades ficticias y ninguna operación queda registrada todavía.
- **Estado:** `Pendiente`

### TASK-001-010 — Implementar `GET /health`

- **Descripción:** Implementar la comprobación de vida resuelta en memoria desde el estado del proceso, con 200 `{"status":"alive"}`, `Cache-Control: no-store`, `X-Request-ID` conforme al contrato, sin consultar ni enumerar dependencias, sin evaluar readiness, sin consultar el Collector y sin compartir la vía de admisión operacional.
- **Rutas previstas:** `apps/backend/src/platform_foundation/api/routes/health.py`; `apps/backend/tests/api/test_health.py`
- **Referencias:** `REQ-001-005`, `NFR-001-017`, `AC-001-009`, `SEC-001-004`, `SEC-001-029`
- **Dependencias:** `TASK-001-009`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-009`
- **Verificación requerida:** `G4`; la respuesta 200 contiene solo `status` y no revela versión, hostname, configuración ni causa interna.
- **Evidencia final a cargo:** `TEST-001-009`
- **Criterio observable de cierre:** health responde vida sin tocar ninguna dependencia y su resultado no se presenta como capacidad de negocio.
- **Estado:** `Pendiente`

### TASK-001-011 — Implementar `GET /ready` y su máquina de estados

- **Descripción:** Implementar la comprobación de readiness con 200 `{"status":"ready"}` cuando la aplicación está inicializada y 503 `{"status":"not_ready"}` durante inicio, cierre y ante indisponibilidad, saturación o timeout de su dependencia obligatoria, conservando el cuerpo mínimo sin `ErrorResponse` y sin representar ninguna de esas condiciones con 500. El cableado de la dependencia obligatoria real y el cierre de su evidencia pertenecen a tareas posteriores; el doble sintético queda reservado a casos adicionales de transición.
- **Rutas previstas:** `apps/backend/src/platform_foundation/application/readiness/service.py`; `apps/backend/src/platform_foundation/application/readiness/state.py`; `apps/backend/src/platform_foundation/api/routes/ready.py`; `apps/backend/tests/integration/test_readiness_states.py`
- **Referencias:** `REQ-001-005`, `REQ-001-006`, `AC-001-010`, `AC-001-011`, `SEC-001-004`, `SEC-001-005`, `SEC-001-029`
- **Dependencias:** `TASK-001-009`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-010`, `TEST-001-011`, `SEC-TEST-001-008`
- **Verificación requerida:** `G4`; la transición listo/no listo conserva cuerpo mínimo y health mantiene su semántica de vida.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** las tres condiciones de no listo se representan con 503 y ninguna con 500.
- **Estado:** `Pendiente`

### TASK-001-012 — Resolver el request ID en la aplicación con CSPRNG

- **Descripción:** Implementar el middleware exterior de correlación que acepta un candidato `X-Request-ID` de 1 a 128 caracteres conforme a `^[A-Za-z0-9._-]+$` y lo devuelve idéntico; ante ausencia, invalidez o ambigüedad por duplicación descarta el valor completo y genera 16 bytes de un CSPRNG del sistema codificados como 32 caracteres hexadecimales en minúscula. Nunca trunca, normaliza ni registra el candidato inválido y no produce 400 ni 422 por el identificador. Este generador, y solo este, sustenta la afirmación ASVS 11.5.1 del plan.
- **Rutas previstas:** `apps/backend/src/platform_foundation/api/middleware/correlation.py`; `apps/backend/src/platform_foundation/domain/correlation/request_id.py`; `apps/backend/tests/integration/test_request_id_application.py`; `apps/backend/tests/security/test_request_id_hostile.py`
- **Referencias:** `REQ-001-008`, `NFR-001-005`, `AC-001-014`, `AC-001-015`, `AC-001-016`, `SEC-001-006`, `SEC-001-007`
- **Dependencias:** `TASK-001-009`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-015`, `SEC-TEST-001-004`, `SEC-TEST-001-005`
- **Verificación requerida:** `G4`; un identificador de 129 caracteres dentro del presupuesto HTTP llega al middleware y obtiene respuesta normal con nuevo ID; CR, LF, tabulador e intentos de inyección no crean líneas ni claves adicionales; los caracteres que invalidan el protocolo se prueban en la frontera ASGI.
- **Evidencia final a cargo:** `TEST-001-015`, `SEC-TEST-001-004`, `SEC-TEST-001-005`
- **Criterio observable de cierre:** el identificador generado tiene 32 caracteres hexadecimales, 128 bits de entropía y no se deriva en ninguna parte del valor recibido.
- **Estado:** `Pendiente`

### TASK-001-013 — Integrar y arrancar el backend con sus dos operaciones

- **Descripción:** Componer la aplicación en `bootstrap` a partir de la configuración ya validada, encadenar el middleware exterior de correlación y **registrar en el router exclusivamente** `GET /health` y `GET /ready`. **La propia composición desactiva las rutas automáticas de documentación y la ruta automática del esquema OpenAPI que el framework serviría por defecto**, mediante los parámetros de construcción correspondientes de FastAPI y sin añadir ningún endpoint alternativo que las sustituya. A continuación arranca el proceso real y verifica que ambas operaciones quedan disponibles y que **no existe ninguna operación adicional**: sin autenticación, tenancy ni módulos de negocio, sin documentación ni esquema servidos, sin ruta de métricas, de diagnóstico ni de prueba.
- **Rutas previstas:** `apps/backend/src/platform_foundation/bootstrap/app.py`; `apps/backend/src/platform_foundation/bootstrap/composition.py`; `apps/backend/src/platform_foundation/api/router.py`; `apps/backend/tests/integration/test_startup_surface.py`
- **Referencias:** `REQ-001-001`, `REQ-001-005`, `REQ-001-006`, `AC-001-001`, `SEC-001-001`, `SEC-001-004`, `SEC-001-005`, `SEC-001-032`
- **Dependencias:** `TASK-001-009`, `TASK-001-010`, `TASK-001-011`, `TASK-001-012`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-001`
- **Verificación requerida:** `G4`; el proceso arranca con configuración válida y la superficie observable contiene **exactamente** las dos operaciones contratadas, sin ninguna adicional; la ausencia de las rutas automáticas de documentación y de esquema se comprueba como resultado de **esta misma composición**, no de una tarea posterior.
- **Evidencia final a cargo:** `TEST-001-001`
- **Criterio observable de cierre:** el backend arrancado por esta tarea responde en las dos rutas registradas y la inspección de su superficie compuesta no encuentra documentación, esquema, ni ninguna otra operación o capacidad fuera de alcance.
- **Estado:** `Pendiente`

### TASK-001-014 — Cerrar el contrato de errores de la aplicación

- **Descripción:** Implementar el handler final que construye el objeto cerrado `ErrorResponse` con `code`, `message` y `request_id`, reserva 500 `INTERNAL_ERROR` exclusivamente a excepción interna no controlada, **desactiva las páginas y superficies de depuración adicionales que pudieran quedar habilitadas después de la composición**, evita respuestas o errores no autorizados y no añade respuestas 422 automáticas ni 400 por un identificador inválido. La desactivación de las rutas automáticas de documentación y de esquema **ya queda resuelta en la composición de `TASK-001-013`** y no se reclama aquí; esta tarea solo comprueba que ninguna respuesta de error reintroduzca una superficie equivalente.
- **Rutas previstas:** `apps/backend/src/platform_foundation/api/errors/contract.py`; `apps/backend/src/platform_foundation/api/errors/handlers.py`; `apps/backend/tests/api/test_error_contract.py`; `apps/backend/tests/security/test_error_sanitization.py`
- **Referencias:** `REQ-001-007`, `NFR-001-006`, `AC-001-012`, `AC-001-013`, `SEC-001-003`, `SEC-001-028`
- **Dependencias:** `TASK-001-010`, `TASK-001-011`, `TASK-001-012`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-012`, `TEST-001-013`
- **Verificación requerida:** `G4`; la respuesta pública no contiene stack trace, excepción cruda, hostname, configuración ni detalle interno, y `X-Request-ID` coincide con `request_id`.
- **Evidencia final a cargo:** `TEST-001-012`, `TEST-001-013`
- **Criterio observable de cierre:** un centinela sintético inyectado en la excepción no aparece en la respuesta pública.
- **Estado:** `Pendiente`

### Fase 3: Observabilidad, admisión y plazos de la aplicación

### TASK-001-015 — Emitir logs estructurados validados contra el esquema

- **Descripción:** Emitir JSON a stdout, un objeto por línea, con serialización estructurada y nunca por concatenación de texto controlado por el cliente, aplicando la lista permitida de campos, los niveles documentados y la supresión del INFO individual por request exitoso, con contabilización de los fallos de serialización.
- **Rutas previstas:** `apps/backend/src/platform_foundation/infrastructure/logging/emitter.py`; `apps/backend/src/platform_foundation/infrastructure/logging/fields.py`; `apps/backend/tests/integration/test_log_schema.py`
- **Referencias:** `REQ-001-009`, `NFR-001-005`, `NFR-001-007`, `AC-001-017`, `SEC-001-007`, `SEC-001-008`
- **Dependencias:** `TASK-001-006`, `TASK-001-012`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-017`
- **Verificación requerida:** `G4`; cada registro capturado valida contra `contracts/observability/log-event.schema.json` y ninguno contiene cuerpo, cookie, token, URL cruda, parámetro SQL ni configuración completa.
- **Evidencia final a cargo:** `TEST-001-017`
- **Criterio observable de cierre:** un campo emitido fuera del esquema hace fallar la prueba en lugar de degradarse a una comprobación más débil.
- **Estado:** `Pendiente`

### TASK-001-016 — Crear el canal obligatorio de eventos de seguridad y auditoría

- **Descripción:** Crear la cola separada de 1024 registros de hasta 2 KiB con espera máxima de 100 ms, sin sampling, con escritura a stdout de deadline 100 ms y reintentos 1/2/4 s dentro de una ventana máxima de 10 s, contadores de observado, emitido, agregado, suprimido y fallido, señal independiente al supervisor y retirada de readiness con terminación controlada si no se recupera en 10 s.
- **Rutas previstas:** `apps/backend/src/platform_foundation/infrastructure/logging/mandatory_channel.py`; `apps/backend/src/platform_foundation/infrastructure/logging/counters.py`; `apps/backend/tests/integration/test_mandatory_channel.py`
- **Referencias:** `REQ-001-021`, `AC-001-040`, `SEC-001-009`, `SEC-001-010`, `SEC-001-015`
- **Dependencias:** `TASK-001-011`, `TASK-001-015`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-040`, `SEC-TEST-001-010`, `SEC-TEST-001-011`
- **Verificación requerida:** `G4`; ningún evento obligatorio desaparece sin señal ni contador, y la cola llena detiene promoción y carga.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la conciliación por ventana cuadra pendientes iniciales más observados con emitidos, agregados, suprimidos, pendientes finales y fallos explícitos.
- **Estado:** `Pendiente`

### TASK-001-017 — Implementar los productores reales de eventos de la aplicación

- **Descripción:** Emitir al canal obligatorio, desde la aplicación, las clases reales de evento que le corresponden: configuración rechazada, validación previa al bind fallida, candidato de correlación inválido registrado **sin** el valor hostil y degradación o caída del propio canal obligatorio. Ningún evento se sintetiza desde una prueba ni se inventa auditoría de dominio.
- **Rutas previstas:** `apps/backend/src/platform_foundation/infrastructure/logging/producers/application.py`; `apps/backend/src/platform_foundation/infrastructure/logging/producers/catalog.py`; `apps/backend/tests/integration/test_application_event_producers.py`
- **Referencias:** `REQ-001-004`, `REQ-001-008`, `REQ-001-009`, `REQ-001-021`, `NFR-001-006`, `AC-001-040`, `SEC-001-001`, `SEC-001-006`, `SEC-001-008`, `SEC-001-010`
- **Dependencias:** `TASK-001-008`, `TASK-001-012`, `TASK-001-016`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-040`, `SEC-TEST-001-011`
- **Verificación requerida:** `G4`; cada clase produce un evento real con nombre estable, componente y correlación cuando existe contexto, y el candidato hostil no aparece en ningún campo.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** las cuatro clases de la aplicación se observan en el canal separado durante una ejecución real, no simulada.
- **Estado:** `Pendiente`

### TASK-001-018 — Integrar el canal obligatorio como dependencia obligatoria de readiness

- **Descripción:** Cablear el canal obligatorio como la única dependencia obligatoria real de readiness: cuando no puede contabilizar un evento obligatorio —cola llena que no drena, escritura a stdout fallida tras sus reintentos acotados o fallo explícito del contador—, la aplicación retira readiness, responde 503 `{"status":"not_ready"}`, emite la señal independiente al supervisor y termina controladamente si no se recupera en 10 s, mientras health conserva su semántica de vida.
- **Rutas previstas:** `apps/backend/src/platform_foundation/application/readiness/mandatory_dependency.py`; `apps/backend/src/platform_foundation/bootstrap/lifespan.py`; `apps/backend/tests/integration/test_readiness_mandatory_wiring.py`
- **Referencias:** `REQ-001-005`, `REQ-001-006`, `REQ-001-021`, `AC-001-010`, `AC-001-011`, `SEC-001-004`, `SEC-001-005`, `SEC-001-010`
- **Dependencias:** `TASK-001-011`, `TASK-001-016`, `TASK-001-017`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-011`, `SEC-TEST-001-008`
- **Verificación requerida:** `G4`; la indisponibilidad, la saturación y el timeout del canal se expresan siempre con el 503 contratado y nunca con 500, y readiness no enumera la dependencia.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el canal obligatorio es la dependencia que readiness evalúa en producción, y el doble sintético queda restringido a transiciones difíciles de forzar.
- **Estado:** `Pendiente`

### TASK-001-019 — Acotar la admisión del backend y su espera máxima

- **Descripción:** Implementar semáforos independientes de 64 activos, 60 públicos y 4 reservados operacionales, con cola pública de 32 y operacional de 4 y espera máxima de 100 ms. Al llenarse la cola o expirar la espera, el middleware devuelve 429 `RATE_LIMITED` **solo** por límite de frecuencia o admisión, contabiliza el rechazo y nunca informa éxito falso.
- **Rutas previstas:** `apps/backend/src/platform_foundation/api/middleware/admission.py`; `apps/backend/src/platform_foundation/infrastructure/config/limits.py`; `apps/backend/tests/integration/test_admission.py`
- **Referencias:** `REQ-001-020`, `NFR-001-016`, `AC-001-039`, `SEC-001-015`, `SEC-001-016`
- **Dependencias:** `TASK-001-008`, `TASK-001-014`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-039`, `SEC-TEST-001-009`
- **Verificación requerida:** `G4`; la saturación de admisión produce 429 y métricas de rechazo, sin usar 429 para representar un vencimiento de plazo.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** las dos vías de admisión conservan sus reservas y el polling público no consume la capacidad operacional.
- **Estado:** `Pendiente`

### TASK-001-020 — Implementar el deadline interno de aplicación de 2 s desde la admisión

- **Descripción:** Implementar el deadline total de 2 s que **empieza a contar en el instante de la admisión del request en el middleware**, se aplica por cancelación del trabajo interno y es un límite distinto y separado del plazo upstream del borde (`route.timeout`, 3 s). Distingue expresamente la espera de admisión, la saturación de admisión y el trabajo ya admitido: la espera y la saturación se representan con 429 y el vencimiento del trabajo admitido produce la respuesta propia de la aplicación, sin convertirse en `500 INTERNAL_ERROR`, que queda reservado a excepción interna no controlada. La contención `deadline interno 2 s ⊂ plazo upstream 3 s` se declara como relación de configuración exigible una vez admitida la solicitud y **no** se presenta como cota end-to-end desde el navegador ni como garantía de emisión o entrega de la respuesta.
- **Rutas previstas:** `apps/backend/src/platform_foundation/api/middleware/deadline.py`; `apps/backend/src/platform_foundation/application/cancellation.py`; `apps/backend/tests/integration/test_internal_deadline.py`
- **Referencias:** `REQ-001-020`, `NFR-001-016`, `AC-001-039`, `SEC-001-015`, `SEC-001-016`
- **Dependencias:** `TASK-001-019`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-039`
- **Verificación requerida:** `G4`; la prueba mide el instante de inicio del deadline en la admisión, comprueba la cancelación efectiva del trabajo interno, verifica el ordenamiento estricto 2 s < 3 s y rechaza toda configuración que lo invierta.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** un vencimiento controlado cancela el trabajo interno sin producir 500 y sin que ninguna evidencia afirme una cota medida desde el primer byte del cliente.
- **Estado:** `Pendiente`

### TASK-001-021 — Instrumentar las métricas OTLP y los sensores de recursos

- **Descripción:** Instrumentar las familias aprobadas con OpenTelemetry y exportarlas por OTLP gRPC al Collector interno, con plantillas de ruta de catálogo finito, dimensiones enumeradas y acotadas, temporalidad delta, buckets de duración aprobados, presupuesto de 10000 series, exemplars deshabilitados y sensores reales de CPU y RSS desde contadores de proceso y cuota. No se crea `/metrics` ni ninguna otra operación HTTP, y los pools de persistencia se registran N/A en lugar de emitir ceros.
- **Rutas previstas:** `apps/backend/src/platform_foundation/infrastructure/telemetry/metrics.py`; `apps/backend/src/platform_foundation/infrastructure/telemetry/exporter.py`; `apps/backend/src/platform_foundation/infrastructure/telemetry/catalog.py`; `apps/backend/tests/integration/test_metrics_contract.py`
- **Referencias:** `REQ-001-018`, `REQ-001-021`, `NFR-001-014`, `AC-001-034`, `AC-001-035`, `AC-001-036`, `SEC-001-011`, `SEC-001-012`, `SEC-001-013`, `SEC-001-014`, `SEC-001-016`
- **Dependencias:** `TASK-001-009`, `TASK-001-019`, `TASK-001-020`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-034`, `TEST-001-035`, `TEST-001-036`, `SEC-TEST-001-006`, `SEC-TEST-001-018`
- **Verificación requerida:** `G4`; ninguna dimensión admite `request_id`, `user_id`, `tenant_id`, IP ni URL cruda, y el validador de cardinalidad rechaza una dimensión nueva antes de exportar.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** las familias emiten unidades y semántica documentadas y el cierre sin respuesta usa `status_class = transport`, nunca un código HTTP.
- **Estado:** `Pendiente`

### Fase 4: Borde HTTP, correlación, límites y taxonomía de resultados

### TASK-001-022 — Crear la entrada pública del borde

- **Descripción:** Definir declarativamente el único listener público de Envoy, terminador TLS y proxy, sin usar el filtro `file_server`. El orden de rutas trata `/ready` explícitamente como ausente **antes** del catch-all estático, dirige la coincidencia exacta `GET /health` al cluster backend y `GET`/`HEAD` de Vite e Inter al cluster `web-static`; no introduce passthrough genérico a la API. Declarar el reenvío hacia `web-static` del `X-Request-ID` final validado por la cadena del borde, nunca del candidato temporal. Envoy conserva correlación, control de acceso, límites, timeouts, rate limiting y respuestas locales; las rutas desconocidas, codificadas o ambiguas no amplían el mapa API. El comportamiento real del fallback SPA y de las redes se cierra después.
- **Rutas previstas:** `tooling/envoy/listeners/public.yaml`; `tooling/envoy/routes/public-routes.yaml`; `tooling/envoy/clusters/backend.yaml`; `tooling/envoy/clusters/web-static.yaml`; `tests/edge/test_public_route_config.py`
- **Referencias:** `REQ-001-002`, `REQ-001-005`, `REQ-001-010`, `REQ-001-016`, `AC-001-009`, `AC-001-030`, `SEC-001-004`, `SEC-001-028`, `SEC-001-029`
- **Dependencias:** `TASK-001-001`, `TASK-001-010`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-030`
- **Verificación requerida:** Validación estática de listeners, clusters, orden de rutas y forwarding del identificador, más pruebas aisladas con fixtures sintéticos: `/ready` queda fuera del mapa público antes del catch-all; solo la coincidencia exacta `GET /health` selecciona el cluster backend; las rutas estáticas seleccionan el cluster `web-static`; codificaciones, rutas desconocidas y métodos no permitidos no amplían la API. `G4` consumirá después esta configuración; no se exige ejecutar ese gate para cerrar esta tarea ni se afirma una imagen, red o despliegue real.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** La configuración declarativa de listeners, clusters y rutas supera la validación estática y las pruebas aisladas, con exclusión explícita de `/ready` antes del fallback. Esta tarea no cierra accesibilidad real de `web-static`, ausencia de exposición pública, conectividad entre contenedores, flujo extremo a extremo ni precedencia real de `/ready`: la red y exposición corresponden a `TASK-001-060`, y el enrutamiento real y fallback a `TASK-001-070`.
- **Estado:** `Pendiente`

### TASK-001-023 — Crear la entrada operacional del borde con deny by default

- **Descripción:** Configurar el listener operacional enlazado a una dirección no enrutable públicamente del segmento privado, que sirve exclusivamente la coincidencia exacta `GET /ready`, con allowlist de peers evaluada sobre la dirección del peer validado y nunca sobre cabeceras de forwarding, inventario de principales con tipo, origen y vencimiento, y comportamiento por defecto denegado: si no se configura, el listener no se crea y `/ready` queda inalcanzable.
- **Rutas previstas:** `tooling/envoy/listeners/operational.yaml`; `tooling/envoy/routes/operational-routes.yaml`; `tooling/envoy/rbac/allowed-peers.yaml`; `tooling/edge/operational-inventory.yaml`
- **Referencias:** `REQ-001-006`, `REQ-001-018`, `AC-001-010`, `AC-001-030`, `SEC-001-005`, `SEC-001-013`, `SEC-001-029`, `SEC-001-030`
- **Dependencias:** `TASK-001-001`, `TASK-001-011`, `TASK-001-022`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-010`, `SEC-TEST-001-007`
- **Verificación requerida:** `G4`; la denegación se evalúa sobre el peer validado y una cabecera de origen suplantada no concede acceso.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** `/ready` solo existe en la entrada operacional y la ausencia de configuración deja la operación inalcanzable.
- **Estado:** `Pendiente`

### TASK-001-024 — Validar la configuración del borde antes del bind

- **Descripción:** Implementar el validador común que falla cerrado **antes del bind** si `/ready` aparece en las rutas públicas, si el listen operacional resuelve a una dirección públicamente enrutable o fuera de la red privada declarada, si `public_exposure` no es `false`, si la allowlist está vacía, abarca todo el espacio de direcciones o incluye un origen no inventariado, o si una entrada del inventario carece de principal, tipo, origen o vencimiento o está vencida. El validador emite el evento obligatorio de validación previa al bind fallida.
- **Rutas previstas:** `tooling/edge/edge-config.schema.json`; `scripts/ci/verify-edge-config`; `tests/operational/test_edge_config_validator.py`
- **Referencias:** `REQ-001-004`, `REQ-001-006`, `REQ-001-018`, `AC-001-008`, `AC-001-010`, `SEC-001-001`, `SEC-001-013`, `SEC-001-029`, `SEC-001-030`
- **Dependencias:** `TASK-001-008`, `TASK-001-022`, `TASK-001-023`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `SEC-TEST-001-002`, `SEC-TEST-001-007`
- **Verificación requerida:** `G0`, `G4`; ninguna comprobación depende de un proveedor concreto y todas se resuelven sobre el inventario de configuración.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** una configuración inválida impide aceptar tráfico y su diagnóstico solo nombra campo y causa sanitizada.
- **Estado:** `Pendiente`

### TASK-001-025 — Capturar el candidato de `X-Request-ID` en una cabecera temporal

- **Descripción:** Primer paso del flujo aprobado. Antes del enrutado y **antes de que el borde genere su propio identificador**, una mutación temprana de cabeceras copia el candidato recibido a una cabecera temporal. Si el cliente no envió `X-Request-ID`, la copia queda vacía y la cabecera temporal **no se añade**, de modo que «ausente» y «presente pero vacío» no se confunden.
- **Rutas previstas:** `tooling/envoy/filters/request-id-capture.yaml`
- **Referencias:** `REQ-001-008`, `NFR-001-005`, `AC-001-014`, `AC-001-015`, `AC-001-016`, `SEC-001-006`, `SEC-001-007`
- **Dependencias:** `TASK-001-022`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-014`, `TEST-001-016`
- **Verificación requerida:** `G4`; la captura ocurre antes de la generación del sustituto y no normaliza ni trunca el candidato.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la cabecera temporal existe solo cuando el cliente envió un valor, y su contenido es el candidato íntegro.
- **Estado:** `Pendiente`

### TASK-001-026 — Generar incondicionalmente el identificador sustituto del borde

- **Descripción:** Segundo paso del flujo aprobado. El borde escribe **incondicionalmente** un identificador propio en `X-Request-ID`, con formato de UUID que satisface `ValidRequestId`, antes de cualquier byte hacia el upstream, de modo que toda respuesta que el borde emita por su cuenta ya disponga de un identificador válido. Este sustituto es distinto del CSPRNG de la aplicación y no hereda su cobertura ASVS.
- **Rutas previstas:** `tooling/envoy/filters/request-id-generate.yaml`
- **Referencias:** `REQ-001-008`, `NFR-001-005`, `AC-001-015`, `AC-001-016`, `SEC-001-006`
- **Dependencias:** `TASK-001-025`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-016`
- **Verificación requerida:** `G4`; la generación es incondicional y precede a cualquier respuesta local del borde.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** toda respuesta emitida por el borde, incluidas 403, 408, 413, 429, 502 y 504, lleva un `X-Request-ID` válido ya existente.
- **Estado:** `Pendiente`

### TASK-001-027 — Validar y restaurar el candidato carácter a carácter

- **Descripción:** Pasos tercero, cuarto y quinto del flujo aprobado. El **primer filtro HTTP de la cadena**, anterior a acceso, frecuencia, tamaño y enrutado, lee la cabecera temporal y la acepta solo si tiene entre 1 y 128 caracteres y cumple `^[A-Za-z0-9._-]+$`. Si la acepta, restaura ese valor sobre `X-Request-ID` **carácter a carácter**; si no, **conserva el sustituto**. No aplica ningún centinela sobre valores concretos: un identificador válido formado por un único guion es un `ValidRequestId` y se conserva. Una cabecera repetida se resuelve por la misma regla, sin caso especial: la lectura une los valores repetidos con una coma, la coma no pertenece al patrón y el candidato completo se descarta sin elegir uno de los valores ni conservar un fragmento.
- **Rutas previstas:** `tooling/envoy/filters/request-id-restore.yaml`; `tooling/envoy/filters/request-id-restore.lua`
- **Referencias:** `REQ-001-008`, `NFR-001-005`, `AC-001-014`, `AC-001-016`, `SEC-001-006`, `SEC-001-007`
- **Dependencias:** `TASK-001-026`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-014`, `TEST-001-016`
- **Verificación requerida:** `G4`; los casos cubiertos son candidato ausente, válido de 1 carácter, válido `-`, válido de 128 caracteres, caracteres inválidos, 129 caracteres y cabecera repetida unida por coma.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el valor restaurado es idéntico al recibido y el rechazado deja intacto el sustituto, sin fragmentos ni normalización.
- **Estado:** `Pendiente`

### TASK-001-028 — Eliminar la cabecera temporal y fijar el consumo del identificador final

- **Descripción:** Pasos sexto y séptimo del flujo aprobado. El filtro **borra siempre** la cabecera temporal, acepte o rechace el candidato, de modo que el valor hostil no se reenvía al backend, no entra en ningún cuerpo y no se registra. Los filtros restantes y el router consumen el identificador ya resuelto del mismo mapa de cabeceras, de forma que el identificador que ve el upstream, el que viaja en la cabecera de la respuesta y el que aparece en el campo `request_id` del cuerpo son el mismo valor, con una sola cabecera `X-Request-ID` en la respuesta.
- **Rutas previstas:** `tooling/envoy/filters/request-id-cleanup.yaml`; `tooling/envoy/filters/chain-order.yaml`
- **Referencias:** `REQ-001-008`, `NFR-001-005`, `AC-001-014`, `AC-001-016`, `SEC-001-006`, `SEC-001-007`
- **Dependencias:** `TASK-001-027`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-014`, `TEST-001-016`
- **Verificación requerida:** `G4`; la cabecera temporal no aparece en upstream, respuesta ni logs, y el orden de la cadena sitúa la validación antes de acceso, frecuencia, tamaño y enrutado.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la respuesta lleva exactamente una cabecera `X-Request-ID` y su valor coincide con el consumido por el upstream.
- **Estado:** `Pendiente`

### TASK-001-029 — Evaluar la adecuación del generador del sustituto frente a ASVS 11.5.1

- **Descripción:** Evaluar y registrar con evidencia verificable si el generador del identificador sustituto del borde es adecuado para un valor destinado a ser no adivinable, dado que la documentación oficial del adaptador **no** lo caracteriza como criptográfico. Hasta que esa evaluación esté registrada y aceptada, `ASVS 11.5.1` se declara satisfecho **únicamente** por el generador CSPRNG de la aplicación y **no** por el UUID del borde. Si la evaluación resulta insuficiente, la tarea registra la sustitución del generador como pendiente bloqueante en lugar de dar el requisito por cubierto.
- **Rutas previstas:** `docs/security/edge-request-id-generator-assessment.md`; `tests/edge/test_substitute_generator.py`
- **Referencias:** `REQ-001-008`, `NFR-001-005`, `AC-001-015`, `AC-001-016`, `SEC-001-006`
- **Dependencias:** `TASK-001-026`, `TASK-001-028`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-015`, `TEST-001-016`
- **Verificación requerida:** `G0`, `G4`; la evidencia distingue expresamente el CSPRNG de aplicación de 16 bytes y 32 caracteres hexadecimales del UUID sustituto del borde, y no extrapola la cobertura ASVS de uno al otro.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** existe un registro fechado con el método, la fuente y el resultado de la evaluación, o una constancia explícita de que sigue pendiente y bloquea la afirmación ASVS del sustituto.
- **Estado:** `Pendiente`

### TASK-001-030 — Unificar las respuestas locales del borde con el contrato

- **Descripción:** Configurar las respuestas locales del borde para que 403 `ACCESS_DENIED`, 408 `REQUEST_TIMEOUT`, 413 `REQUEST_TOO_LARGE`, 429 `RATE_LIMITED`, 502 `UPSTREAM_UNAVAILABLE` y 504 `GATEWAY_TIMEOUT` usen el objeto cerrado `ErrorResponse`, con una sola cabecera `X-Request-ID` igual al `request_id` del cuerpo, mensajes públicos estables y sin revelar límites configurados, ventanas, claves de origen, dependencias, reglas de acceso, identidad del upstream ni cuál de las causas agrupadas se produjo. El presupuesto de emisión es de 50 ms desde el vencimiento y no consulta al upstream.
- **Rutas previstas:** `tooling/envoy/local-reply/error-response.yaml`; `tooling/envoy/local-reply/bodies/`
- **Referencias:** `REQ-001-007`, `REQ-001-010`, `REQ-001-016`, `NFR-001-006`, `AC-001-012`, `AC-001-030`, `SEC-001-003`, `SEC-001-028`
- **Dependencias:** `TASK-001-014`, `TASK-001-028`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-030`, `SEC-TEST-001-020`
- **Verificación requerida:** `G4`; cada respuesta local valida contra el contrato y ninguna añade cabeceras que permitan inferir la configuración.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** los cuerpos proceden de las respuestas reutilizables del contrato y no de un cuerpo alternativo inventado.
- **Estado:** `Pendiente`

### TASK-001-031 — Fijar los plazos de recepción y el 408 del borde

- **Descripción:** Fijar el plazo de encabezados en 1 s desde el primer byte de los encabezados y el plazo total de recepción en 2 s desde el inicio de la solicitud, con la precedencia obligatoria `encabezados 1 s ≤ recepción total 2 s`. Al vencer el plazo de encabezados el borde cancela el flujo y **no produce estado HTTP**; al vencer el plazo total sobre una solicitud cuya ruta y destino ya resolvió, el borde emite 408 `REQUEST_TIMEOUT` **sin contactar al backend**. El validador rechaza cualquier configuración invertida, y contratar el 408 sin configurar el plazo total dejaría una respuesta documentada inalcanzable.
- **Rutas previstas:** `tooling/envoy/timeouts/request-receive.yaml`; `tooling/edge/timeout-precedence.yaml`
- **Referencias:** `REQ-001-010`, `REQ-001-016`, `REQ-001-020`, `NFR-001-016`, `AC-001-030`, `AC-001-039`, `SEC-001-015`, `SEC-001-028`
- **Dependencias:** `TASK-001-030`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-030`, `TEST-001-039`
- **Verificación requerida:** `G4`; la configuración con el plazo de encabezados por encima del total se rechaza antes del arranque.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el 408 es observable sobre una coincidencia exacta de ruta y método y no dice nada de la aplicación.
- **Estado:** `Pendiente`

### TASK-001-032 — Fijar los límites de transporte de cabeceras, línea de solicitud y framing

- **Descripción:** Configurar y probar los límites de transporte que **no** son respuestas contratadas: 8 KiB por campo individual de cabecera, 16 KiB agregados de cabeceras, máximo 64 campos, 2 KiB de línea de solicitud o URI, y rechazo del framing ambiguo o inválido —`Content-Length` junto a `Transfer-Encoding`, o longitud incoherente—, que puede cerrar la conexión. Para cada límite se exige la configuración efectiva, un caso bajo el límite, el caso exacto en el límite cuando corresponda, un caso por encima, el resultado observable aprobado como política de transporte y la evidencia de que **el backend no fue alcanzado** cuando el borde debía rechazar. No se inventa ningún código fuera del contrato: estos excedentes no producen 413, porque 413 nombra el cuerpo.
- **Rutas previstas:** `tooling/envoy/limits/transport.yaml`; `tests/edge/test_transport_limits.py`; `tests/fixtures/edge/transport/`
- **Referencias:** `REQ-001-010`, `REQ-001-016`, `REQ-001-020`, `NFR-001-016`, `AC-001-030`, `AC-001-039`, `SEC-001-015`, `SEC-001-016`, `SEC-001-028`
- **Dependencias:** `TASK-001-030`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-030`, `TEST-001-039`, `SEC-TEST-001-009`
- **Verificación requerida:** `G4`; el presupuesto de cabeceras permite probar un identificador de 129 caracteres y ninguna respuesta de transporte reutiliza un código contratado.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** cada límite registra el caso bajo, el exacto cuando aplica y el superior, con constancia de que el upstream no recibió la solicitud rechazada.
- **Estado:** `Pendiente`

### TASK-001-033 — Fijar el presupuesto del cuerpo y el 413

- **Descripción:** Configurar el presupuesto de lectura de cuerpo en 1 KiB. Por encima del presupuesto el borde responde 413 `REQUEST_TOO_LARGE` con `ErrorResponse` cerrado y **no reenvía nada**; dentro del presupuesto, un cuerpo inesperado en `GET` **se acepta y se reenvía al backend**, que es quien lo consume y lo descarta, y la operación responde normalmente. Se exige la configuración efectiva, un caso bajo el límite, el caso exacto en el límite y un caso por encima, con evidencia de que el backend no fue alcanzado en el rechazo.
- **Rutas previstas:** `tooling/envoy/limits/body.yaml`; `tests/edge/test_body_budget.py`; `tests/fixtures/edge/body/`
- **Referencias:** `REQ-001-010`, `REQ-001-016`, `REQ-001-020`, `NFR-001-016`, `AC-001-030`, `AC-001-039`, `SEC-001-015`, `SEC-001-028`
- **Dependencias:** `TASK-001-030`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-030`, `TEST-001-039`
- **Verificación requerida:** `G4`; un cuerpo pequeño inesperado no produce 413 y un cuerpo excesivo no llega al upstream.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el 413 se emite solo por exceder el presupuesto del cuerpo, nunca por URI ni por cabeceras.
- **Estado:** `Pendiente`

### TASK-001-034 — Acotar la frecuencia en el borde y su 429

- **Descripción:** Configurar 20 solicitudes por segundo y por clave de origen con burst 40, un límite global de 100 por segundo y por límite HTTP, y un estado del limitador de 4096 claves máximo con TTL de 60 s, evaluado sobre el peer validado. La clave de origen permanece solo en memoria operacional acotada y nunca se registra ni se exporta como dimensión o etiqueta, y la IP no se considera identidad ni autorización de producto.
- **Rutas previstas:** `tooling/envoy/limits/rate-limit.yaml`; `tests/edge/test_rate_limit.py`
- **Referencias:** `REQ-001-018`, `REQ-001-020`, `NFR-001-014`, `NFR-001-016`, `AC-001-036`, `AC-001-039`, `SEC-001-012`, `SEC-001-015`, `SEC-001-016`
- **Dependencias:** `TASK-001-023`, `TASK-001-030`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-039`, `TEST-001-030`
- **Verificación requerida:** `G4`; el exceso produce 429 `RATE_LIMITED` sin revelar límites ni ventanas, y 429 no representa ningún vencimiento de plazo.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el rechazo por frecuencia se contabiliza aparte del rechazo de admisión y del vencimiento de plazo.
- **Estado:** `Pendiente`

### TASK-001-035 — Remapear UF, UC y UR al 502 contratado

- **Descripción:** Crear tres reglas de remapeo **separadas y explícitas**, una por indicador de resultado del adaptador, hacia 502 `UPSTREAM_UNAVAILABLE`: `UF` por fallo o timeout de conexión upstream, `UC` por terminación de la conexión upstream antes de encabezados y `UR` por reset upstream antes de encabezados. El remapeo se aplica **únicamente** cuando la causa ocurre antes de recibir encabezados válidos del upstream **y** antes de comprometer encabezados de respuesta downstream; si alguna de las dos condiciones no se cumple, el fallo posterior es transporte y no puede convertirse en 502. Queda prohibido seleccionar el código por umbrales de duración. La respuesta pública comparte código y cuerpo para las tres causas, mientras el flag de resultado y el detalle operacional conservan la causa concreta en contadores separados. `UF` se observó en el POC; `UC` y `UR` siguen siendo **comportamiento contratado no observado** y su verificación es la prueba de implementación del borde.
- **Rutas previstas:** `tooling/envoy/local-reply/upstream-failure.yaml`; `tooling/envoy/observability/upstream-cause-counters.yaml`
- **Referencias:** `REQ-001-010`, `REQ-001-016`, `AC-001-030`, `SEC-001-003`, `SEC-001-028`
- **Dependencias:** `TASK-001-030`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-030`
- **Verificación requerida:** `G4`; la selección del código depende solo del indicador de resultado del adaptador y ninguna regla depende del tiempo transcurrido.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** las tres causas producen el mismo 502 público con causa distinguible en el detalle operacional, y `UC` y `UR` quedan declaradas pendientes de verificación hasta su prueba de implementación.
- **Estado:** `Pendiente`

### TASK-001-036 — Fijar el plazo upstream total y la ociosidad del flujo

- **Descripción:** Fijar el plazo upstream total del borde en 3 s contados **desde que la solicitud downstream está completamente recibida**, como límite total del intento upstream por cancelación, y la guarda de ociosidad del flujo en 2.5 s entre eventos de lectura o escritura. El vencimiento del total con respuesta completa aún emitible produce 504 `GATEWAY_TIMEOUT` (`UT`); el vencimiento de la ociosidad **antes de comprometer encabezados** se traduce al mismo 504 contratado (`SI`). La guarda permanece **dentro** del total y nunca lo extiende: el plazo observable es `min(ociosidad, total)` y la evidencia declara cuál venció. El timeout de conexión upstream se fija en 500 ms y su fallo o vencimiento produce 502, no 504.
- **Rutas previstas:** `tooling/envoy/timeouts/upstream.yaml`; `tooling/envoy/local-reply/gateway-timeout.yaml`
- **Referencias:** `REQ-001-010`, `REQ-001-016`, `REQ-001-020`, `NFR-001-016`, `AC-001-030`, `AC-001-039`, `SEC-001-015`, `SEC-001-016`, `SEC-001-028`
- **Dependencias:** `TASK-001-030`, `TASK-001-031`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-030`, `TEST-001-039`
- **Verificación requerida:** `G4`; un upstream que gotea un byte antes de cada vencimiento de la guarda no extiende el intento más allá del plazo total, y el 504 no revela el valor del plazo ni cuál causa venció.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** un 504 no se presenta como demostración de que el deadline interno de la aplicación haya vencido.
- **Estado:** `Pendiente`

### TASK-001-037 — Preservar el 503 de la aplicación a través del borde

- **Descripción:** Excluir expresamente del remapeo el 503 `{"status":"not_ready"}` originado por la aplicación: es una respuesta upstream con encabezados propios ya recibidos y **atraviesa el borde sin alteración**, con su cuerpo mínimo, sin convertirse en `ErrorResponse` y sin transformarse en 502. El borde no genera ni reescribe ese 503.
- **Rutas previstas:** `tooling/envoy/local-reply/passthrough-503.yaml`
- **Referencias:** `REQ-001-006`, `REQ-001-010`, `REQ-001-016`, `AC-001-010`, `AC-001-011`, `AC-001-030`, `SEC-001-005`, `SEC-001-028`
- **Dependencias:** `TASK-001-011`, `TASK-001-035`, `TASK-001-036`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-030`
- **Verificación requerida:** `G4`; el cuerpo mínimo llega intacto al cliente en la entrada operacional, sin `ErrorResponse` y sin remapeo.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el 503 conserva su cuerpo mínimo y el borde no añade ni sustituye código alguno.
- **Estado:** `Pendiente`

### TASK-001-038 — Clasificar el cierre sin estado y la respuesta interrumpida

- **Descripción:** Clasificar por separado dos casos distintos. Cuando la conexión se cierra **antes** de poder emitir una respuesta no existe ningún estado HTTP: las métricas usan `status_class = transport` y `outcome = error`, los logs registran el resultado de transporte con su motivo enumerado y el request ID si existía contexto, y **ninguna evidencia puede presentarlo como 504 observado**. Cuando los encabezados ya se emitieron y la transmisión se interrumpe después, el estado ya emitido **no puede sustituirse**: el caso es un fallo de transporte con estado previo, se registran los bytes enviados frente a los declarados y el contador de flujos reiniciados, y la solicitud no cuenta como completada. No se promete ninguna retención de respuesta como forma de mantener abierta la ventana de sustitución.
- **Rutas previstas:** `tooling/envoy/observability/transport-outcomes.yaml`; `tests/edge/test_transport_outcomes.py`
- **Referencias:** `REQ-001-018`, `REQ-001-021`, `NFR-001-014`, `AC-001-030`, `AC-001-034`, `SEC-001-014`, `SEC-001-028`
- **Dependencias:** `TASK-001-035`, `TASK-001-036`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-030`, `TEST-001-034`
- **Verificación requerida:** `G4`; ningún cierre de conexión se contabiliza como 504 ni como ningún otro código.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** las dos clases de fallo de transporte se miden separadas del rechazo de borde, del rechazo de admisión, del vencimiento de recepción, del fallo upstream y del vencimiento de upstream.
- **Estado:** `Pendiente`

### TASK-001-039 — Aplicar los headers de seguridad y la política CORS

- **Descripción:** Configurar la CSP productiva aprobada, `X-Content-Type-Options`, `Referrer-Policy`, `Permissions-Policy`, `X-Frame-Options` como compatibilidad, `Content-Type` correcto y HSTS de un año solo en el límite TLS productivo, junto a CORS same-origin con allowlist cross-origin vacía, credenciales deshabilitadas, método permitido `GET`, header solicitado opcional `X-Request-ID` y preflight resuelto como protocolo sin crear operación. El catálogo usa una política separada limitada a local y CI, sin comodines ni herencia en producción.
- **Rutas previstas:** `tooling/envoy/headers/security-headers.yaml`; `tooling/envoy/headers/cors.yaml`; `tooling/envoy/headers/catalog-policy.yaml`
- **Referencias:** `REQ-001-001`, `REQ-001-002`, `AC-001-001`, `AC-001-002`, `AC-001-003`, `SEC-001-026`, `SEC-001-034`
- **Dependencias:** `TASK-001-022`, `TASK-001-030`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `SEC-TEST-001-013`
- **Verificación requerida:** `G4`, `G7`; un origen, método o header no permitido se rechaza y no se refleja un `Origin` arbitrario ni se combinan credenciales con comodín.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** los headers efectivos coinciden con la política documentada en la build de producción.
- **Estado:** `Pendiente`

### TASK-001-040 — Implementar los productores reales de eventos del borde

- **Descripción:** Emitir al canal obligatorio, desde el borde, las clases reales de evento que le corresponden: acceso operacional denegado, candidato de correlación inválido registrado **sin** el valor hostil y recurso o destino externo bloqueado. Los eventos usan la lista permitida de campos, conservan el identificador ya resuelto y no incorporan la clave de origen, la IP ni ningún identificador único.
- **Rutas previstas:** `tooling/envoy/observability/security-events.yaml`; `tests/edge/test_edge_event_producers.py`
- **Referencias:** `REQ-001-009`, `REQ-001-021`, `NFR-001-006`, `AC-001-040`, `SEC-001-008`, `SEC-001-010`, `SEC-001-029`, `SEC-001-034`
- **Dependencias:** `TASK-001-023`, `TASK-001-024`, `TASK-001-028`, `TASK-001-034`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-040`, `SEC-TEST-001-011`
- **Verificación requerida:** `G4`; las tres clases se observan como eventos reales del borde y ninguna contiene el candidato hostil ni la dirección del peer.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** los eventos del borde llegan al canal separado y se contabilizan como obligatorios, no como logs ordinarios.
- **Estado:** `Pendiente`

### Fase 5: Sistema visual, frontend y verificación de interfaz

### TASK-001-041 — Trasladar los tokens aprobados y configurar Tailwind sobre variables semánticas

- **Descripción:** Inventariar **todos** los tokens de `ui-design.md` 1.0.0 —colores semánticos de superficie, navegación, texto, bordes, foco, acento, enlaces, disabled y estados semánticos; tipografía y escala; espaciado y tokens de gap; tamaños; bordes, radios, sombras y elevación; breakpoints, gutters y columnas; y motion— y transformarlos sin reinterpretación en variables CSS y en su exposición tipada de `packages/ui`. Tailwind se configura para consumir **exclusivamente** esas variables semánticas aprobadas: queda prohibida toda paleta propia, valor cromático paralelo, utilidad arbitraria que sustituya un token y estilo duplicado. `Inter` se empaqueta y sirve localmente en los pesos 400, 500, 600 y 700, sin CDN ni destino externo, con licencia, origen y hashes inventariados y el fallback stack aprobado.
- **Rutas previstas:** `packages/ui/src/tokens/tokens.css`; `packages/ui/src/tokens/tokens.ts`; `packages/ui/tailwind.preset.ts`; `packages/ui/src/fonts/inter/`; `tooling/design/verify-token-parity.ts`
- **Referencias:** `REQ-001-022`, `REQ-001-026`, `REQ-001-028`, `NFR-001-020`, `AC-001-043`, `AC-001-050`, `SEC-001-021`, `SEC-001-034`, `SEC-001-036`
- **Dependencias:** `TASK-001-004`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-043`, `TEST-001-050`, `SEC-TEST-001-028`
- **Verificación requerida:** `G2`, `G3`; la comparación mecánica de nombre, valor, categoría y versión falla ante cualquier divergencia y ante cualquier valor cromático definido fuera del inventario.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** ninguna categoría mínima queda sin valores concretos, semántica, aplicabilidad y versión, o sin su justificación expresa de no aplicabilidad.
- **Estado:** `Pendiente`

### TASK-001-042 — Construir las primitivas de layout Container, Stack, Inline, Cluster y Grid

- **Descripción:** Construir las **cinco** primitivas de layout aprobadas: `Container` con modos `reading` de 720 px y `application` de 1600 px, `Stack` para el eje vertical, `Inline` para alinear elementos relacionados con wrap, `Cluster` para agrupar acciones y `Grid` sobre las columnas documentadas por breakpoint. Son semánticamente neutras, no reciben foco, no introducen color propio, no alteran el orden DOM, no sustituyen landmarks y no habilitan superficies de datos fluidas sin ancho máximo. No se añade ninguna primitiva fuera del conjunto aprobado.
- **Rutas previstas:** `packages/ui/src/primitives/Container.tsx`; `packages/ui/src/primitives/Stack.tsx`; `packages/ui/src/primitives/Inline.tsx`; `packages/ui/src/primitives/Cluster.tsx`; `packages/ui/src/primitives/Grid.tsx`; `packages/ui/tests/primitives.test.tsx`
- **Referencias:** `REQ-001-022`, `REQ-001-023`, `REQ-001-024`, `NFR-001-019`, `NFR-001-020`, `AC-001-044`, `AC-001-045`, `AC-001-047`, `SEC-001-034`, `SEC-001-036`
- **Dependencias:** `TASK-001-041`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-044`, `TEST-001-045`, `TEST-001-047`
- **Verificación requerida:** `G3`; los gaps proceden de la escala aprobada, no existen márgenes arbitrarios y ninguna primitiva fuerza estados que su contrato marca N/A.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** las cinco primitivas existen con sus modos y gaps aprobados y ninguna duplica otra.
- **Estado:** `Pendiente`

### TASK-001-043 — Construir Button, Language selector, Status indicator y Alert/message

- **Descripción:** Construir los cuatro componentes restantes del conjunto mínimo aprobado con su anatomía, variantes, tamaños y tokens exactos: `Button` con `primary`, `secondary` y `quiet` y tamaños `default` de 44 px y `large` de 48 px, sin variante destructiva; `Language selector` con variantes `on-light` y `on-navigation`, control de selección nativo, etiqueta visible localizada, nombres completos de idioma, sin banderas ni siglas y altura de 44 px con mínimo de 144 px; `Status indicator` con `neutral`, `checking`, `available`, `degraded` y `unavailable`, no interactivo y con etiqueta textual obligatoria; y `Alert/message` con `info`, `success`, `warning` y `danger`, borde semántico y cuerpo obligatorio. El foco visible usa 3 px de grosor y 2 px de separación con `color.focus.on-light` o `color.focus.on-dark` según la superficie adyacente, inmediato y sin animación. No se añade ningún componente fuera del conjunto aprobado.
- **Rutas previstas:** `packages/ui/src/components/Button.tsx`; `packages/ui/src/components/LanguageSelector.tsx`; `packages/ui/src/components/StatusIndicator.tsx`; `packages/ui/src/components/Alert.tsx`; `packages/ui/tests/components.test.tsx`
- **Referencias:** `REQ-001-023`, `REQ-001-024`, `NFR-001-018`, `NFR-001-020`, `AC-001-044`, `AC-001-045`, `AC-001-046`, `SEC-001-034`, `SEC-001-036`
- **Dependencias:** `TASK-001-042`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-044`, `TEST-001-045`, `TEST-001-046`
- **Verificación requerida:** `G3`; cada componente consume solo tokens compartidos, expone semántica nativa y nombre accesible y nunca comunica estado solo con color.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** los cuatro componentes y las cinco primitivas completan el conjunto mínimo de cinco capacidades aprobadas, sin añadidos.
- **Estado:** `Pendiente`

### TASK-001-044 — Crear el catálogo ejecutable con variantes, estados y N/A justificados

- **Descripción:** Crear el catálogo Storybook ejecutable local y en CI **sobre la base Node `24.21.0-trixie-slim` ya fijada para el builder Vite**, con la misma versión de diseño, historias versionadas del inventario de tokens, de cada componente mínimo, de cada variante, de la matriz completa de estados `default`, `hover`, `focus-visible`, `active`, `disabled`, `loading`, `success`, `warning` y `error` con cada N/A justificado expresamente, un ejemplo ejecutable por estado aplicable, ejemplos en español e inglés con expansión y pseudolocalización, y la variante `on-navigation` del selector junto al token `color.focus.on-dark`, que no pueden quedar sin ejemplo. Los addons se limitan a los imprescindibles inventariados, los recursos son locales y el catálogo queda excluido de preview, staging y del artefacto productivo; no introduce otra base OCI externa.
- **Rutas previstas:** `packages/ui/.storybook/main.ts`; `packages/ui/.storybook/preview.ts`; `packages/ui/src/tokens/tokens.stories.tsx`; `packages/ui/src/primitives/*.stories.tsx`; `packages/ui/src/components/*.stories.tsx`; `docs/design/state-matrix.md`
- **Referencias:** `REQ-001-024`, `REQ-001-025`, `NFR-001-020`, `AC-001-045`, `AC-001-049`, `SEC-001-019`, `SEC-001-020`, `SEC-001-033`, `SEC-001-034`, `SEC-001-036`
- **Dependencias:** `TASK-001-043`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-045`, `TEST-001-049`, `SEC-TEST-001-023`, `SEC-TEST-001-024`
- **Verificación requerida:** `G5`; una omisión implícita de estado, un ejemplo ausente o una referencia que no resuelve contra un contrato existente de la misma versión hacen fallar la validación.
- **Evidencia final a cargo:** `TEST-001-045`
- **Criterio observable de cierre:** cada estado aplicable tiene ejemplo ejecutable y cada N/A su justificación registrada, sin navegación ni datos de negocio.
- **Estado:** `Pendiente`

### TASK-001-045 — Componer la pantalla inicial con la franja de navegación aprobada

- **Descripción:** Componer la pantalla inicial con dos regiones construidas solo con tokens y primitivas aprobadas. La franja sólida se implementa como `<header>` semántico con `Container` en modo `application` y `Inline`/`Cluster`, su superficie usa `color.navigation` opaca y sin transparencia, contiene el wordmark tipográfico `SimuCenter` en `Inter` 700, 24/32 px, tracking `-0.01em` y color `text.inverse`, mientras los demás textos e iconos de la franja usan `navigation.text`; los dos tokens conviven y no son intercambiables. El selector de idioma se sitúa sobre la navegación con su variante `on-navigation` y todo control enfocado en esa superficie dibuja su anillo con `color.focus.on-dark`. El bloque de lectura usa `Container` en modo `reading` con el mensaje localizado y el indicador de estado técnico. No se crea componente de navegación nuevo, sistema visual paralelo, librería adicional, destinos, menús, agrupaciones, login, dashboard ni datos simulados.
- **Rutas previstas:** `apps/frontend/src/app/InitialScreen.tsx`; `apps/frontend/src/app/AppHeader.tsx`; `apps/frontend/src/app/ReadingBlock.tsx`; `apps/frontend/src/main.tsx`
- **Referencias:** `REQ-001-002`, `REQ-001-022`, `REQ-001-023`, `REQ-001-024`, `REQ-001-028`, `NFR-001-019`, `AC-001-002`, `AC-001-003`, `AC-001-044`, `AC-001-047`, `SEC-001-026`, `SEC-001-032`, `SEC-001-034`, `SEC-001-036`
- **Dependencias:** `TASK-001-042`, `TASK-001-043`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-002`, `TEST-001-003`, `TEST-001-044`, `TEST-001-047`
- **Verificación requerida:** `G3`, `G5`; la pantalla consume los componentes compartidos sin estilos paralelos y la franja no incorpora navegación de negocio.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la identidad, el wordmark, la franja, el selector sobre navegación y el bloque de lectura usan exactamente los tokens aprobados.
- **Estado:** `Pendiente`

### TASK-001-046 — Resolver el idioma ES/EN sin persistencia

- **Descripción:** Configurar i18next y react-i18next con catálogos completos en español e inglés, resolución por navegador para español o inglés incluidas sus variantes regionales, fallback a inglés para idioma no soportado, indeterminado o traducción ausente, y selección manual que vive **únicamente** en el estado actual de la página: una recarga descarta la elección y vuelve a resolver la regla aprobada. No se introduce `localStorage`, `sessionStorage`, IndexedDB, cookies, almacenamiento de servidor, preferencia de usuario ni preferencia de tenant, y no se hace ninguna petición a un proveedor de traducción. El cambio de idioma no pierde foco ni estado visible y actualiza el idioma del documento; la expansión general admite al menos 30 % y las etiquetas breves hasta 100 %, con wrap permitido y sin truncar acciones ni instrucciones ni reducir tipografía.
- **Rutas previstas:** `apps/frontend/src/i18n/config.ts`; `apps/frontend/src/i18n/locales/es.json`; `apps/frontend/src/i18n/locales/en.json`; `apps/frontend/src/i18n/resolve-language.ts`; `apps/frontend/tests/i18n.test.ts`
- **Referencias:** `REQ-001-002`, `REQ-001-012`, `NFR-001-003`, `AC-001-022`, `AC-001-023`, `AC-001-024`, `AC-001-025`, `SEC-001-027`, `SEC-001-036`
- **Dependencias:** `TASK-001-045`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-022`, `TEST-001-023`, `TEST-001-024`, `TEST-001-025`
- **Verificación requerida:** `G3`, `G7`; la verificación de cobertura detecta claves ausentes y ninguna clave interna queda visible al usuario.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el 100 % de las cadenas estáticas visibles procede de los catálogos y tiene entrada en ambos idiomas.
- **Estado:** `Pendiente`

### TASK-001-047 — Consultar health desde el navegador

- **Descripción:** Implementar la consulta de `GET /health` al montar y cada 30 s mientras la página está visible, con **una sola solicitud en vuelo**, `credentials: omit`, **sin enviar cabeceras innecesarias**, **sin generar ni enviar artificialmente `X-Request-ID`**, **sin caché**, timeout de cliente de 4 s y sin reintentos inmediatos. El polling se pausa en pestaña oculta, se reanuda con una única consulta sin avalancha y **se cancela al desmontar** el componente. Cualquier respuesta distinta de 200 y cualquier fallo de conexión o vencimiento del timeout se presentan como un **único estado seguro de indisponibilidad**, sin mostrar código, cuerpo, causa ni detalles de red, sin inferir readiness ni capacidad de negocio y sin anunciar repetidamente el mismo estado. El idioma activo no se persiste y el frontend nunca obtiene ruta ni credencial para readiness. El timeout de 4 s es una decisión del cliente y no se presenta como garantía del borde.
- **Rutas previstas:** `apps/frontend/src/health/client.ts`; `apps/frontend/src/health/useHealthProbe.ts`; `apps/frontend/tests/health-probe.test.ts`
- **Referencias:** `REQ-001-002`, `REQ-001-005`, `NFR-001-012`, `NFR-001-017`, `AC-001-003`, `SEC-001-004`, `SEC-001-026`, `SEC-001-027`
- **Dependencias:** `TASK-001-022`, `TASK-001-045`, `TASK-001-046`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-003`
- **Verificación requerida:** `G3`, `G7`; una prueba inspecciona **la solicitud realmente emitida por el navegador** y comprueba método, ruta same-origin, ausencia de credenciales, ausencia de `X-Request-ID` artificial, ausencia de cabeceras innecesarias, directivas de no caché, cancelación al desmontar y frecuencia aprobada.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la solicitud observada coincide exactamente con la consulta aprobada y ningún estado de indisponibilidad revela detalles internos.
- **Estado:** `Pendiente`

### TASK-001-048 — Limitar el diagnóstico del frontend

- **Descripción:** Implementar el adaptador de diagnóstico deshabilitado por defecto, con lista permitida de nombre estable de evento, componente, severidad y estado técnico acotado, que excluye cuerpos y cabeceras de solicitudes y respuestas, tokens, cookies, secretos, identificadores, URLs con query, stack traces mostrados al usuario y detalles internos, sin telemetría remota obligatoria y sin llamadas de consola en producción fuera del mecanismo documentado.
- **Rutas previstas:** `apps/frontend/src/diagnostics/adapter.ts`; `apps/frontend/src/diagnostics/allowlist.ts`; `apps/frontend/tests/diagnostics.test.ts`
- **Referencias:** `REQ-001-009`, `NFR-001-006`, `NFR-001-012`, `AC-001-019`, `SEC-001-027`
- **Dependencias:** `TASK-001-045`, `TASK-001-047`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-019`, `SEC-TEST-001-021`
- **Verificación requerida:** `G3`, `G7`; el adaptador conserva solo campos permitidos y funciona sin proveedor ni envío remoto.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la build de producción no emite consola fuera del mecanismo documentado.
- **Estado:** `Pendiente`

### TASK-001-049 — Preparar el arnés Playwright y los contextos fijados

- **Descripción:** Preparar el arnés Playwright sobre la imagen oficial `mcr.microsoft.com/playwright:v1.63.0-noble` fijada por digest hijo `linux/amd64`; verificar y documentar el Node efectivo dentro de esa imagen y asegurar Node `24.21.0` para toda herramienta del proyecto. Fijar contextos por navegador, versión, sistema operativo o plataforma, viewport, escala y versión de fuente, reloj y datos congelados, espera de fuentes y layout, desactivación del motion no esencial, viewports canónicos `375×812`, `768×1024`, `1280×800` y `1920×1080` más `320 px` y los bordes `599/600`, `1023/1024` y `1439/1440`, e interceptores de consola, red, DOM y bundles con centinelas sintéticos.
- **Rutas previstas:** `tests/e2e/playwright.config.ts`; `tests/e2e/fixtures/browser-context.ts`; `tests/e2e/fixtures/sentinels.ts`; `tests/e2e/fixtures/viewports.ts`; `docs/evidence/browsers/playwright-runtime.md`
- **Referencias:** `REQ-001-011`, `NFR-001-011`, `NFR-001-019`, `AC-001-021`, `AC-001-028`, `SEC-001-019`, `SEC-001-031`
- **Dependencias:** `TASK-001-044`, `TASK-001-045`, `TASK-001-046`, `TASK-001-047`, `TASK-001-048`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-021`
- **Verificación requerida:** `G7`; el arnés usa exclusivamente datos sintéticos y registra navegador, versión, plataforma y viewport en cada ejecución.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** dos ejecuciones sin cambios producen el mismo contexto declarado y el mismo conjunto de casos; la evidencia registra el Node efectivo del contenedor y comprueba Node `24.21.0` para las herramientas del proyecto.
- **Estado:** `Pendiente`

### TASK-001-050 — Cerrar el E2E de idioma, selector, indisponibilidad y solicitud real

- **Descripción:** Ejecutar y cerrar el E2E de la pantalla inicial sobre el arnés completo: identidad y alcance de la pantalla sin login, navegación de negocio, dashboard ni datos simulados; estado comprensible y localizado cuando el backend no está disponible, con el selector disponible y sin detalles de red; selección automática de español y de inglés incluidas variantes regionales; fallback a inglés; cambio completo mediante el selector sin perder foco; e inspección de la **solicitud real emitida por el navegador** hacia health.
- **Rutas previstas:** `tests/e2e/specs/initial-screen.spec.ts`; `tests/e2e/specs/language.spec.ts`; `tests/e2e/specs/unavailable.spec.ts`; `tests/e2e/specs/health-request.spec.ts`
- **Referencias:** `REQ-001-002`, `REQ-001-012`, `NFR-001-003`, `AC-001-002`, `AC-001-003`, `AC-001-022`, `AC-001-023`, `AC-001-024`, `AC-001-025`, `SEC-001-026`, `SEC-001-027`, `SEC-001-032`
- **Dependencias:** `TASK-001-047`, `TASK-001-049`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-002`, `TEST-001-003`, `TEST-001-022`, `TEST-001-023`, `TEST-001-024`, `TEST-001-025`
- **Verificación requerida:** `G7`; ninguna clave interna aparece, ningún texto queda vacío y la indisponibilidad no infiere readiness.
- **Evidencia final a cargo:** `TEST-001-002`, `TEST-001-003`, `TEST-001-022`, `TEST-001-023`, `TEST-001-024`, `TEST-001-025`
- **Criterio observable de cierre:** los seis casos pasan sobre los componentes, el selector, la pantalla, la consulta de health y el estado de indisponibilidad reales.
- **Estado:** `Pendiente`

### TASK-001-051 — Cerrar el diagnóstico controlado del frontend

- **Descripción:** Ejecutar y cerrar, sobre una build de producción, la verificación del diagnóstico con errores y eventos representativos que incluyen datos sensibles sintéticos: cuerpo, token, URL con query y detalle interno. La evidencia inspecciona consola, red, DOM y bundle y demuestra que el diagnóstico conserva solo datos permitidos y funciona sin proveedor ni envío remoto.
- **Rutas previstas:** `tests/e2e/specs/diagnostics.spec.ts`; `tests/e2e/fixtures/diagnostic-events.ts`
- **Referencias:** `REQ-001-009`, `NFR-001-006`, `NFR-001-012`, `AC-001-019`, `SEC-001-027`
- **Dependencias:** `TASK-001-048`, `TASK-001-049`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-019`, `SEC-TEST-001-021`
- **Verificación requerida:** `G7`; ningún centinela aparece en consola, red, DOM ni bundle y ninguna consola no autorizada lo reproduce.
- **Evidencia final a cargo:** `TEST-001-019`, `SEC-TEST-001-021`
- **Criterio observable de cierre:** la build de producción no emite ningún dato prohibido por el adaptador.
- **Estado:** `Pendiente`

### TASK-001-052 — Cerrar los componentes compartidos usados por la pantalla inicial

- **Descripción:** Ejecutar y cerrar la inspección de dependencias visuales y el ejercicio de la interfaz inicial: existen como componentes compartidos `Button`, `Language selector`, `Status indicator`, `Alert/message` y las primitivas de layout `Container`, `Stack`, `Inline`, `Cluster` y `Grid`; todos consumen los tokens compartidos; y la pantalla inicial usa esos componentes cuando la capacidad correspondiente está presente, sin estilos, tokens ni primitivas paralelas.
- **Rutas previstas:** `packages/ui/tests/shared-contract.test.tsx`; `apps/frontend/tests/shared-components.test.tsx`
- **Referencias:** `REQ-001-023`, `NFR-001-020`, `AC-001-044`, `SEC-001-034`, `SEC-001-036`
- **Dependencias:** `TASK-001-043`, `TASK-001-045`, `TASK-001-049`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-044`
- **Verificación requerida:** `G3`, `G5`; una capacidad resuelta con un estilo propio en lugar del componente compartido hace fallar la prueba.
- **Evidencia final a cargo:** `TEST-001-044`
- **Criterio observable de cierre:** las cinco capacidades mínimas se resuelven con el sistema compartido y ninguna se duplica.
- **Estado:** `Pendiente`

### TASK-001-053 — Verificar la accesibilidad automatizada y la revisión manual dirigida

- **Descripción:** Ejecutar axe-core sobre la pantalla inicial en español e inglés, con backend disponible y no disponible, y sobre cada componente interactivo en todas sus variantes y estados aplicables, además de la revisión manual dirigida por teclado que comprueba alcance y operación de todos los controles, nombre accesible, foco visible, orden documentado y ausencia de trampas de foco. El contraste se mide **sobre el render real**, incluido el par del wordmark `text.inverse` sobre `color.navigation` frente a su umbral aplicable, y el de `navigation.text` sobre `color.navigation` y sus estados de navegación; ningún resultado de una variante o estado se extrapola a los demás.
- **Rutas previstas:** `tests/a11y/axe-initial-screen.spec.ts`; `tests/a11y/axe-components.spec.ts`; `tests/a11y/contrast.spec.ts`; `tests/a11y/keyboard-review.md`; `docs/evidence/a11y/`
- **Referencias:** `REQ-001-002`, `REQ-001-013`, `REQ-001-023`, `REQ-001-024`, `NFR-001-004`, `NFR-001-018`, `AC-001-026`, `AC-001-027`, `AC-001-046`, `SEC-001-034`, `SEC-001-035`, `SEC-001-036`
- **Dependencias:** `TASK-001-044`, `TASK-001-049`, `TASK-001-050`, `TASK-001-052`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-026`, `TEST-001-027`, `TEST-001-046`
- **Verificación requerida:** `G6`; la revisión automatizada no reporta violaciones en ningún estado ni idioma y el contraste medido no baja del umbral aplicable.
- **Evidencia final a cargo:** `TEST-001-026`, `TEST-001-027`, `TEST-001-046`
- **Criterio observable de cierre:** la evidencia registra el par medido, el valor y el umbral, y la revisión manual queda documentada por caso.
- **Estado:** `Pendiente`

### TASK-001-054 — Validar el reflow y los breakpoints

- **Descripción:** Ejecutar y cerrar la verificación responsive en cada breakpoint aprobado y en los tamaños intermedios representativos, con contenido expandido: no se pierde contenido, no existen solapamientos ni desbordamientos que impidan el uso, se conserva la operabilidad, el orden DOM y de foco no cambia de forma distinta a la documentada y no aparece scroll horizontal accidental. La franja de navegación, el selector `on-navigation` y el bloque de lectura entran en la cobertura obligatoria.
- **Rutas previstas:** `tests/e2e/specs/responsive.spec.ts`; `docs/evidence/responsive/`
- **Referencias:** `REQ-001-022`, `REQ-001-023`, `REQ-001-024`, `REQ-001-028`, `NFR-001-019`, `AC-001-047`, `SEC-001-034`, `SEC-001-036`
- **Dependencias:** `TASK-001-049`, `TASK-001-050`, `TASK-001-052`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-047`
- **Verificación requerida:** `G7`; los máximos por breakpoint y los máximos de modo de `Container` se comprueban como contratos distintos y ambos aplican.
- **Evidencia final a cargo:** `TEST-001-047`
- **Criterio observable de cierre:** cada breakpoint y cada borde documentado tiene evidencia de reflow sin pérdida de contenido ni de operabilidad.
- **Estado:** `Pendiente`

### TASK-001-055 — Cubrir la matriz de navegadores y plataformas

- **Descripción:** Ejecutar las verificaciones de carga, localización, selector de idioma, indisponibilidad del backend y navegación por teclado aplicable en la versión estable actual y la anterior de Chrome, Edge, Firefox y Safari desktop, y en la versión estable actual de Safari en iOS y Chrome en Android, registrando navegador, versión, plataforma, arquitectura, escala, viewport y fecha. Sobre esa misma matriz se ejecuta la selección representativa y documentada de escenarios de componentes, estados y breakpoints. WebKit no se equipara a Safari ni la emulación móvil a un navegador real: donde Playwright no controle el navegador requerido, el procedimiento manual dirigido registra cada caso y su captura nativa, y la falta de entorno o evidencia bloquea la aceptación en lugar de marcarse como pasada.
- **Rutas previstas:** `tests/e2e/specs/browser-matrix.spec.ts`; `tests/e2e/specs/visual-system-matrix.spec.ts`; `docs/evidence/browser-matrix.md`
- **Referencias:** `REQ-001-011`, `REQ-001-023`, `REQ-001-024`, `NFR-001-011`, `NFR-001-019`, `AC-001-028`, `AC-001-048`, `SEC-001-034`, `SEC-001-036`
- **Dependencias:** `TASK-001-053`, `TASK-001-054`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-028`, `TEST-001-048`
- **Verificación requerida:** `G7`; Internet Explorer y navegadores obsoletos no forman parte de la matriz y la cobertura del sistema visual extiende la matriz sin duplicar infraestructura.
- **Evidencia final a cargo:** `TEST-001-028`, `TEST-001-048`
- **Criterio observable de cierre:** cada contexto de la matriz registra su evidencia ejecutada, no una declaración documental.
- **Estado:** `Pendiente`

### TASK-001-056 — Validar la regresión visual reproducible

- **Descripción:** Ejecutar la regresión visual con baselines versionados por contexto exacto y vinculados a la revisión de código, con umbral inicial de cero píxeles diferentes dentro del mismo contexto y sin enmascarar controles, cubriendo el inventario componente × variante × estado aplicable × breakpoint más idioma y expansión relevantes. Dos ejecuciones sin cambios producen resultados consistentes; una mutación visual controlada se detecta; una mutación de teclado, semántica o seguridad con captura idéntica hace fallar un gate independiente; y toda actualización o promoción de baseline exige revisión explícita vinculada al PR. El manifest de evidencia enlaza versión y hash del diseño, token, componente e historia, commit revisado, ejecución, contexto, hashes de baseline, actual y diff, resultado y aprobación; los snapshots, diffs y metadatos se inspeccionan con centinelas sintéticos y se complementan con revisión documentada cuando la imagen no pueda analizarse íntegramente.
- **Rutas previstas:** `tests/visual/visual-regression.spec.ts`; `tests/visual/coverage-inventory.ts`; `tests/visual/baselines/`; `docs/evidence/visual-manifest.json`
- **Referencias:** `REQ-001-027`, `NFR-001-018`, `NFR-001-021`, `AC-001-051`, `AC-001-052`, `SEC-001-019`, `SEC-001-023`, `SEC-001-033`, `SEC-001-035`
- **Dependencias:** `TASK-001-044`, `TASK-001-055`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-051`, `TEST-001-052`, `SEC-TEST-001-019`, `SEC-TEST-001-024`, `SEC-TEST-001-027`
- **Verificación requerida:** `G7`; una actualización de baseline sin revisión vinculada falla, y un snapshot coincidente con un fallo funcional, de teclado, de accesibilidad o de seguridad conserva el resultado global no satisfactorio.
- **Evidencia final a cargo:** `TEST-001-051`, `TEST-001-052`, `SEC-TEST-001-019`, `SEC-TEST-001-024`, `SEC-TEST-001-027`
- **Criterio observable de cierre:** los resultados visuales se informan separados de los funcionales, de teclado, de accesibilidad y de seguridad.
- **Estado:** `Pendiente`

### TASK-001-057 — Cerrar la concordancia diseño–catálogo–implementación y la no duplicación

- **Descripción:** Ejecutar y cerrar el control mecánico de concordancia entre `ui-design.md` 1.0.0, el catálogo y la implementación para tokens, componentes, variantes, estados, comportamiento responsive, interacción y excepciones de reutilización, junto al control automatizado de definiciones prohibidas, imports, tokens o primitivas paralelas y valores cromáticos fuera del inventario, y la revisión estructural documentada de las equivalencias conceptuales o de interacción. Una excepción solo se acepta si registra necesidad, justificación, alcance y criterio para eliminarla o incorporarla al sistema compartido. Aquí se cierra además el inventario y el contrato de tokens contra las categorías obligatorias de `SPEC-001`.
- **Rutas previstas:** `tooling/design/verify-design-concordance.ts`; `tooling/lint/eslint-no-parallel-styles.js`; `packages/ui/tests/token-inventory.test.ts`; `docs/design/reuse-exceptions.md`
- **Referencias:** `REQ-001-022`, `REQ-001-023`, `REQ-001-024`, `REQ-001-025`, `REQ-001-026`, `REQ-001-027`, `REQ-001-028`, `NFR-001-018`, `NFR-001-019`, `NFR-001-020`, `NFR-001-021`, `AC-001-043`, `AC-001-050`, `SEC-001-021`, `SEC-001-034`, `SEC-001-036`
- **Dependencias:** `TASK-001-041`, `TASK-001-044`, `TASK-001-045`, `TASK-001-056`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-043`, `TEST-001-050`, `SEC-TEST-001-028`
- **Verificación requerida:** `G2`, `G5`; una divergencia mecánica, una duplicación detectable o una excepción incompleta bloquean el gate.
- **Evidencia final a cargo:** `TEST-001-043`, `TEST-001-050`, `SEC-TEST-001-028`
- **Criterio observable de cierre:** el inventario cubre todas las categorías mínimas con valores, semántica, aplicabilidad y versión, y ninguna definición depende de nombres de un framework o librería.
- **Estado:** `Pendiente`

### Fase 6: Contenedores, entorno ejecutable y cierres operacionales

### TASK-001-058 — Construir las imágenes OCI mínimas

- **Descripción:** Construir backend multietapa con builder y runtime desde `docker.io/library/python:3.14.7-slim-trixie`: las herramientas de compilación temporales quedan solo en builder. Construir Vite con builder `docker.io/library/node:24.21.0-trixie-slim` y copiar únicamente Vite e Inter a la imagen final `web-static`, derivada de `docker.io/library/nginx:<VERSION_ESTABLE_EXACTA>-trixie` fijada en `TASK-001-001`. Envoy conserva imagen separada como único borde, sin filtro `file_server`; el Collector conserva su base resuelta. Todas las imágenes de entrega ejecutan con usuario numérico no root, filesystem raíz de solo lectura, capacidades eliminadas, `no-new-privileges`, tmpfs acotado para temporales, sin socket del motor, montajes amplios del host, herramientas de desarrollo o secretos en imagen, capas, metadata o configuración frontend. En `web-static`: puerto interno no privilegiado, un worker, autoindex apagado, `GET`/`HEAD` únicamente, logs stdout/stderr sin secretos, ninguna función dinámica innecesaria ni proxy al backend. Storybook, pruebas y fuentes no necesarias quedan fuera de imágenes productivas.
- **Rutas previstas:** `apps/backend/Dockerfile`; `apps/frontend/Dockerfile`; `tooling/oci/edge.Dockerfile`; `tooling/oci/web-static.Dockerfile`; `tooling/oci/collector.Dockerfile`; `tooling/nginx/nginx.conf`; `tooling/nginx/static.conf`; `tests/security/test_image_inspection.py`; `tests/security/test_web_static_isolation.py`
- **Referencias:** `REQ-001-014`, `NFR-001-008`, `NFR-001-009`, `AC-001-029`, `AC-001-031`, `AC-001-032`, `SEC-001-021`, `SEC-001-025`
- **Dependencias:** `TASK-001-013`, `TASK-001-022`, `TASK-001-045`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-029`, `SEC-TEST-001-016`, `SEC-TEST-001-003`
- **Verificación requerida:** `G8`; inspeccionar las cuatro imágenes centrales y sus bases hijas `linux/amd64`; fallar ante usuario root efectivo, privilegios indebidos, capacidades innecesarias, compiladores en runtime, secreto centinela, archivos fuente o catálogo en `web-static`, rootfs escribible sin justificación, autoindex habilitado o filtro `file_server` de Envoy.
- **Evidencia final a cargo:** `SEC-TEST-001-016`
- **Criterio observable de cierre:** el usuario efectivo, permisos, capas, contenido estático, puerto interno, único worker y `tmpfs` necesarios se inspeccionan; `web-static` no tiene superficie API y ningún artefacto con privilegios indebidos es entregable.
- **Estado:** `Pendiente`

### TASK-001-059 — Verificar la cuota efectiva de runtime y OCI

- **Descripción:** Declarar e inspeccionar automáticamente los límites **efectivos** del contenedor y runtime de los cuatro servicios centrales, no solo su documentación: backend **1 vCPU**, **512 MiB**, un worker por réplica y 128 PIDs; edge Envoy 0.5 vCPU y 128 MiB; `web-static` **0.25 vCPU**, **64 MiB** y un worker; Collector 0.5 vCPU y 256 MiB; generador de carga separado del backend; y techo de buffers de 16 MiB por proceso backend y `P × 16 MiB` agregado. Contrastar las cuotas efectivas con `P = R × W`, `activos_total = P × C`, `espera_total = P × Q`, `conexiones_total = R × K`, `canales_OTLP = P`, `exports_en_vuelo = 2 × P`, `buffers_backend_MiB = P × (L + T)`, `mem_backend_MiB = R × 512`, `CPU_runtime = R × 1 + E × 0.5 + S × 0.25 + O × 0.5` y `RAM_runtime_MiB = R × 512 + E × 128 + S × 64 + O × 256`, con E, S y O réplicas Envoy, `web-static` y Collector inicialmente iguales a 1. El gate **falla si cualquier cuota efectiva diverge**, si un worker excede lo aprobado sin revisión o si el consumo backend por proceso o agregado supera su techo aunque la memoria total lo admita.
- **Rutas previstas:** `tooling/oci/runtime-limits.yaml`; `scripts/ci/verify-runtime-quota`; `apps/backend/src/platform_foundation/infrastructure/config/budget.py`; `apps/backend/tests/integration/test_effective_quota.py`
- **Referencias:** `REQ-001-014`, `REQ-001-020`, `NFR-001-008`, `NFR-001-016`, `AC-001-029`, `AC-001-038`, `SEC-001-015`, `SEC-001-016`, `SEC-001-025`, `SEC-001-030`
- **Dependencias:** `TASK-001-021`, `TASK-001-058`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-029`, `TEST-001-038`
- **Verificación requerida:** `G4`, `G8`; documentar el presupuesto no basta: la comprobación lee la cuota efectiva y rechaza la divergencia antes del arranque.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la cuota efectiva de CPU, memoria, PIDs y workers de los cuatro servicios coincide con la declarada, incluido `web-static` con 0.25 vCPU, 64 MiB y un worker; el techo de buffers backend se comprueba por proceso y en agregado.
- **Estado:** `Pendiente`

### TASK-001-060 — Ensamblar el entorno local con Compose

- **Descripción:** Ensamblar el entorno local reproducible de **cuatro servicios centrales**: backend, edge Envoy, `web-static` NGINX y Collector. Solo el listener público de Envoy se publica en loopback; el operacional permanece en red privada. Backend, `web-static` y OTLP carecen de puertos publicados, y solo Envoy alcanza `web-static` por una red interna dedicada o segmentación equivalente. El orden público excluye `/ready` antes del catch-all SPA; health exacto llega al backend y los estáticos a `web-static`. Credenciales TLS operacionales montadas solo en procesos que las necesitan; perfiles explícitos y apagados para catálogo y carga, imágenes por digest hijo `linux/amd64` y plataforma explícita, sin volumen de datos de aplicación ni servicio de DB/cache.
- **Rutas previstas:** `tooling/compose/compose.yaml`; `tooling/compose/networks.yaml`; `tooling/compose/profiles/catalog.yaml`; `tooling/compose/profiles/load.yaml`; `scripts/dev/up.sh`; `scripts/dev/down.sh`; `tests/operational/test_web_static_network.py`
- **Referencias:** `REQ-001-003`, `REQ-001-014`, `REQ-001-015`, `NFR-001-001`, `NFR-001-008`, `NFR-001-010`, `AC-001-005`, `AC-001-029`, `SEC-001-002`, `SEC-001-025`, `SEC-001-029`, `SEC-001-033`
- **Dependencias:** `TASK-001-021`, `TASK-001-023`, `TASK-001-039`, `TASK-001-058`, `TASK-001-059`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-005`, `TEST-001-029`
- **Verificación requerida:** `G7`, `G8`; health es alcanzable por el listener público y readiness solo desde el plano operacional; el catálogo y la carga no arrancan por defecto.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** los cuatro servicios arrancan desde los scripts documentados sin pasos manuales; backend, `web-static` y Collector no son alcanzables directamente desde redes externas, y `/ready` público no devuelve la SPA.
- **Estado:** `Pendiente`

### TASK-001-061 — Ejecutar los contenedores en local y en CI

- **Descripción:** Construir las imágenes derivadas previstas y ejecutar los cuatro servicios documentados —backend, edge Envoy, `web-static` y Collector— en local y CI, consumiendo las imágenes oficiales fijadas cuando corresponda. Verificar pantalla inicial, Vite e Inter desde `web-static` solo a través de Envoy, health y readiness por sus entradas respectivas, ausencia de acceso directo a backend/`web-static`/OTLP, cierre controlado ante SIGTERM con retirada de readiness, drenaje y registro de pendientes, y límites efectivos, sin API propietaria del proveedor de despliegue.
- **Rutas previstas:** `tests/operational/test_container_runtime.py`; `docs/evidence/containers/`
- **Referencias:** `REQ-001-014`, `NFR-001-008`, `AC-001-029`, `SEC-001-021`, `SEC-001-025`
- **Dependencias:** `TASK-001-058`, `TASK-001-060`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-029`
- **Verificación requerida:** `G8`; la ejecución local y la de CI producen la misma evidencia y ninguna depende de una API propietaria.
- **Evidencia final a cargo:** `TEST-001-029`
- **Criterio observable de cierre:** los cuatro servicios centrales —backend, Envoy, `web-static` y OpenTelemetry Collector— inician en local y CI y permiten verificar pantalla, health y readiness por sus entradas aprobadas. La evidencia distingue las imágenes derivadas construidas por el repositorio de las bases e imágenes oficiales consumidas; no presenta los cuatro servicios como cuatro artefactos necesariamente construidos aquí.
- **Estado:** `Pendiente`

### TASK-001-062 — Restringir el Collector y su plano operacional

- **Descripción:** Configurar el Collector con receptor OTLP interno, sin puerto publicado, con exporters HTTP de lectura, páginas de diagnóstico y telemetría interna HTTP desactivados, diagnóstico propio por stdout u OTLP, colas y reintentos acotados, agregación por ventana con temporalidad delta sin confundir resets ni sobrescribir emisores, y separación en memoria operacional acotada de las conexiones de origen sin exportar esa identidad como atributo o dimensión. La expiración de una muestra marca incompletitud y no simula consumo cero.
- **Rutas previstas:** `tooling/otel/collector-config.yaml`; `tooling/otel/pipelines.yaml`; `tests/operational/test_collector_semantics.py`
- **Referencias:** `REQ-001-018`, `REQ-001-021`, `NFR-001-014`, `AC-001-034`, `AC-001-036`, `SEC-001-011`, `SEC-001-012`, `SEC-001-013`, `SEC-001-014`
- **Dependencias:** `TASK-001-021`, `TASK-001-060`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-034`, `SEC-TEST-001-006`
- **Verificación requerida:** `G4`; si la configuración no logra la semántica de agregación exigida, el gate falla antes de desplegar en lugar de atribuirle agregación implícita.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** no existe ninguna ruta de lectura HTTP del Collector y la suma por componente de dos réplicas es correcta y detecta colisiones.
- **Estado:** `Pendiente`

### TASK-001-063 — Cerrar el acceso a métricas desde contextos autorizados y denegados

- **Descripción:** Configurar e inspeccionar el acceso operacional a métricas y ejecutarlo **en local y en CI** sobre el entorno de cuatro servicios: la instrumentación emite, el Collector recibe por OTLP en la red interna, los límites de cardinalidad están activos y un cliente **no autorizado** que intenta leer o recibir series desde el contexto público es denegado sin obtener series ni metadata. `web-static` no incorpora `/metrics` ni exposición operacional pública; cualquier métrica necesaria usa el canal interno aprobado. Ser poco conocida no hace segura una ruta. Esta ejecución local y de CI es **evidencia preliminar** y **prerrequisito** del cierre posterior: la evidencia final de `SEC-TEST-001-007` se agrega en `TASK-001-068`, que suma los contextos de preview y staging exigidos por `plan.md`.
- **Rutas previstas:** `tests/operational/test_metrics_access.py`; `docs/evidence/metrics-access.md`
- **Referencias:** `REQ-001-018`, `AC-001-034`, `AC-001-036`, `SEC-001-013`, `SEC-001-029`
- **Dependencias:** `TASK-001-021`, `TASK-001-060`, `TASK-001-062`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `SEC-TEST-001-007`
- **Verificación requerida:** `G4`; la prueba se ejecuta desde el contexto permitido y desde el denegado, con el Collector y el destino OTLP reales del entorno local y de CI, y su resultado se registra como evidencia preliminar.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el contexto no autorizado no recibe ninguna serie en local ni en CI, y esa evidencia queda registrada y enlazada como prerrequisito del cierre agregado posterior.
- **Estado:** `Pendiente`

### TASK-001-064 — Cerrar las métricas, la cardinalidad y la validez de sensores

- **Descripción:** Ejecutar y cerrar, sobre el entorno ejecutable con receptor OTLP real, la verificación de métricas: requests, duración, errores, throughput, CPU y memoria con unidades y valores válidos bajo una variación controlada de carga y consumo; inventario de dependencias sin pools, con el N/A justificado en lugar de ceros; plantillas de ruta y dimensiones finitas ante miles de candidatos de identificador y de query sin crecimiento de series; rechazo de una emisión con URL cruda, `request_id`, `user_id` o `tenant_id` como dimensión; e invalidación de la conclusión ante métricas ausentes, constantes pese a la perturbación, no finitas, negativas o con unidad errónea.
- **Rutas previstas:** `tests/operational/test_metrics_validity.py`; `tests/operational/test_metrics_cardinality.py`; `tests/fixtures/otlp/receiver.py`; `docs/evidence/metrics/`
- **Referencias:** `REQ-001-018`, `REQ-001-021`, `NFR-001-014`, `NFR-001-015`, `AC-001-034`, `AC-001-035`, `AC-001-036`, `SEC-001-011`, `SEC-001-012`, `SEC-001-014`, `SEC-001-016`
- **Dependencias:** `TASK-001-021`, `TASK-001-062`, `TASK-001-063`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-034`, `TEST-001-035`, `TEST-001-036`, `SEC-TEST-001-006`, `SEC-TEST-001-018`
- **Verificación requerida:** `G4`; la perturbación controlada distingue un cero válido en reposo de un cero constante bajo carga y la persistencia se registra N/A.
- **Evidencia final a cargo:** `TEST-001-034`, `TEST-001-035`, `TEST-001-036`, `SEC-TEST-001-006`, `SEC-TEST-001-018`
- **Criterio observable de cierre:** ninguna métrica requerida falta, permanece constante pese a la variación ejercida ni contiene valores inválidos.
- **Estado:** `Pendiente`

### TASK-001-065 — Crear los fixtures upstream deterministas del borde

- **Descripción:** Crear los upstream sintéticos deterministas y las rutas de prueba concretas que provocan, de forma reproducible y por separado, cada condición exigida: `UF` por **rechazo activo de conexión**; `UF` por **timeout de conexión**; `UC` por terminación de la conexión upstream **antes de encabezados**; `UR` por **reset upstream antes de encabezados**; `UT` por upstream que no responde dentro del plazo upstream total; `SI` por upstream que agota la ociosidad del flujo antes de comprometer encabezados; `503 {"status":"not_ready"}` emitido por la aplicación; respuesta **iniciada y luego interrumpida** tras comprometer encabezados; recepción lenta de una solicitud ya encaminada para el 408; cuerpo por encima del presupuesto para el 413; exceso de frecuencia para el 429; y acceso operacional denegado para el 403. Cada fixture expone su condición mediante un puerto o una ruta propia del perfil de pruebas, usa exclusivamente datos sintéticos y no añade ninguna operación al contrato.
  Si el perfil ejecuta fixtures Python en contenedor, reutiliza la base fijada `docker.io/library/python:3.14.7-slim-trixie`; no introduce otra base externa ni abre una ruta pública de producto.
- **Rutas previstas:** `tests/fixtures/upstream/reject_connect.py`; `tests/fixtures/upstream/hang_connect.py`; `tests/fixtures/upstream/close_before_headers.py`; `tests/fixtures/upstream/reset_before_headers.py`; `tests/fixtures/upstream/no_response_within_total.py`; `tests/fixtures/upstream/idle_stream.py`; `tests/fixtures/upstream/not_ready_503.py`; `tests/fixtures/upstream/interrupt_after_headers.py`; `tests/fixtures/client/slow_body_sender.py`; `tests/fixtures/client/oversized_body.py`; `tests/fixtures/client/rate_burst.py`; `tooling/compose/profiles/edge-fixtures.yaml`
- **Referencias:** `REQ-001-010`, `REQ-001-016`, `REQ-001-020`, `AC-001-030`, `AC-001-039`, `SEC-001-019`, `SEC-001-028`, `SEC-001-031`
- **Dependencias:** `TASK-001-035`, `TASK-001-036`, `TASK-001-037`, `TASK-001-038`, `TASK-001-060`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-030`, `TEST-001-039`
- **Verificación requerida:** `G4`; cada fixture reproduce su condición de forma determinista en dos ejecuciones y ninguno se activa por una bandera accesible en producción.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** las doce condiciones tienen fixture y ruta propias y ninguna se induce por umbral de duración.
- **Estado:** `Pendiente`

### TASK-001-066 — Preparar los entornos no productivos de preview y staging

- **Descripción:** Materializar los entornos de **preview** y de **staging** a partir del contrato de configuración aprobado, **sin seleccionar ni inventar un proveedor**: para cada entorno se preparan y despliegan los artefactos ya construidos —imágenes por digest, configuración del borde, backend integrado y Collector— y se materializan **dos entradas distintas**. La **entrada pública** escucha en una interfaz enrutable con TLS y sirve los estáticos y la coincidencia exacta `GET /health`, con `/ready` **ausente de su mapa de rutas**, sin prefijo, alias, reescritura, redirección ni passthrough que pueda alcanzarlo. La **entrada operacional** se enlaza a una dirección **no enrutable públicamente** del segmento privado del entorno, sin puerto publicado, sin registro DNS público y sin asociación a un balanceador o ingress con alcance a Internet, sirve exclusivamente `GET /ready`, fija `edge.operational.public_exposure` en `false` y aplica el inventario de principales con tipo, origen y vencimiento junto a una allowlist no vacía que no abarca todo el espacio de direcciones. El acceso se decide **deny by default** sobre la **identidad del peer validado**, descartando y reconstruyendo las cabeceras de forwarding recibidas del cliente, y sin introducir login, API key ni sesión de producto. El validador común se ejecuta y **falla cerrado antes del bind**. La tarea publica los endpoints y las referencias de entorno que la batería consumirá y registra la evidencia de despliegue y de configuración **por entorno y por entrada**. Preview y staging no se convierten en producto ni añaden infraestructura productiva.
  En ambos entornos se despliegan **cuatro servicios centrales** por digest: backend, edge Envoy, `web-static` NGINX y Collector. Envoy termina TLS y hace proxy de estáticos al origen interno; solo él puede alcanzar `web-static` por la red segmentada. Este origen no tiene puerto publicado ni DNS público. La guarda de ausencia de `/ready` se evalúa antes del catch-all SPA y un healthcheck interno de `web-static` no crea operación pública.
- **Rutas previstas:** `tooling/environments/preview/edge-config.yaml`; `tooling/environments/preview/operational-inventory.yaml`; `tooling/environments/staging/edge-config.yaml`; `tooling/environments/staging/operational-inventory.yaml`; `scripts/deploy/prepare-nonprod`; `docs/evidence/nonprod/preview/`; `docs/evidence/nonprod/staging/`
- **Referencias:** `REQ-001-006`, `REQ-001-014`, `REQ-001-018`, `NFR-001-008`, `AC-001-010`, `AC-001-029`, `AC-001-030`, `SEC-001-005`, `SEC-001-013`, `SEC-001-025`, `SEC-001-029`, `SEC-001-030`
- **Dependencias:** `TASK-001-013`, `TASK-001-022`, `TASK-001-023`, `TASK-001-024`, `TASK-001-039`, `TASK-001-058`, `TASK-001-059`, `TASK-001-060`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-010`, `SEC-TEST-001-007`
- **Verificación requerida:** `G4`, `G8`; ninguna comprobación depende de un proveedor concreto, la configuración se resuelve sobre el inventario antes del despliegue y una configuración inválida impide que el entorno acepte tráfico.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** preview y staging quedan preparados con cuatro servicios, sus dos entradas, redes aisladas, cuotas efectivas y evidencia por entorno; `web-static` no tiene acceso directo externo y `/ready` no figura en ningún mapa público ni cae en el fallback SPA.
- **Estado:** `Pendiente`

### TASK-001-067 — Ejecutar la batería de conformidad de `/ready` en preview y staging

- **Descripción:** Ejecutar los once casos de acceso operacional **contra los entornos no productivos ya preparados**, en preview y en staging, y nunca contra una simulación local etiquetada como tal: probe inventariado permitido; operador inventariado y vigente permitido con registro de principal y vencimiento; denegado por red, donde la conexión TCP no se establece; denegado por ruta desde la entrada pública, con respuesta de transporte y nunca un 200 o 503 de readiness; denegado por inventario para un origen de la red privada no inventariado; denegado por vencimiento; denegado materializado como 403 `ACCESS_DENIED` sin indicar si la aplicación está lista ni qué regla denegó; origen falsificado con `X-Forwarded-For`; origen falsificado con `X-Real-IP` o `Forwarded`; origen falsificado hacia la entrada pública; y comportamiento por defecto sin configurar, con `/ready` inalcanzable. Se añade el caso de **configuración inválida**, en el que el validador falla cerrado antes del bind y el entorno no llega a aceptar tráfico. El entorno local con Compose se conserva como prerrequisito distinto y no sustituye a ninguno de los dos contextos no productivos. Su contribución a `SEC-TEST-001-007` es la **evidencia de los contextos de preview y staging** —incluida la comprobación de que ninguna de las dos entradas de esos entornos expone una superficie de métricas—, que `TASK-001-068` agrega con la evidencia local y de CI; esta tarea no cierra por sí sola esa prueba.
- **Rutas previstas:** `tests/operational/test_ready_access_matrix.py`; `tests/operational/contexts/preview/`; `tests/operational/contexts/staging/`; `docs/evidence/ready-access-matrix.md`
- **Referencias:** `REQ-001-006`, `REQ-001-018`, `AC-001-010`, `AC-001-030`, `SEC-001-005`, `SEC-001-013`, `SEC-001-029`, `SEC-001-030`
- **Dependencias:** `TASK-001-011`, `TASK-001-023`, `TASK-001-024`, `TASK-001-034`, `TASK-001-060`, `TASK-001-066`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-010`, `SEC-TEST-001-007`
- **Verificación requerida:** `G4`; ninguna cabecera de origen concede acceso, la falsificación no crea `/ready` donde no está y la evidencia identifica **por separado** preview público, preview operacional, staging público y staging operacional.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** los casos permitidos, denegados, de ausencia de ruta pública, de configuración inválida, de comportamiento por defecto y de falsificación de origen registran su contexto y su resultado exigido en las cuatro superficies no productivas, con la decisión evaluada sobre el peer validado.
- **Estado:** `Pendiente`

### TASK-001-068 — Cerrar readiness contra el canal obligatorio real

- **Descripción:** Ejecutar y cerrar la evidencia de readiness **ejercitando en primer lugar la dependencia obligatoria real**, el canal de eventos obligatorios de seguridad y auditoría, sobre el entorno ejecutable completo y a través de la entrada operacional: readiness satisfactoria desde el contexto permitido; y, al impedir que el canal contabilice un evento obligatorio, readiness no satisfactoria con 503 `{"status":"not_ready"}` sin enumerar la dependencia, health conservando su semántica de vida, señal independiente al supervisor y terminación controlada si no se recupera. La evidencia **identifica explícitamente** qué dependencia obligatoria se ejercitó. El doble sintético queda **solo** como caso adicional para las transiciones difíciles de forzar —oscilación rápida listo/no listo y recuperación— y no puede sustituir al canal real como sujeto primario.

  Aquí se **agrega además la evidencia final del acceso operacional a métricas**, sumando la ejecución local y de CI de `TASK-001-063` con los contextos de preview y staging de `TASK-001-067`. La agregación demuestra que **no existe ninguna ruta `/metrics`**; que las métricas y la telemetría salen **únicamente** por el canal OTLP aprobado hacia el Collector interno; que el **listener público no expone métricas**; que el **listener operacional no añade una ruta `/metrics`**; que ningún contexto no autorizado obtiene una superficie de métricas, series ni metadata; y que la prueba queda **extendida a preview y staging** conforme a `plan.md`. La evidencia final **no se cierra únicamente con Compose local**.
- **Rutas previstas:** `tests/operational/test_readiness_mandatory_dependency.py`; `tests/operational/test_readiness_synthetic_transitions.py`; `docs/evidence/readiness/`; `docs/evidence/metrics-access-aggregated.md`
- **Referencias:** `REQ-001-005`, `REQ-001-006`, `REQ-001-018`, `REQ-001-021`, `AC-001-010`, `AC-001-011`, `AC-001-034`, `SEC-001-004`, `SEC-001-005`, `SEC-001-010`, `SEC-001-013`, `SEC-001-029`
- **Dependencias:** `TASK-001-018`, `TASK-001-040`, `TASK-001-060`, `TASK-001-063`, `TASK-001-067`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-010`, `TEST-001-011`, `SEC-TEST-001-007`, `SEC-TEST-001-008`
- **Verificación requerida:** `G4`; la prueba primaria ejercita el canal obligatorio real, ninguna de las tres condiciones de no listo se representa con 500, y el cierre del acceso a métricas exige los seis contextos, de modo que la ausencia de cualquiera de ellos bloquea la evidencia en lugar de darse por cubierta.
- **Evidencia final a cargo:** `TEST-001-010`, `TEST-001-011`, `SEC-TEST-001-007`, `SEC-TEST-001-008`
- **Criterio observable de cierre:** la evidencia nombra la dependencia obligatoria ejercitada, registra el doble sintético como caso adicional y agrega, identificados **por separado**, los seis contextos exigidos: **local**, **CI**, **preview público**, **preview operacional**, **staging público** y **staging operacional**.
- **Estado:** `Pendiente`

### TASK-001-069 — Cerrar los headers, CORS y el contenido seguro

- **Descripción:** Ejecutar y cerrar, sobre el entorno ejecutable y la build de producción servida por Envoy → `web-static`, la verificación de headers y CORS: preflight y solicitud desde origen, método o header no permitido rechazados; combinación de origen comodín con credenciales rechazada; CSP, `nosniff`, `Referrer-Policy`, `Permissions-Policy`, `frame-ancestors` y HSTS efectivos; `Content-Type` y caché correctos para HTML, archivos Vite y fuentes Inter; ausencia de headers de versión detallada, directorios listables, TRACE y archivos fuente, de configuración o de mapas sensibles; y política separada del catálogo limitada a local y CI.
- **Rutas previstas:** `tests/e2e/specs/headers-cors.spec.ts`; `tests/operational/test_cors_preflight.py`; `docs/evidence/headers/`
- **Referencias:** `REQ-001-001`, `REQ-001-002`, `AC-001-001`, `AC-001-002`, `AC-001-003`, `SEC-001-026`, `SEC-001-034`
- **Dependencias:** `TASK-001-039`, `TASK-001-049`, `TASK-001-060`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `SEC-TEST-001-013`
- **Verificación requerida:** `G4`, `G7`; la combinación insegura se rechaza y los headers efectivos se miden sobre la build de producción, no sobre la configuración declarada.
- **Evidencia final a cargo:** `SEC-TEST-001-013`
- **Criterio observable de cierre:** ningún origen no permitido recibe `Access-Control-Allow-Origin` y CORS no sustituye el aislamiento de readiness.
- **Estado:** `Pendiente`

### TASK-001-070 — Cerrar la conformidad del borde y del contrato

- **Descripción:** Ejecutar y cerrar las **dos mitades** de la verificación de contrato sobre el despliegue real, atravesando **ambas superficies**. La primera compara el esquema generado en memoria con el contrato normalizado: operaciones exactas, códigos, `required`, `additionalProperties` y headers. La segunda ejercita el límite HTTP y comprueba que el conjunto de estados y cuerpos realmente observables coincide exactamente con el contratado: health 200, 408, 413, 429, 500, 502 y 504 en la entrada pública, y ready 200, 403, 408, 413, 429, 500, 502, 503 y 504 en la entrada operacional, sin ningún estado ni cuerpo fuera del contrato. El 502 se verifica en **cuatro casos separados** —`UF` por rechazo, `UF` por timeout, `UC` y `UR`— y el 504 en sus dos causas `UT` y `SI`; el 408 con recepción lenta de una solicitud ya encaminada; el 413 con cuerpo excesivo; el 429 por frecuencia; el 403 por acceso operacional denegado. Cada caso verifica por separado **estado, cuerpo, schema, `X-Request-ID`, su igualdad exacta con `request_id`, el flag de resultado del borde, el detalle operacional de la causa, el emisor, la preservación del 503 y la ausencia de remapeo después de comprometer encabezados**. Un cierre de conexión sin respuesta se registra como transporte y nunca como 504; una respuesta interrumpida tras encabezados comprometidos conserva el estado emitido con cuerpo incompleto y no es 502 ni 504; `GET /ready` contra la entrada pública obtiene ausencia de ruta y no se registra como respuesta de readiness. `UC` y `UR` se conservan como **previstas por contrato y no observadas en el POC**, y esta es su prueba de implementación. Aquí se cierra además la correlación observada de extremo a extremo: el identificador válido se conserva idéntico y el ausente, inválido, de 129 caracteres o repetido y unido por coma se sustituye íntegro, en la respuesta upstream y en las respuestas 403, 408, 413, 429, 502 y 504.
  Ejercitar además la ruta pública de archivos Vite e Inter a través de Envoy → `web-static`: `GET`/`HEAD` funcionan sin abrir otra operación API, el fallback SPA no captura `/ready` ni lo convierte en 200, y un cliente exterior no alcanza el origen interno directamente. Una sonda efímera y controlada en el segmento interno de Envoy envía al contenedor `web-static` real al menos `POST` y `PUT` directamente y comprueba que **NGINX** los rechaza; la sonda no permanece en el despliegue. Probar por separado la entrada pública y atribuir su rechazo a **Envoy**, sin usarlo como sustituto de la prueba interna. La configuración de NGINX no usa CGI, FastCGI, SSI, autoindex ni proxy al backend.
- **Rutas previstas:** `apps/backend/tests/contract/test_openapi_schema.py`; `tests/edge/test_contract_conformance.py`; `tests/edge/test_correlation_end_to_end.py`; `tests/edge/test_static_and_ready_routing.py`; `tests/edge/test_web_static_methods.py`; `tooling/contract/openapi-comparator.py`; `docs/evidence/contract/`
- **Referencias:** `REQ-001-008`, `REQ-001-010`, `REQ-001-016`, `NFR-001-005`, `AC-001-014`, `AC-001-016`, `AC-001-030`, `SEC-001-006`, `SEC-001-007`, `SEC-001-028`
- **Dependencias:** `TASK-001-014`, `TASK-001-028`, `TASK-001-030`, `TASK-001-031`, `TASK-001-032`, `TASK-001-033`, `TASK-001-034`, `TASK-001-035`, `TASK-001-036`, `TASK-001-037`, `TASK-001-038`, `TASK-001-060`, `TASK-001-065`, `TASK-001-067`, `TASK-001-068`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-014`, `TEST-001-016`, `TEST-001-030`, `SEC-TEST-001-020`
- **Verificación requerida:** `G4`; un estado observable no documentado, una operación divergente o un ejemplo con centinela hacen fallar el gate, y la comparación nunca sobrescribe el contrato para aceptar diferencias. La prueba de métodos usa cliente dentro de la red interna contra `web-static` y evidencia el emisor NGINX; el rechazo del listener público se verifica aparte como control de Envoy. `TASK-001-060` ya materializa la imagen y la red antes de esta prueba.
- **Evidencia final a cargo:** `TEST-001-014`, `TEST-001-016`, `TEST-001-030`, `SEC-TEST-001-020`
- **Criterio observable de cierre:** cada respuesta API contratada llega **completa** al cliente, con estado, cuerpo, schema y correlación verificados en las dos entradas; los estáticos proceden de `web-static` a través de Envoy, `/ready` público nunca devuelve la SPA y la evidencia separa el rechazo directo de `POST`/`PUT` por NGINX del rechazo público por Envoy.
- **Estado:** `Pendiente`

### TASK-001-071 — Cerrar la admisión, los plazos y la saturación

- **Descripción:** Ejecutar y cerrar, sobre el entorno ejecutable con los fixtures deterministas, la verificación de capacidad, espera y plazos: capacidad y espera de admisión observables; 429 al agotar la admisión o expirar la espera máxima de 100 ms; 503 por la dependencia obligatoria de readiness y su recuperación; **instante de inicio del deadline interno de aplicación en la admisión** y cancelación efectiva; plazo upstream total de 3 s contado **desde que la solicitud está completamente recibida**; ordenamiento estricto 2 s < 3 s entre aplicación y borde; precedencia `encabezados ≤ recepción total` con rechazo de la configuración invertida; 408 sobre una solicitud ya encaminada cuyo envío se prolonga; respuesta en una sola escritura sin `chunked`; y un upstream que gotea indefinidamente sin extender el intento más allá del plazo total. La comprobación **no afirma ninguna cota** desde el primer byte del cliente ni hasta la entrega al navegador, y la saturación no se informa como éxito.
- **Rutas previstas:** `tests/operational/test_admission_and_deadlines.py`; `tests/operational/test_saturation_behaviour.py`; `docs/evidence/timeouts/`
- **Referencias:** `REQ-001-018`, `REQ-001-020`, `NFR-001-016`, `AC-001-035`, `AC-001-039`, `SEC-001-015`, `SEC-001-016`
- **Dependencias:** `TASK-001-019`, `TASK-001-020`, `TASK-001-031`, `TASK-001-036`, `TASK-001-060`, `TASK-001-065`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-039`, `SEC-TEST-001-009`
- **Verificación requerida:** `G4`; el pool sintético de prueba y la admisión real se saturan con límite y timeout respetados, saturación visible en métricas y sin falso éxito; ningún pool de persistencia productivo participa.
- **Evidencia final a cargo:** `TEST-001-039`, `SEC-TEST-001-009`
- **Criterio observable de cierre:** la evidencia declara qué plazo venció y distingue espera de admisión, saturación y trabajo ya admitido.
- **Estado:** `Pendiente`

### TASK-001-072 — Validar el presupuesto agregado y el techo de buffers

- **Descripción:** Ejecutar y cerrar el cálculo reproducible del consumo agregado al variar réplicas y workers y contrastarlo con la cuota efectiva inspeccionada: sockets, colas, activos, espera, canales OTLP, exports en vuelo, CPU, RAM y buffers. La comprobación verifica por separado que el cálculo real coincide con las fórmulas y que el **techo de 16 MiB por proceso** y el **agregado `P × 16 MiB`** rechazan toda configuración excedida, además de rechazar valores no positivos o no finitos, colas sin cota, timeouts incompatibles con el cierre, cadenas de plazos que dejen de ser estrictamente crecientes, cabeceras insuficientes para probar un identificador de 129 caracteres, límites del host inferiores al agregado, `R` fuera de presupuesto, `W` distinto de 1 sin revisión y procesos no inventariados.
  Incluir explícitamente `web-static` en el presupuesto de cuatro servicios y verificar `CPU_runtime = R × 1 + E × 0.5 + S × 0.25 + O × 0.5` y `RAM_runtime_MiB = R × 512 + E × 128 + S × 64 + O × 256`, con E, S, O inicialmente 1; exigir cuota efectiva de 0.25 vCPU, 64 MiB y un worker del origen estático, y fallar ante divergencia.
- **Rutas previstas:** `apps/backend/tests/integration/test_aggregate_budget.py`; `apps/backend/src/platform_foundation/infrastructure/config/budget.py`; `docs/evidence/budget/`
- **Referencias:** `REQ-001-020`, `NFR-001-016`, `AC-001-038`, `SEC-001-015`, `SEC-001-016`, `SEC-001-030`
- **Dependencias:** `TASK-001-008`, `TASK-001-021`, `TASK-001-059`, `TASK-001-060`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-038`
- **Verificación requerida:** `G4`; la configuración excedida falla **antes del bind** y las pruebas ejercitan también el rechazo.
- **Evidencia final a cargo:** `TEST-001-038`
- **Criterio observable de cierre:** el cálculo agregado de los cuatro servicios, incluida la cuota de `web-static`, y el techo de buffers backend por proceso se comprueban por separado y rechazan cualquier combinación que los exceda.
- **Estado:** `Pendiente`

### TASK-001-073 — Demostrar el procesamiento stateless entre réplicas

- **Descripción:** Ejecutar y cerrar, sobre dos réplicas equivalentes sin afinidad obligatoria, la verificación de que las solicitudes sucesivas dirigidas alternadamente a réplicas diferentes producen el resultado correcto sin depender de memoria mutable de una réplica anterior, con la caché local iniciada vacía o descartada entre solicitudes y sin ningún estado compartido que no proceda de un mecanismo externo documentado.
- **Rutas previstas:** `tests/operational/test_stateless_replicas.py`; `tooling/compose/profiles/replicas.yaml`; `docs/evidence/stateless/`
- **Referencias:** `REQ-001-017`, `NFR-001-013`, `AC-001-033`, `SEC-001-016`, `SEC-001-032`
- **Dependencias:** `TASK-001-060`, `TASK-001-068`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-033`
- **Verificación requerida:** `G4`; la suma de gauges de las dos réplicas es correcta y detecta colisiones sin exportar la identidad de transporte.
- **Evidencia final a cargo:** `TEST-001-033`
- **Criterio observable de cierre:** cada solicitud conserva su resultado correcto al cambiar de réplica.
- **Estado:** `Pendiente`

### TASK-001-074 — Aplicar la retención y la respuesta operacional

- **Descripción:** Configurar y documentar la retención y el acceso: en local, logs rotados con máximo de 7 días o 100 MiB por servicio y evidencia en directorio privado eliminada a los 7 días; en CI, logs y reportes 14 días, snapshots y diffs no promovidos 14 días, informes de rendimiento 30 días y baseline aprobado versionado con historial de revisión; y el contrato portable productivo inicial de logs ordinarios 14 días, eventos de seguridad 90 días y métricas 30 días, con cifrado en tránsito y reposo, reloj UTC sincronizado, escritores sin permiso de borrado, lectura solo operacional o de revisores, eliminación al expirar, alertas de export fallido, cola al 80 %, evento obligatorio fallido, OOM y CPU sostenida, y revisión de acceso mensual. Ninguna rotación borra eventos obligatorios sin señal.
- **Rutas previstas:** `tooling/otel/retention.yaml`; `docs/operations/retention.md`; `docs/operations/alerts.md`
- **Referencias:** `REQ-001-021`, `AC-001-040`, `SEC-001-009`, `SEC-001-010`, `SEC-001-024`
- **Dependencias:** `TASK-001-016`, `TASK-001-062`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-040`, `SEC-TEST-001-011`
- **Verificación requerida:** `G4`; la expiración y el borrado se verifican y no se presume durabilidad de los buffers en memoria ante SIGKILL.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la evidencia no se carga a almacenamiento público y una ventana de entrega incierta se registra en lugar de silenciarse.
- **Estado:** `Pendiente`

### TASK-001-075 — Configurar la reducción y conciliación del logging de alto volumen

- **Descripción:** Configurar la reducción explícita y medible del logging: INFO individual por request exitoso deshabilitado con su supresión contabilizada; para errores no clasificados como seguridad, hasta 10 por segundo y tipo estable, con las tres primeras muestras por ventana de 10 s conservando request ID válido, el resto agregado por evento y resultado y un máximo de 16 tipos catalogados; seguridad **sin sampling**; cada evento observado con exactamente un destino contable; y la identidad de conciliación por ventana `pendientes_iniciales + observados = emitidos + agregados + suprimidos + pendientes_finales + fallos_explícitos`. Configurar los logs reales de acceso y error de NGINX hacia stdout/stderr e ingerirlos mediante la captura supervisada aprobada. El borde reenvía a `web-static` únicamente el `X-Request-ID` final validado, cuando existe contexto de solicitud; NGINX lo incorpora solo tras recibirlo, y la prueba coteja su igualdad entre Envoy y NGINX. Aplicar el esquema cerrado cuando el registro sea estructurado, la reducción y conciliación también a `web-static`, sin cuerpos, secretos, valores hostiles ni URL cruda o query sensible. Conservar en Envoy los rechazos públicos y distinguir registros de backend, Envoy y `web-static`.
- **Rutas previstas:** `apps/backend/src/platform_foundation/infrastructure/logging/reduction.py`; `apps/backend/src/platform_foundation/infrastructure/logging/reconciliation.py`; `apps/backend/tests/integration/test_log_reduction.py`; `tooling/nginx/logging.conf`; `tooling/logging/web-static-stdout.yaml`; `tests/operational/test_web_static_logs.py`; `docs/evidence/logging/web-static/`
- **Referencias:** `REQ-001-021`, `AC-001-040`, `SEC-001-009`, `SEC-001-010`, `SEC-001-015`
- **Dependencias:** `TASK-001-015`, `TASK-001-016`, `TASK-001-074`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-040`, `SEC-TEST-001-010`
- **Verificación requerida:** `G4`; capturar la salida real de NGINX y la recepción del canal supervisado para solicitudes estáticas exitosas, rutas ausentes y métodos rechazados directamente por `web-static`; comprobar emisor, correlación propagada desde Envoy cuando aplique, supresión/agrupación de éxitos, reducción y conciliación de errores por ventana, y ausencia de secretos, candidatos hostiles, cuerpos y datos prohibidos. Las muestras de agregados son referencias y no duplican el conteo; el muestreo jamás oculta el total de errores. La captura distingue los logs de backend, Envoy y NGINX.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la evidencia de acceso y error de NGINX real incluye los tres escenarios estáticos, demuestra ingestión, correlación disponible, sanitización y reducción/conciliación por ventana junto a los otros componentes; «emitido» significa aceptado por stdout, no almacenado durablemente. Evidencia exclusiva del backend no cierra esta tarea.
- **Estado:** `Pendiente`

### Fase 7: CI, gates, cadena de suministro y promoción

Orden exigido: primero existen los artefactos y workflows de `G0`–`G8` y `GC`;
el manifiesto versionado existe antes de comprobar checks; el verificador
externo existe antes de cerrar `G0`; la protección de rama se comprueba mediante
un control externo a los jobs; y `G9` solo se ejecuta después del conjunto
exigido para PR, con `G10` después de `G9` dentro de la promoción.

### TASK-001-076 — Versionar el manifiesto de gates obligatorios

- **Descripción:** Crear el manifiesto versionado que declara el conjunto obligatorio de **doce** entradas —`G0` a `G10` y `GC`— con identificador estable, nombre, carácter bloqueante, condición de aplicabilidad cuando la tenga y la **etapa** en que se exige: `stage: pr` con `G0`–`G8` y `GC`, diez entradas, y `stage: promotion` con `G0`–`G10` y `GC`, doce entradas. El manifiesto es la única fuente de verdad y el workflow no puede ampliarlo ni reducirlo implícitamente. `GC` **pertenece** al conjunto obligatorio y no es un verificador externo a él.
- **Rutas previstas:** `tooling/ci/required-gates.yaml`; `tooling/ci/required-gates.schema.json`
- **Referencias:** `REQ-001-010`, `NFR-001-002`, `AC-001-020`, `SEC-001-020`, `SEC-001-021`, `SEC-001-024`
- **Dependencias:** `TASK-001-002`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-020`
- **Verificación requerida:** `G0`; un identificador omitido, uno de más o una etapa distinta de la declarada hacen fallar la validación del manifiesto.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el manifiesto existe, está versionado y declara exactamente doce entradas con su etapa.
- **Estado:** `Pendiente`

### TASK-001-077 — Implementar G0

- **Descripción:** Implementar el gate de alcance y aprobaciones que se ejecuta primero: verifica documentos y referencias vigentes, la concordancia de versiones y aprobaciones de `spec.md`, `acceptance.md`, `security.md`, `ui-design.md`, `data-model.md`, `contracts/openapi.yaml` y `plan.md`, el inventario de recursos y los lockfiles, SHA y digests, y bloquea ante alcance nuevo, placeholders fuera del bloque de aprobación o referencias mutables. Consume y valida `docs/supply-chain/resolved-versions.md` y `docs/supply-chain/provenance-notes.md`: falla ante referencias flotantes, hashes requeridos ausentes, digest aplicable `linux/amd64` ausente o procedencia incompleta, y verifica los SHA completos de las acciones ya introducidas. Esta tarea construye el gate; su evidencia final se cierra después del verificador externo y de la protección de rama.
- **Rutas previstas:** `scripts/ci/gate-g0`; `tooling/ci/approvals.yaml`; `.github/workflows/ci.yml`; `docs/supply-chain/provenance-notes.md`; `docs/supply-chain/resource-inventory.md`
- **Referencias:** `REQ-001-010`, `REQ-001-028`, `NFR-001-002`, `AC-001-020`, `AC-001-042`, `SEC-001-021`, `SEC-001-036`
- **Dependencias:** `TASK-001-005`, `TASK-001-007`, `TASK-001-076`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-020`, `TEST-001-042`, `SEC-TEST-001-022`, `SEC-TEST-001-028`
- **Verificación requerida:** `G0`; una versión o aprobación divergente bloquea el PR.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el gate existe, se ejecuta primero y bloquea ante aprobación divergente, sin cerrar todavía su propia evidencia. Toda acción utilizada por `.github/workflows/ci.yml` queda fijada en `uses:` por SHA completo de commit; esta misma tarea registra en `docs/supply-chain/provenance-notes.md` su nombre, repositorio propietario, SHA completo, versión humana asociada y procedencia, y actualiza incrementalmente `docs/supply-chain/resource-inventory.md` con nombre, repositorio propietario, propósito, SHA completo usado en `uses:`, versión o tag humano solo informativo, procedencia, workflow y job consumidores y estado de revisión o autorización exigido por el inventario aprobado. Los tags no son referencias ejecutables. Una acción sin SHA completo, ausente de cualquiera de los dos registros o divergente entre workflow, procedencia e inventario impide cerrar esta tarea; `TASK-001-097` conserva el ownership final de `SEC-TEST-001-029`.
- **Estado:** `Pendiente`

### TASK-001-078 — Implementar G1

- **Descripción:** Implementar el gate de secretos y cadena de suministro antes de cualquier build privilegiado: Gitleaks sobre el repositorio, revisión de dependencias, acciones fijadas por SHA completo, imágenes y recursos visuales, y Trivy con base de vulnerabilidades identificada por versión, hash y fecha, de modo que una base no disponible o desactualizada más de 24 h bloquee la promoción en lugar de pasar en silencio. Consume y valida `docs/supply-chain/resolved-versions.md` y `docs/supply-chain/provenance-notes.md`: falla ante referencias flotantes, hashes requeridos ausentes, digest aplicable `linux/amd64` ausente o procedencia incompleta, y verifica los SHA completos de las acciones ya introducidas. Los permisos por job son mínimos y el checkout no persiste credenciales.
- **Rutas previstas:** `scripts/ci/gate-g1`; `tooling/ci/gitleaks.toml`; `tooling/ci/trivy.yaml`
- **Referencias:** `REQ-001-010`, `NFR-001-002`, `NFR-001-009`, `AC-001-020`, `AC-001-031`, `AC-001-032`, `SEC-001-002`, `SEC-001-019`, `SEC-001-020`, `SEC-001-021`, `SEC-001-022`, `SEC-001-024`
- **Dependencias:** `TASK-001-005`, `TASK-001-076`, `TASK-001-077`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-031`, `TEST-001-032`, `SEC-TEST-001-003`, `SEC-TEST-001-014`, `SEC-TEST-001-029`
- **Verificación requerida:** `G1`; un hallazgo crítico o alto explotable bloquea sin aceptación implícita y ninguna excepción se admite sin el proceso constitucional.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el gate se ejecuta antes del build privilegiado y no recibe secretos desde un contexto no confiable.
- **Estado:** `Pendiente`

### TASK-001-079 — Implementar G2 y G3

- **Descripción:** Implementar los gates de análisis estático y de pruebas unitarias sobre los scripts normativos: Ruff, ESLint y Prettier de formato y lint, Pyright y TypeScript de tipos, límites de imports y detección de duplicaciones mecánicas, más pytest, Vitest y Testing Library para reglas puras, configuración, tokens, i18n y componentes.
- **Rutas previstas:** `scripts/ci/gate-g2`; `scripts/ci/gate-g3`; `tooling/lint/eslint.config.js`; `tooling/lint/ruff.toml`; `tooling/lint/import-boundaries.json`
- **Referencias:** `REQ-001-011`, `NFR-001-002`, `AC-001-020`, `AC-001-021`, `SEC-001-019`, `SEC-001-031`
- **Dependencias:** `TASK-001-003`, `TASK-001-004`, `TASK-001-076`, `TASK-001-077`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-021`
- **Verificación requerida:** `G2`, `G3`; una mutación de tipo o una dependencia cruzada prohibida falla y las unitarias reportan resultado propio.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** los mismos comandos se ejecutan en local y en CI sin divergencia.
- **Estado:** `Pendiente`

### TASK-001-080 — Implementar G4

- **Descripción:** Implementar el gate de integración, API y contrato en entorno efímero, orquestando las pruebas operacionales y de seguridad, la verificación de contrato en sus dos mitades, la validez de métricas y la existencia, validez, versionado y uso real del esquema de eventos de log, cuya ausencia, invalidez o no uso bloquea el gate en lugar de degradarse a una comprobación más débil. El gate nunca actualiza el contrato para aceptar diferencias.
- **Rutas previstas:** `scripts/ci/gate-g4`; `tooling/ci/ephemeral-env.yaml`
- **Referencias:** `REQ-001-010`, `REQ-001-016`, `REQ-001-018`, `NFR-001-002`, `AC-001-017`, `AC-001-020`, `AC-001-030`, `SEC-001-011`, `SEC-001-014`, `SEC-001-028`
- **Dependencias:** `TASK-001-064`, `TASK-001-070`, `TASK-001-071`, `TASK-001-072`, `TASK-001-075`, `TASK-001-076`, `TASK-001-077`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-030`, `TEST-001-017`
- **Verificación requerida:** `G4`; una divergencia de respuesta observable o de esquema de log bloquea la integración.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el gate reúne las verificaciones de integración, API, red y observabilidad ya cerradas por sus tareas responsables.
- **Estado:** `Pendiente`

### TASK-001-081 — Implementar G5

- **Descripción:** Implementar el gate de build y catálogo: build Vite sobre la base Node fijada y copia de sus archivos e Inter a `web-static`, **sin catálogo ni Node en imágenes productivas**; ejecución aislada del catálogo reutilizando esa misma base Node sin secretos, montajes amplios del host ni egress por defecto; obtención de dependencias en fase separada verificada, cobertura completa de historias, resolución de referencias e inventario de addons, y concordancia de versión entre diseño, catálogo e implementación.
- **Rutas previstas:** `scripts/ci/gate-g5`; `tooling/ci/catalog-sandbox.yaml`
- **Referencias:** `REQ-001-025`, `NFR-001-002`, `NFR-001-020`, `AC-001-020`, `AC-001-049`, `SEC-001-019`, `SEC-001-020`, `SEC-001-033`, `SEC-001-034`, `SEC-001-036`
- **Dependencias:** `TASK-001-044`, `TASK-001-057`, `TASK-001-076`, `TASK-001-077`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-049`, `SEC-TEST-001-023`, `SEC-TEST-001-024`
- **Verificación requerida:** `G5`; una referencia faltante, una historia ausente o la inclusión del catálogo en el artefacto productivo bloquean el gate.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la build productiva no contiene el catálogo y el catálogo se ejecuta aislado en local y CI.
- **Estado:** `Pendiente`

### TASK-001-082 — Implementar G6

- **Descripción:** Implementar el gate de accesibilidad que orquesta axe-core y la revisión manual dirigida de teclado, foco y semántica sobre la cobertura de variantes y estados, con resultado independiente de los snapshots visuales.
- **Rutas previstas:** `scripts/ci/gate-g6`
- **Referencias:** `REQ-001-013`, `REQ-001-023`, `REQ-001-024`, `NFR-001-002`, `NFR-001-004`, `NFR-001-018`, `AC-001-020`, `AC-001-026`, `AC-001-027`, `AC-001-046`, `SEC-001-034`, `SEC-001-035`, `SEC-001-036`
- **Dependencias:** `TASK-001-053`, `TASK-001-076`, `TASK-001-077`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-026`, `TEST-001-027`, `TEST-001-046`
- **Verificación requerida:** `G6`; un fallo de foco, de nombre accesible o de contraste bloquea el gate y no lo dispensa un snapshot coincidente.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la cobertura de variantes y estados se reporta por caso, sin extrapolación.
- **Estado:** `Pendiente`

### TASK-001-083 — Implementar G7

- **Descripción:** Implementar el gate de E2E y regresión visual con `mcr.microsoft.com/playwright:v1.63.0-noble` fijada por digest hijo `linux/amd64`, verificación del Node efectivo y Node 24.21.0 para herramientas del proyecto, contextos fijados y evidencia de la matriz real de navegadores, manteniendo separados el gate funcional y el visual y exigiendo revisión explícita de los diffs.
- **Rutas previstas:** `scripts/ci/gate-g7`
- **Referencias:** `REQ-001-011`, `REQ-001-027`, `NFR-001-002`, `NFR-001-011`, `NFR-001-021`, `AC-001-020`, `AC-001-028`, `AC-001-051`, `AC-001-052`, `SEC-001-019`, `SEC-001-023`, `SEC-001-035`
- **Dependencias:** `TASK-001-055`, `TASK-001-056`, `TASK-001-076`, `TASK-001-077`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-028`, `TEST-001-048`, `TEST-001-051`, `TEST-001-052`
- **Verificación requerida:** `G7`; una mutación visual o funcional bloquea el gate y los snapshots nunca se actualizan automáticamente para hacer pasar CI.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la falta de entorno o de evidencia de un navegador requerido bloquea la aceptación en lugar de marcarse como pasada.
- **Estado:** `Pendiente`

### TASK-001-084 — Implementar G8

- **Descripción:** Implementar el gate de contenedores y SBOM para las cuatro imágenes centrales, incluida la derivada NGINX `web-static`: construcción OCI, escaneo de bases e imágenes finales, SBOM SPDX o CycloneDX con dependencias transitivas, Vite, Inter, fuentes, iconos, addons y procedencia, metadatos que enlazan commit completo, pipeline o run y digest de imagen, y prueba de ejecución que inspecciona usuario efectivo, permisos, cuotas, capas y ausencia de exposición directa de `web-static`.
- **Rutas previstas:** `scripts/ci/gate-g8`; `tooling/ci/syft.yaml`; `tooling/ci/sbom-policy.yaml`
- **Referencias:** `REQ-001-010`, `REQ-001-014`, `NFR-001-002`, `NFR-001-008`, `NFR-001-009`, `AC-001-020`, `AC-001-029`, `AC-001-032`, `SEC-001-021`, `SEC-001-023`, `SEC-001-025`
- **Dependencias:** `TASK-001-058`, `TASK-001-061`, `TASK-001-076`, `TASK-001-077`, `TASK-001-078`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-029`, `TEST-001-032`, `SEC-TEST-001-016`, `SEC-TEST-001-019`, `SEC-TEST-001-029`
- **Verificación requerida:** `G8`; usuario root, secreto centinela o procedencia divergente bloquean el gate.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la SBOM queda ligada a commit y digest y las diferencias se revisan.
- **Estado:** `Pendiente`

### TASK-001-085 — Implementar GC

- **Descripción:** Implementar el job final de completitud que **no depende del éxito de los demás** y se ejecuta siempre, comparando el conjunto declarado para la etapa en el manifiesto con los resultados realmente reportados y bloqueando si un gate obligatorio ejecutado termina en fallo o si un gate obligatorio está ausente, omitido, saltado, cancelado, deshabilitado o condicionado de manera que no llega a ejecutarse. Un estado `skipped`, `cancelled` o ausente nunca se contabiliza como satisfactorio y ninguna agregación convierte un resultado faltante en aprobado. `GC` **no puede verificar su propia ausencia**: esa comprobación pertenece al control de integración externo a los jobs.
- **Rutas previstas:** `scripts/ci/gate-gc`; `tooling/ci/completeness-policy.yaml`
- **Referencias:** `REQ-001-010`, `NFR-001-002`, `AC-001-020`, `SEC-001-020`, `SEC-001-021`, `SEC-001-024`
- **Dependencias:** `TASK-001-076`, `TASK-001-077`, `TASK-001-078`, `TASK-001-079`, `TASK-001-080`, `TASK-001-081`, `TASK-001-082`, `TASK-001-083`, `TASK-001-084`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-020`
- **Verificación requerida:** `GC`; el job se ejecuta siempre, con independencia del resultado de los demás, y su alcance declarado excluye expresamente verificar su propia presencia.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el conjunto reportado se compara con el declarado para la etapa y cualquier divergencia bloquea.
- **Estado:** `Pendiente`

### TASK-001-086 — Configurar la protección de rama con los checks requeridos

- **Descripción:** Configurar la protección de rama del repositorio de modo que su lista de *required status checks* sea **exactamente igual** al conjunto `stage: pr` del manifiesto, `GC` incluido, y registrar esa configuración esperada como artefacto versionado para poder compararla. La plataforma impide la integración mientras cualquiera de esos checks no haya reportado éxito, y un check que nunca se ejecuta nunca reporta éxito, de modo que su omisión bloquea sin depender de que ningún job la detecte.
- **Rutas previstas:** `tooling/ci/branch-protection.expected.json`; `docs/operations/branch-protection.md`
- **Referencias:** `REQ-001-010`, `NFR-001-002`, `AC-001-020`, `SEC-001-020`, `SEC-001-024`
- **Dependencias:** `TASK-001-076`, `TASK-001-085`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-020`
- **Verificación requerida:** `G0`; la configuración esperada declara las diez entradas de `stage: pr` y ninguna adicional.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la protección de rama es un control **externo a los jobs** y no un job del workflow.
- **Estado:** `Pendiente`

### TASK-001-087 — Crear `scripts/ci/verify-required-checks`

- **Descripción:** Crear el verificador dedicado que lee por API la configuración de protección de rama y **falla si difiere del manifiesto en cualquier sentido**: check requerido que falta, check requerido de más o `GC` ausente de la lista. Se ejecuta dentro de `G0` y, además, desde el contexto confiable antes de promover, de modo que una relajación de la protección de rama se detecte aunque el workflow esté intacto.
- **Rutas previstas:** `scripts/ci/verify-required-checks`
- **Referencias:** `REQ-001-010`, `NFR-001-002`, `AC-001-020`, `SEC-001-020`, `SEC-001-021`, `SEC-001-024`
- **Dependencias:** `TASK-001-076`, `TASK-001-085`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-020`
- **Verificación requerida:** `G0`; el verificador existe antes de que `G0` pueda cerrar su evidencia y no depende de ningún job del workflow para funcionar.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el verificador compara ambas listas y falla ante cualquier divergencia, incluida la supresión de `GC`.
- **Estado:** `Pendiente`

### TASK-001-088 — Ejecutar la verificación externa de los required checks

- **Descripción:** Ejecutar el verificador contra la protección de rama real y registrar su evidencia, comprobando la igualdad exacta entre los checks requeridos y el conjunto `stage: pr` del manifiesto, y ejercitando la divergencia controlada en sus tres formas: check requerido que falta, check requerido de más y `GC` ausente.
- **Rutas previstas:** `tests/ci/test_required_checks_external.py`; `docs/evidence/required-checks.md`
- **Referencias:** `REQ-001-010`, `NFR-001-002`, `AC-001-020`, `SEC-001-020`, `SEC-001-024`
- **Dependencias:** `TASK-001-086`, `TASK-001-087`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-020`
- **Verificación requerida:** `G0`; la comprobación se realiza **fuera** de los jobs verificados y no se sustituye por un resultado agregado del workflow.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** la evidencia registra la lista leída por API y su comparación con el manifiesto.
- **Estado:** `Pendiente`

### TASK-001-089 — Cerrar G0 y su evidencia final

- **Descripción:** Cerrar la evidencia final de `G0` una vez que existen y se han ejecutado su implementación, el manifiesto versionado, `scripts/ci/verify-required-checks`, la verificación externa de los required checks y la protección de rama configurada. El cierre incluye la validación de `docs/supply-chain/resolved-versions.md` y `docs/supply-chain/provenance-notes.md`, incluida la fijación por SHA completo de las acciones ya introducidas, sin referencia flotante, hash requerido ausente, digest aplicable `linux/amd64` ausente ni procedencia incompleta. Aquí se cierra además la validación documental del diseño visual: existencia, versión, contenido mínimo y aprobación explícita de `ui-design.md` previas a `plan.md`, de modo que la falta del archivo, de algún contenido, de la versión o de la aprobación impida habilitar la etapa.
- **Rutas previstas:** `tests/ci/test_gate_g0_closure.py`; `tooling/ci/design-approval-validator.py`; `docs/evidence/gates/g0/`
- **Referencias:** `REQ-001-010`, `REQ-001-028`, `NFR-001-002`, `AC-001-020`, `AC-001-042`, `SEC-001-021`, `SEC-001-036`
- **Dependencias:** `TASK-001-076`, `TASK-001-077`, `TASK-001-086`, `TASK-001-087`, `TASK-001-088`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-042`
- **Verificación requerida:** `G0`; `G0` no se declara cerrado antes de que el verificador externo exista y se haya ejecutado contra la protección de rama.
- **Evidencia final a cargo:** `TEST-001-042`
- **Criterio observable de cierre:** una versión o una aprobación divergente bloquea el PR y la evidencia de `G0` enlaza manifiesto, verificador y protección de rama.
- **Estado:** `Pendiente`

### TASK-001-090 — Cerrar la completitud de gates

- **Descripción:** Cerrar la evidencia de bloqueo de la integración ejercitando **tres modos de fallo independientes** con fixtures de pipeline: un gate obligatorio ejecutado que **falla**; un gate obligatorio **suprimido, saltado, cancelado o condicionado** que no llega a ejecutarse y por tanto no reporta resultado; y una **protección de rama divergente** del manifiesto, incluida la supresión de `GC` de los checks requeridos. Los tres bloquean por separado y ninguno se compensa con otro.
- **Rutas previstas:** `tests/ci/test_gate_completeness.py`; `tests/ci/fixtures/pipeline/failing-gate.yaml`; `tests/ci/fixtures/pipeline/missing-gate.yaml`; `tests/ci/fixtures/pipeline/divergent-protection.json`; `docs/evidence/gates/completeness/`
- **Referencias:** `REQ-001-010`, `NFR-001-002`, `AC-001-020`, `SEC-001-020`, `SEC-001-021`, `SEC-001-024`
- **Dependencias:** `TASK-001-085`, `TASK-001-087`, `TASK-001-088`, `TASK-001-089`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-020`
- **Verificación requerida:** `G0`, `GC`; un estado `skipped`, `cancelled` o ausente nunca cuenta como satisfactorio.
- **Evidencia final a cargo:** `TEST-001-020`
- **Criterio observable de cierre:** la etapa PR exige `G0`–`G8` y `GC` y la promoción exige `G0`–`G10` y `GC`, comprobado sobre el manifiesto y sobre la protección de rama.
- **Estado:** `Pendiente`

### TASK-001-091 — Implementar los productores reales de eventos de CI y promoción

- **Descripción:** Emitir al canal obligatorio, desde CI y desde el proceso de promoción, las clases reales de evento que les corresponden: **gate fallido**, **gate ausente u omitido**, **promoción permitida**, **promoción bloqueada** y hallazgo bloqueante de secretos o de cadena de suministro. Los eventos usan la lista permitida de campos, enlazan commit, ejecución y artefacto por referencia opaca del proceso de revisión y no añaden identificadores personales ni dimensiones métricas.
- **Rutas previstas:** `scripts/ci/emit-security-event`; `tooling/ci/security-events.yaml`; `tests/ci/test_ci_event_producers.py`
- **Referencias:** `REQ-001-009`, `REQ-001-010`, `REQ-001-021`, `NFR-001-002`, `AC-001-020`, `AC-001-040`, `SEC-001-008`, `SEC-001-010`, `SEC-001-020`, `SEC-001-024`
- **Dependencias:** `TASK-001-085`, `TASK-001-090`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-040`, `SEC-TEST-001-011`
- **Verificación requerida:** `G4`, `GC`; los eventos se producen en ejecuciones reales del pipeline, no se sintetizan desde una prueba.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** las cinco clases de CI y promoción se observan en el canal separado con su nombre estable.
- **Estado:** `Pendiente`

### TASK-001-092 — Cerrar el alcance, los secretos y las superficies excluidas

- **Descripción:** Ejecutar y cerrar las pruebas negativas de alcance y de secretos: sembrar centinelas sintéticos en entrada y configuración y buscarlos en respuestas, logs, métricas, bundle frontend, OpenAPI, imagen y artefactos, con el repositorio limpio superando el control y una entrada sintética controlada siendo detectada sin revelar su valor; demostrar la ausencia de stack traces, contraseñas, tokens, secretos, credenciales y cuerpos sensibles en respuestas y logs; e inspeccionar la ausencia de login, sesiones, usuarios, roles, tenants, tablas o políticas RLS tenant y datos de negocio, de modo que su aparición detenga el trabajo y exija el control de cambios antes de continuar.
- **Rutas previstas:** `tests/security/test_scope_and_secrets.py`; `tests/security/test_sentinel_sweep.py`; `tests/security/fixtures/sentinels/`; `docs/evidence/security/scope/`
- **Referencias:** `REQ-001-001`, `REQ-001-002`, `REQ-001-009`, `REQ-001-010`, `NFR-001-006`, `NFR-001-009`, `AC-001-001`, `AC-001-002`, `AC-001-018`, `AC-001-031`, `SEC-001-002`, `SEC-001-003`, `SEC-001-008`, `SEC-001-019`, `SEC-001-025`, `SEC-001-028`, `SEC-001-032`
- **Dependencias:** `TASK-001-078`, `TASK-001-080`, `TASK-001-084`, `TASK-001-090`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `TEST-001-018`, `TEST-001-031`, `SEC-TEST-001-003`, `SEC-TEST-001-022`
- **Verificación requerida:** `G1`, `G4`, `G8`; cualquier aparición de un centinela o de una superficie ajena bloquea la entrega.
- **Evidencia final a cargo:** `TEST-001-018`, `TEST-001-031`, `SEC-TEST-001-003`, `SEC-TEST-001-022`
- **Criterio observable de cierre:** una detección no resuelta bloquea la entrega y la evidencia no reproduce el valor del centinela.
- **Estado:** `Pendiente`

### TASK-001-093 — Ejercitar addons y recursos visuales hostiles en sandbox

- **Descripción:** Ejecutar, **únicamente en un sandbox sintético sin secretos ni credenciales reales**, un ejemplo o addon hostil que intenta leer, escribir, acceder a red o publicar fuera de su alcance, y un HTML o SVG activo o un recurso externo no autorizado que intenta ejecutar código o transmitir información. Todos los intentos se deniegan, ningún proceso privilegiado promueve el resultado, el contenido se rechaza o neutraliza y el control falla.
- **Rutas previstas:** `tests/security/sandbox/test_hostile_addons.py`; `tests/security/sandbox/test_active_content.spec.ts`; `tests/security/sandbox/assets/`; `tooling/ci/sandbox-policy.yaml`
- **Referencias:** `REQ-001-022`, `REQ-001-023`, `REQ-001-024`, `REQ-001-025`, `NFR-001-018`, `NFR-001-019`, `NFR-001-020`, `AC-001-043`, `AC-001-044`, `AC-001-045`, `AC-001-046`, `AC-001-047`, `AC-001-048`, `AC-001-049`, `SEC-001-020`, `SEC-001-024`, `SEC-001-033`, `SEC-001-034`
- **Dependencias:** `TASK-001-044`, `TASK-001-081`, `TASK-001-090`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `SEC-TEST-001-025`, `SEC-TEST-001-026`
- **Verificación requerida:** `G5`, `G7`, `G10`; no se produce ejecución ni transmisión fuera del alcance autorizado y el sandbox no accede a credenciales ni al host.
- **Evidencia final a cargo:** `SEC-TEST-001-025`, `SEC-TEST-001-026`
- **Criterio observable de cierre:** cada intento hostil queda registrado como denegado y ningún resultado del sandbox se promueve.
- **Estado:** `Pendiente`

### TASK-001-094 — Cerrar el catálogo ejecutable y su exclusión del artefacto productivo

- **Descripción:** Ejecutar y cerrar la verificación del catálogo sobre la base Node compartida fijada: disponible solo localmente por el procedimiento documentado y en CI aislado, excluido de preview y staging, declarando su versión y conteniendo todas las categorías de tokens, componentes mínimos, variantes, matrices de estados y ejemplos permitidos, con referencias a contratos existentes y sin navegación, journeys ni datos de negocio. Se ejercita el acceso desde un contexto no autorizado y la inclusión accidental en un artefacto productivo; ambos se deniegan y hacen fallar la verificación antes de la entrega.
- **Rutas previstas:** `tests/security/test_catalog_isolation.py`; `tests/e2e/specs/catalog-validation.spec.ts`; `docs/evidence/catalog/`
- **Referencias:** `REQ-001-025`, `NFR-001-020`, `AC-001-049`, `SEC-001-019`, `SEC-001-020`, `SEC-001-029`, `SEC-001-030`, `SEC-001-033`, `SEC-001-034`
- **Dependencias:** `TASK-001-044`, `TASK-001-058`, `TASK-001-081`, `TASK-001-084`, `TASK-001-090`, `TASK-001-093`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-049`, `SEC-TEST-001-023`
- **Verificación requerida:** `G5`, `G8`; el catálogo no forma parte de la imagen ni del bundle productivo y su publicación permanece denegada por defecto.
- **Evidencia final a cargo:** `TEST-001-049`, `SEC-TEST-001-023`
- **Criterio observable de cierre:** la validación del catálogo pasa en CI y el acceso no autorizado se deniega.
- **Estado:** `Pendiente`

### TASK-001-095 — Cerrar el bloqueo por vulnerabilidad crítica o alta explotable

- **Descripción:** Ejecutar y cerrar, una vez incorporados **imágenes, inventario, addons, recursos de CI y el catálogo aplicable**, incluido `web-static` y su base NGINX Debian 13, la prueba negativa de vulnerabilidad: una dependencia, imagen, dependencia UI, addon, fuente, icono o recurso visual con hallazgo crítico o alto explotable bloquea la entrega sin aceptación implícita, y sin hallazgos bloqueantes el control finaliza satisfactoriamente. Los plazos de remediación documentados se comprueban: crítico y alto antes de promover con triage en 24 h, medio con análisis en 7 días y corrección propuesta en 30, y bajo en 90.
- **Rutas previstas:** `tests/security/test_vulnerability_gate.py`; `tests/security/fixtures/vulnerable/`; `docs/evidence/security/vulnerabilities/`
- **Referencias:** `REQ-001-010`, `REQ-001-014`, `REQ-001-023`, `REQ-001-025`, `NFR-001-009`, `AC-001-032`, `AC-001-044`, `AC-001-049`, `SEC-001-021`, `SEC-001-022`
- **Dependencias:** `TASK-001-044`, `TASK-001-058`, `TASK-001-078`, `TASK-001-084`, `TASK-001-090`, `TASK-001-093`, `TASK-001-094`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-032`, `SEC-TEST-001-014`
- **Verificación requerida:** `G1`, `G8`; no existe excepción implícita por tratarse de una herramienta de desarrollo, prueba o catálogo.
- **Evidencia final a cargo:** `TEST-001-032`, `SEC-TEST-001-014`
- **Criterio observable de cierre:** el fixture vulnerable bloquea la promoción y el repositorio sin hallazgos supera el control.
- **Estado:** `Pendiente`

### TASK-001-096 — Configurar la autorización de carga y sus productores de eventos

- **Descripción:** Crear el wrapper de k6 denegado por defecto que registra autorizador, revisión, entorno, destino permitido, parámetros, comienzo, fin y vencimiento máximo de 2 h, valida el destino por allowlist de host, puerto y red en la resolución inicial **y antes de conectar**, sin redirects, sin salida a producción ni a Internet, y con la red del generador alcanzando solo el entorno autorizado. Emite además los eventos obligatorios de **carga autorizada** y **carga denegada**. Una ejecución desde un contexto sin autorización o contra un destino no permitido no comienza y registra el rechazo sin secretos.
- **Rutas previstas:** `scripts/load/run-baseline`; `tooling/load/allowlist.yaml`; `tooling/load/authorization.schema.json`; `tests/security/test_load_authorization.py`
- **Referencias:** `REQ-001-019`, `NFR-001-015`, `AC-001-037`, `SEC-001-017`, `SEC-001-018`, `SEC-001-029`, `SEC-001-031`
- **Dependencias:** `TASK-001-060`, `TASK-001-072`, `TASK-001-090`
- **Paralelizable:** `Sí`
- **Pruebas planificadas relacionadas:** `SEC-TEST-001-012`, `TEST-001-040`
- **Verificación requerida:** `G9`; el destino productivo o la ausencia de autorización reciben **cero tráfico** y el preflight falla antes de enviar carga.
- **Evidencia final a cargo:** `SEC-TEST-001-012`
- **Criterio observable de cierre:** las variables no bastan: la red del generador solo alcanza el entorno autorizado y el rechazo queda registrado sin secretos.
- **Estado:** `Pendiente`

### TASK-001-097 — Cerrar el inventario, la procedencia y la fijación inmutable

- **Descripción:** Ejecutar y cerrar la prueba negativa final de cadena de suministro, una vez incorporados **todos** los recursos: dependencias de producción, desarrollo y prueba, Storybook y addons, Playwright y navegadores, bases oficiales Python/Node/NGINX/Playwright, Envoy, imágenes OCI derivadas —incluido `web-static`—, acciones y herramientas de CI, catálogo, carga, Collector y dependencias de frontend/backend. Storybook y fixtures declaran la reutilización de bases fijadas. La prueba verifica y **bloquea** cada caso por separado: recurso **no inventariado**; procedencia **ausente o no verificable**; **referencia flotante** donde el flujo de entrega exige fijación inmutable verificable; y **actualización sin la revisión requerida**. No se admite aceptación implícita ni excepción por tratarse de herramienta de desarrollo, prueba o catálogo. Esta prueba negativa final es distinta del control temprano de resolución e inventario de la primera fase.
- **Rutas previstas:** `tests/security/test_resource_inventory_gate.py`; `tests/security/fixtures/supply-chain/`; `docs/evidence/supply-chain/`
- **Referencias:** `REQ-001-010`, `REQ-001-014`, `REQ-001-022`, `REQ-001-023`, `REQ-001-025`, `NFR-001-002`, `NFR-001-008`, `NFR-001-009`, `NFR-001-020`, `AC-001-020`, `AC-001-029`, `AC-001-032`, `AC-001-043`, `AC-001-044`, `AC-001-049`, `SEC-001-021`
- **Dependencias:** `TASK-001-001`, `TASK-001-003`, `TASK-001-004`, `TASK-001-005`, `TASK-001-022`, `TASK-001-044`, `TASK-001-049`, `TASK-001-058`, `TASK-001-062`, `TASK-001-078`, `TASK-001-084`, `TASK-001-093`, `TASK-001-094`, `TASK-001-095`, `TASK-001-096`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `SEC-TEST-001-029`
- **Verificación requerida:** `G0`, `G1`, `G8`; los cuatro casos hacen fallar su gate correspondiente y bloquean la entrega por separado.
- **Evidencia final a cargo:** `SEC-TEST-001-029`
- **Criterio observable de cierre:** ningún recurso del inventario queda sin procedencia verificable ni con referencia flotante en el flujo de entrega.
- **Estado:** `Pendiente`

### TASK-001-098 — Cerrar los eventos obligatorios y su conciliación integrada

- **Descripción:** Ejecutar y cerrar la prueba integrada que **observa cada clase real de evento obligatorio en el canal separado**, producida por su componente real y no por un doble sintético: configuración rechazada y validación pre-bind fallida; acceso operacional denegado; candidato de correlación inválido **sin registrar el valor hostil**; carga autorizada y carga denegada; gate fallido y gate ausente u omitido; promoción permitida y promoción bloqueada; y degradación o caída del canal obligatorio. La prueba valida el **schema** contra el artefacto versionado, la **allowlist** de campos, la **correlación** por request ID cuando existe contexto, la **reducción de datos** aplicada, y confirma la **ausencia de secretos y de payloads hostiles**. Ejercita además la tormenta de logs y errores con reducción cuantificada y conciliación de observado, emitido, agregado y suprimido, y comprueba que **readiness se retira mediante el cableado real de `TASK-001-018`** cuando el canal obligatorio deja de contabilizar eventos. Ningún evento sintético cierra este requisito.
- **Rutas previstas:** `tests/operational/test_mandatory_events_integrated.py`; `tests/operational/test_log_storm.py`; `docs/evidence/mandatory-events/`
- **Referencias:** `REQ-001-006`, `REQ-001-009`, `REQ-001-021`, `NFR-001-006`, `NFR-001-007`, `AC-001-040`, `SEC-001-005`, `SEC-001-008`, `SEC-001-009`, `SEC-001-010`, `SEC-001-015`
- **Dependencias:** `TASK-001-016`, `TASK-001-017`, `TASK-001-018`, `TASK-001-023`, `TASK-001-024`, `TASK-001-040`, `TASK-001-060`, `TASK-001-075`, `TASK-001-091`, `TASK-001-096`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-040`, `SEC-TEST-001-010`, `SEC-TEST-001-011`
- **Verificación requerida:** `G4`; ningún evento obligatorio desaparece sin señal ni contador, y la reducción nunca oculta el total observado.
- **Evidencia final a cargo:** `TEST-001-040`, `SEC-TEST-001-010`, `SEC-TEST-001-011`
- **Criterio observable de cierre:** las once clases reales se observan en el canal separado y la conciliación por ventana cuadra.
- **Estado:** `Pendiente`

### TASK-001-099 — Ejecutar el baseline autorizado en G9

- **Descripción:** Ejecutar `G9` **después del conjunto de gates exigido para PR**, desde una ejecución autorizada y confiable, con el escenario aprobado: una réplica de backend, generador separado, warm-up de 30 s excluido, medición de 120 s, cinco usuarios virtuales constantes, think time de 250 ms, timeout de cliente de 3 s y mezcla determinista de 80 % health atravesando el límite público y 20 % readiness por el destino interno permitido. Se ejecuta dos veces con la misma revisión, recursos, mezcla, semilla y parámetros, reiniciando estado efímero y dejando 30 s sin carga entre ejecuciones, y se registran throughput, p50, p95, p99, requests, errores por clase, CPU, memoria, concurrencia, espera, colas, rechazos, OTLP y contadores de logs, con persistencia y pools registrados N/A. Los reportes se sanitizan y su conclusión de alcance dice **solo baseline operativo, no capacidad de journeys de negocio**; health y readiness satisfactorios se identifican solo como comprobación operativa. Un identificador o dato prohibido sintético sembrado en el reporte impide su publicación.
- **Rutas previstas:** `tests/perf/baseline.js`; `tests/perf/scenarios/mix-80-20.json`; `scripts/load/report-sanitizer`; `docs/evidence/baseline/`
- **Referencias:** `REQ-001-019`, `NFR-001-015`, `NFR-001-017`, `AC-001-037`, `AC-001-041`, `SEC-001-004`, `SEC-001-014`, `SEC-001-017`, `SEC-001-018`, `SEC-001-019`, `SEC-001-031`
- **Dependencias:** `TASK-001-064`, `TASK-001-072`, `TASK-001-090`, `TASK-001-096`, `TASK-001-097`, `TASK-001-098`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-037`, `TEST-001-041`, `SEC-TEST-001-017`
- **Verificación requerida:** `G9`; el éxito del procedimiento son dos reportes completos, sanitizados y válidos, no un umbral de RPS, y una métrica ausente, constante o inválida invalida la conclusión.
- **Evidencia final a cargo:** `TEST-001-037`, `TEST-001-041`, `SEC-TEST-001-017`
- **Criterio observable de cierre:** los dos reportes reproducen los mismos parámetros y ninguna conclusión afirma capacidad de negocio ni una tasa soportada.
- **Estado:** `Pendiente`

### TASK-001-100 — Configurar la promoción confiable en G10

- **Descripción:** Configurar `G10` **después de `G9`** dentro de la promoción, desde un commit confiable revisado y con todos los gates requeridos en verde, con manifest de hashes, commit, run y aprobación, registro y evidencia privados, publisher separado que valida formato cerrado, hashes, procedencia y aprobación **sin ejecutar contenido** y con permiso limitado al destino. Se ejercita la prueba negativa: un pull request o contexto no confiable que intenta obtener secretos, escribir en el repositorio, publicar artefactos o evidencia visual o invocar indirectamente un proceso privilegiado de publicación o promoción es denegado en todos los intentos. No se promueven baselines desde un fork ni por actualización automática, y los artefactos de un PR son no confiables aunque su nombre coincida con el de una ejecución confiable.
- **Rutas previstas:** `scripts/ci/gate-g10`; `scripts/ci/publisher`; `tooling/ci/promotion-policy.yaml`; `tests/security/test_untrusted_promotion.py`; `docs/evidence/promotion/`
- **Referencias:** `REQ-001-010`, `REQ-001-014`, `REQ-001-025`, `REQ-001-027`, `NFR-001-002`, `NFR-001-009`, `NFR-001-020`, `NFR-001-021`, `AC-001-020`, `AC-001-031`, `AC-001-049`, `AC-001-051`, `SEC-001-020`, `SEC-001-023`, `SEC-001-024`
- **Dependencias:** `TASK-001-056`, `TASK-001-084`, `TASK-001-088`, `TASK-001-089`, `TASK-001-090`, `TASK-001-097`, `TASK-001-099`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `SEC-TEST-001-015`
- **Verificación requerida:** `G10`; la promoción ocurre solo desde el contexto confiable y una evidencia alterada bloquea la entrega.
- **Evidencia final a cargo:** `SEC-TEST-001-015`
- **Criterio observable de cierre:** todos los intentos desde el contexto no confiable se deniegan y `G10` no se ejecuta antes que `G9`.
- **Estado:** `Pendiente`

### Fase 8: Cierre documental, paridad de suite y trazabilidad

### TASK-001-101 — Cerrar la paridad de la suite local y de CI

- **Descripción:** Ejecutar y cerrar la verificación de que el mismo revisionado de código y la configuración de prueba documentada producen, **local y en CI**, la ejecución de las verificaciones aplicables de smoke, health, readiness, errores, request ID, localización, accesibilidad y compatibilidad, y el reporte **por separado** de pruebas pasadas, fallidas y no ejecutadas, con la razón de cualquier prueba omitida. Un test faltante, una evidencia ilegible, una revisión manual pendiente o un fixture no ejecutado se reportan como pendiente o fallido y no se convierten en aprobados por agregación.
- **Rutas previstas:** `tests/suite/test_local_ci_parity.py`; `scripts/dev/verify.sh`; `docs/evidence/suite-parity.md`
- **Referencias:** `REQ-001-011`, `AC-001-021`, `SEC-001-019`, `SEC-001-031`
- **Dependencias:** `TASK-001-079`, `TASK-001-090`, `TASK-001-098`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-021`
- **Verificación requerida:** `G3`, `G4`; los dos entornos ejecutan los mismos comandos y producen resultados reproducibles.
- **Evidencia final a cargo:** `TEST-001-021`
- **Criterio observable de cierre:** la evidencia distingue pasadas, fallidas y no ejecutadas con su razón.
- **Estado:** `Pendiente`

### TASK-001-102 — Validar la documentación desde un checkout limpio

- **Descripción:** Ejecutar el E2E final de arranque reproducible **una vez que existen** el monorepo, los lockfiles, los servicios, la configuración, los contenedores, el entorno Compose, la suite de pruebas y los gates requeridos: en un entorno compatible limpio con solo los prerrequisitos documentados, una persona sigue de principio a fin la documentación y obtiene backend y frontend operativos y las verificaciones documentadas satisfactorias, sin editar código fuente ni usar pasos manuales no documentados. Se revisa además el contenido mínimo de la documentación —prerrequisitos, configuración externa, arranque, health, readiness, pruebas, controles equivalentes a CI y resolución de problemas frecuentes— y la ausencia de secretos reales.
- **Rutas previstas:** `scripts/dev/clean-checkout-verify.sh`; `tests/e2e/specs/clean-checkout.spec.ts`; `docs/evidence/clean-checkout.md`
- **Referencias:** `REQ-001-003`, `REQ-001-015`, `NFR-001-001`, `NFR-001-010`, `AC-001-004`, `AC-001-005`, `SEC-001-001`, `SEC-001-002`, `SEC-001-025`
- **Dependencias:** `TASK-001-002`, `TASK-001-003`, `TASK-001-004`, `TASK-001-007`, `TASK-001-058`, `TASK-001-060`, `TASK-001-090`, `TASK-001-101`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-004`, `TEST-001-005`
- **Verificación requerida:** `G7`; un paso no documentado o una edición de código fuente hacen fallar la verificación.
- **Evidencia final a cargo:** `TEST-001-004`, `TEST-001-005`
- **Criterio observable de cierre:** el registro del arranque enlaza cada comando documentado con su resultado, sin conocimiento implícito.
- **Estado:** `Pendiente`

### TASK-001-103 — Conciliar la evidencia y la trazabilidad

- **Descripción:** Unir la cadena `REQ/NFR → AC → TEST → ejecución → artefacto y hash → revisión y aprobación`, y `THREAT → SEC → SEC-TEST` para seguridad, en un manifest cerrado por ejecución con commit, plan, diseño, contrato y sus hashes, versiones exactas de herramientas, escenario, recursos, resultados por ID, archivos sanitizados y sus hashes, revisión y autorización cuando corresponda, sin secretos, identificadores personales ni URLs internas. Una comprobación mecánica verifica que las 29 filas de pruebas negativas reproducen exactamente el mapeo de controles de `security.md` y falla si este documento añade o retira un control de cualquiera de ellas.
- **Rutas previstas:** `tooling/traceability/build-manifest.py`; `tooling/traceability/verify-sec-mapping.py`; `docs/evidence/traceability-manifest.json`
- **Referencias:** `REQ-001-010`, `NFR-001-002`, `AC-001-020`, `SEC-001-019`, `SEC-001-020`, `SEC-001-023`, `SEC-001-024`
- **Dependencias:** `TASK-001-029`, `TASK-001-050`, `TASK-001-051`, `TASK-001-052`, `TASK-001-053`, `TASK-001-054`, `TASK-001-055`, `TASK-001-057`, `TASK-001-061`, `TASK-001-068`, `TASK-001-069`, `TASK-001-070`, `TASK-001-071`, `TASK-001-072`, `TASK-001-073`, `TASK-001-092`, `TASK-001-094`, `TASK-001-095`, `TASK-001-097`, `TASK-001-098`, `TASK-001-099`, `TASK-001-100`, `TASK-001-101`, `TASK-001-102`
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-020`, `TEST-001-021`
- **Verificación requerida:** `G0`, `G4`, `GC`; los pendientes se reportan como pendientes y no se declaran pasados por agregación.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** cada `AC-*` enlaza al menos una prueba y su evidencia, y ningún mapeo de `security.md` queda alterado.
- **Estado:** `Pendiente`

### TASK-001-104 — Preparar la revisión documental de tareas

- **Descripción:** Verificar documentalmente este borrador contra los artefactos aprobados, los IDs, las dependencias, la trazabilidad, los gates, las exclusiones de alcance y los pendientes declarados, sin requerir ninguna ejecución de implementación. Esta revisión permite decidir de forma independiente sobre `tasks.md`; durante esa revisión previa a la aprobación, `SPEC-001` permanecía en `Plan Approved` hasta una autorización posterior.
- **Rutas previstas:** `specs/001-platform-foundation/tasks.md`
- **Referencias:** `REQ-001-010`, `NFR-001-002`, `AC-001-020`, `SEC-001-020`, `SEC-001-021`, `SEC-001-024`
- **Dependencias:** Ninguna
- **Paralelizable:** `No`
- **Pruebas planificadas relacionadas:** `TEST-001-020`
- **Verificación requerida:** `G0`; la revisión es documental y no depende de evidencia ejecutada.
- **Evidencia final a cargo:** La matriz asigna el cierre a otra tarea
- **Criterio observable de cierre:** el borrador queda revisable de forma independiente; durante esa revisión previa a la aprobación, `tasks.md` se encontraba en `Draft` con aprobación `Pendiente`.
- **Estado:** `Pendiente`

## Dependencias y paralelismo

Las dependencias apuntan únicamente a tareas de numeración inferior, de modo que
el grafo es acíclico por construcción. El paralelismo se declara solo cuando las
tareas implicadas ya tienen sus dependencias satisfechas y no comparten archivo,
artefacto ni decisión; en cualquier otro caso se declara `Ninguna declarada` en
lugar de presuponer independencia.

| Tarea | Depende de | Puede ejecutarse en paralelo con | Motivo |
|---|---|---|---|
| `TASK-001-001` | Ninguna | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-002` | `TASK-001-001` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-003` | `TASK-001-001`, `TASK-001-002` | `TASK-001-004`, `TASK-001-005`, `TASK-001-006` | Stacks y artefactos disjuntos: proyecto Python, proyecto frontend, inventario de recursos y esquema de logs no comparten archivo. |
| `TASK-001-004` | `TASK-001-001`, `TASK-001-002` | `TASK-001-003`, `TASK-001-005`, `TASK-001-006` | Stacks y artefactos disjuntos: proyecto Python, proyecto frontend, inventario de recursos y esquema de logs no comparten archivo. |
| `TASK-001-005` | `TASK-001-001`, `TASK-001-002` | `TASK-001-003`, `TASK-001-004`, `TASK-001-006` | Stacks y artefactos disjuntos: proyecto Python, proyecto frontend, inventario de recursos y esquema de logs no comparten archivo. |
| `TASK-001-006` | `TASK-001-002` | `TASK-001-003`, `TASK-001-004`, `TASK-001-005` | Stacks y artefactos disjuntos: proyecto Python, proyecto frontend, inventario de recursos y esquema de logs no comparten archivo. |
| `TASK-001-007` | `TASK-001-003`, `TASK-001-004` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-008` | `TASK-001-003` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-009` | `TASK-001-003`, `TASK-001-008` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-010` | `TASK-001-009` | `TASK-001-011`, `TASK-001-012` | Tres módulos distintos del backend: ruta de health, servicio de readiness y middleware de correlación. |
| `TASK-001-011` | `TASK-001-009` | `TASK-001-010`, `TASK-001-012` | Tres módulos distintos del backend: ruta de health, servicio de readiness y middleware de correlación. |
| `TASK-001-012` | `TASK-001-009` | `TASK-001-010`, `TASK-001-011` | Tres módulos distintos del backend: ruta de health, servicio de readiness y middleware de correlación. |
| `TASK-001-013` | `TASK-001-009`, `TASK-001-010`, `TASK-001-011`, `TASK-001-012` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-014` | `TASK-001-010`, `TASK-001-011`, `TASK-001-012` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-015` | `TASK-001-006`, `TASK-001-012` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-016` | `TASK-001-011`, `TASK-001-015` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-017` | `TASK-001-008`, `TASK-001-012`, `TASK-001-016` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-018` | `TASK-001-011`, `TASK-001-016`, `TASK-001-017` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-019` | `TASK-001-008`, `TASK-001-014` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-020` | `TASK-001-019` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-021` | `TASK-001-009`, `TASK-001-019`, `TASK-001-020` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-022` | `TASK-001-001`, `TASK-001-010` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-023` | `TASK-001-001`, `TASK-001-011`, `TASK-001-022` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-024` | `TASK-001-008`, `TASK-001-022`, `TASK-001-023` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-025` | `TASK-001-022` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-026` | `TASK-001-025` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-027` | `TASK-001-026` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-028` | `TASK-001-027` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-029` | `TASK-001-026`, `TASK-001-028` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-030` | `TASK-001-014`, `TASK-001-028` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-031` | `TASK-001-030` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-032` | `TASK-001-030` | `TASK-001-033`, `TASK-001-034`, `TASK-001-035` | Cuatro archivos distintos de configuración del borde: transporte, cuerpo, frecuencia y respuestas locales de fallo upstream. |
| `TASK-001-033` | `TASK-001-030` | `TASK-001-032`, `TASK-001-034`, `TASK-001-035` | Cuatro archivos distintos de configuración del borde: transporte, cuerpo, frecuencia y respuestas locales de fallo upstream. |
| `TASK-001-034` | `TASK-001-023`, `TASK-001-030` | `TASK-001-032`, `TASK-001-033`, `TASK-001-035` | Cuatro archivos distintos de configuración del borde: transporte, cuerpo, frecuencia y respuestas locales de fallo upstream. |
| `TASK-001-035` | `TASK-001-030` | `TASK-001-032`, `TASK-001-033`, `TASK-001-034` | Cuatro archivos distintos de configuración del borde: transporte, cuerpo, frecuencia y respuestas locales de fallo upstream. |
| `TASK-001-036` | `TASK-001-030`, `TASK-001-031` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-037` | `TASK-001-011`, `TASK-001-035`, `TASK-001-036` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-038` | `TASK-001-035`, `TASK-001-036` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-039` | `TASK-001-022`, `TASK-001-030` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-040` | `TASK-001-023`, `TASK-001-024`, `TASK-001-028`, `TASK-001-034` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-041` | `TASK-001-004` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-042` | `TASK-001-041` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-043` | `TASK-001-042` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-044` | `TASK-001-043` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-045` | `TASK-001-042`, `TASK-001-043` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-046` | `TASK-001-045` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-047` | `TASK-001-022`, `TASK-001-045`, `TASK-001-046` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-048` | `TASK-001-045`, `TASK-001-047` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-049` | `TASK-001-044`, `TASK-001-045`, `TASK-001-046`, `TASK-001-047`, `TASK-001-048` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-050` | `TASK-001-047`, `TASK-001-049` | `TASK-001-051`, `TASK-001-052` | Tres especificaciones distintas sobre el mismo arnés ya preparado; no comparten archivo ni baseline. |
| `TASK-001-051` | `TASK-001-048`, `TASK-001-049` | `TASK-001-050`, `TASK-001-052` | Tres especificaciones distintas sobre el mismo arnés ya preparado; no comparten archivo ni baseline. |
| `TASK-001-052` | `TASK-001-043`, `TASK-001-045`, `TASK-001-049` | `TASK-001-050`, `TASK-001-051` | Tres especificaciones distintas sobre el mismo arnés ya preparado; no comparten archivo ni baseline. |
| `TASK-001-053` | `TASK-001-044`, `TASK-001-049`, `TASK-001-050`, `TASK-001-052` | `TASK-001-054` | Accesibilidad y reflow usan directorios y evidencias separados sobre el mismo arnés. |
| `TASK-001-054` | `TASK-001-049`, `TASK-001-050`, `TASK-001-052` | `TASK-001-053` | Accesibilidad y reflow usan directorios y evidencias separados sobre el mismo arnés. |
| `TASK-001-055` | `TASK-001-053`, `TASK-001-054` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-056` | `TASK-001-044`, `TASK-001-055` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-057` | `TASK-001-041`, `TASK-001-044`, `TASK-001-045`, `TASK-001-056` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-058` | `TASK-001-013`, `TASK-001-022`, `TASK-001-045` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-059` | `TASK-001-021`, `TASK-001-058` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-060` | `TASK-001-021`, `TASK-001-023`, `TASK-001-039`, `TASK-001-058`, `TASK-001-059` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-061` | `TASK-001-058`, `TASK-001-060` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-062` | `TASK-001-021`, `TASK-001-060` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-063` | `TASK-001-021`, `TASK-001-060`, `TASK-001-062` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-064` | `TASK-001-021`, `TASK-001-062`, `TASK-001-063` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-065` | `TASK-001-035`, `TASK-001-036`, `TASK-001-037`, `TASK-001-038`, `TASK-001-060` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-066` | `TASK-001-013`, `TASK-001-022`, `TASK-001-023`, `TASK-001-024`, `TASK-001-039`, `TASK-001-058`, `TASK-001-059`, `TASK-001-060` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-067` | `TASK-001-011`, `TASK-001-023`, `TASK-001-024`, `TASK-001-034`, `TASK-001-060`, `TASK-001-066` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-068` | `TASK-001-018`, `TASK-001-040`, `TASK-001-060`, `TASK-001-063`, `TASK-001-067` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-069` | `TASK-001-039`, `TASK-001-049`, `TASK-001-060` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-070` | `TASK-001-014`, `TASK-001-028`, `TASK-001-030`, `TASK-001-031`, `TASK-001-032`, `TASK-001-033`, `TASK-001-034`, `TASK-001-035`, `TASK-001-036`, `TASK-001-037`, `TASK-001-038`, `TASK-001-060`, `TASK-001-065`, `TASK-001-067`, `TASK-001-068` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-071` | `TASK-001-019`, `TASK-001-020`, `TASK-001-031`, `TASK-001-036`, `TASK-001-060`, `TASK-001-065` | `TASK-001-072`, `TASK-001-073` | Plazos, presupuesto agregado y stateless usan archivos de prueba y evidencia distintos. |
| `TASK-001-072` | `TASK-001-008`, `TASK-001-021`, `TASK-001-059`, `TASK-001-060` | `TASK-001-071`, `TASK-001-073` | Plazos, presupuesto agregado y stateless usan archivos de prueba y evidencia distintos. |
| `TASK-001-073` | `TASK-001-060`, `TASK-001-068` | `TASK-001-071`, `TASK-001-072` | Plazos, presupuesto agregado y stateless usan archivos de prueba y evidencia distintos. |
| `TASK-001-074` | `TASK-001-016`, `TASK-001-062` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-075` | `TASK-001-015`, `TASK-001-016`, `TASK-001-074` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-076` | `TASK-001-002` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-077` | `TASK-001-005`, `TASK-001-007`, `TASK-001-076` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-078` | `TASK-001-005`, `TASK-001-076`, `TASK-001-077` | `TASK-001-079` | Gates de secretos y de análisis estático son scripts independientes. |
| `TASK-001-079` | `TASK-001-003`, `TASK-001-004`, `TASK-001-076`, `TASK-001-077` | `TASK-001-078` | Gates de secretos y de análisis estático son scripts independientes. |
| `TASK-001-080` | `TASK-001-064`, `TASK-001-070`, `TASK-001-071`, `TASK-001-072`, `TASK-001-075`, `TASK-001-076`, `TASK-001-077` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-081` | `TASK-001-044`, `TASK-001-057`, `TASK-001-076`, `TASK-001-077` | `TASK-001-082`, `TASK-001-083` | Gates de catálogo, accesibilidad y E2E/visual son scripts independientes. |
| `TASK-001-082` | `TASK-001-053`, `TASK-001-076`, `TASK-001-077` | `TASK-001-081`, `TASK-001-083` | Gates de catálogo, accesibilidad y E2E/visual son scripts independientes. |
| `TASK-001-083` | `TASK-001-055`, `TASK-001-056`, `TASK-001-076`, `TASK-001-077` | `TASK-001-081`, `TASK-001-082` | Gates de catálogo, accesibilidad y E2E/visual son scripts independientes. |
| `TASK-001-084` | `TASK-001-058`, `TASK-001-061`, `TASK-001-076`, `TASK-001-077`, `TASK-001-078` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-085` | `TASK-001-076`, `TASK-001-077`, `TASK-001-078`, `TASK-001-079`, `TASK-001-080`, `TASK-001-081`, `TASK-001-082`, `TASK-001-083`, `TASK-001-084` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-086` | `TASK-001-076`, `TASK-001-085` | `TASK-001-087` | Configuración esperada de la protección de rama y verificador dedicado son artefactos distintos. |
| `TASK-001-087` | `TASK-001-076`, `TASK-001-085` | `TASK-001-086` | Configuración esperada de la protección de rama y verificador dedicado son artefactos distintos. |
| `TASK-001-088` | `TASK-001-086`, `TASK-001-087` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-089` | `TASK-001-076`, `TASK-001-077`, `TASK-001-086`, `TASK-001-087`, `TASK-001-088` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-090` | `TASK-001-085`, `TASK-001-087`, `TASK-001-088`, `TASK-001-089` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-091` | `TASK-001-085`, `TASK-001-090` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-092` | `TASK-001-078`, `TASK-001-080`, `TASK-001-084`, `TASK-001-090` | `TASK-001-093`, `TASK-001-096` | Alcance y secretos, sandbox hostil y autorización de carga operan en árboles y sandboxes separados. |
| `TASK-001-093` | `TASK-001-044`, `TASK-001-081`, `TASK-001-090` | `TASK-001-092`, `TASK-001-096` | Alcance y secretos, sandbox hostil y autorización de carga operan en árboles y sandboxes separados. |
| `TASK-001-094` | `TASK-001-044`, `TASK-001-058`, `TASK-001-081`, `TASK-001-084`, `TASK-001-090`, `TASK-001-093` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-095` | `TASK-001-044`, `TASK-001-058`, `TASK-001-078`, `TASK-001-084`, `TASK-001-090`, `TASK-001-093`, `TASK-001-094` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-096` | `TASK-001-060`, `TASK-001-072`, `TASK-001-090` | `TASK-001-092`, `TASK-001-093` | Alcance y secretos, sandbox hostil y autorización de carga operan en árboles y sandboxes separados. |
| `TASK-001-097` | `TASK-001-001`, `TASK-001-003`, `TASK-001-004`, `TASK-001-005`, `TASK-001-022`, `TASK-001-044`, `TASK-001-049`, `TASK-001-058`, `TASK-001-062`, `TASK-001-078`, `TASK-001-084`, `TASK-001-093`, `TASK-001-094`, `TASK-001-095`, `TASK-001-096` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-098` | `TASK-001-016`, `TASK-001-017`, `TASK-001-018`, `TASK-001-023`, `TASK-001-024`, `TASK-001-040`, `TASK-001-060`, `TASK-001-075`, `TASK-001-091`, `TASK-001-096` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-099` | `TASK-001-064`, `TASK-001-072`, `TASK-001-090`, `TASK-001-096`, `TASK-001-097`, `TASK-001-098` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-100` | `TASK-001-056`, `TASK-001-084`, `TASK-001-088`, `TASK-001-089`, `TASK-001-090`, `TASK-001-097`, `TASK-001-099` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-101` | `TASK-001-079`, `TASK-001-090`, `TASK-001-098` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-102` | `TASK-001-002`, `TASK-001-003`, `TASK-001-004`, `TASK-001-007`, `TASK-001-058`, `TASK-001-060`, `TASK-001-090`, `TASK-001-101` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-103` | `TASK-001-029`, `TASK-001-050`, `TASK-001-051`, `TASK-001-052`, `TASK-001-053`, `TASK-001-054`, `TASK-001-055`, `TASK-001-057`, `TASK-001-061`, `TASK-001-068`, `TASK-001-069`, `TASK-001-070`, `TASK-001-071`, `TASK-001-072`, `TASK-001-073`, `TASK-001-092`, `TASK-001-094`, `TASK-001-095`, `TASK-001-097`, `TASK-001-098`, `TASK-001-099`, `TASK-001-100`, `TASK-001-101`, `TASK-001-102` | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |
| `TASK-001-104` | Ninguna | Ninguna declarada | Integración secuencial: comparte artefactos o decisiones con tareas vecinas; no se presupone independencia. |

## Matriz de trazabilidad

Una sola tarea es responsable de la evidencia final de cada prueba. Otras tareas
pueden crear la prueba, implementar el comportamiento o ejecutarla de forma
preliminar, pero no cierran su evidencia.

### TEST ↔ AC ↔ tipo

| Prueba | AC aprobado | Tipo aprobado | Responsable final de la evidencia |
|---|---|---|---|
| `TEST-001-001` | `AC-001-001` | `INTEGRATION` | `TASK-001-013` |
| `TEST-001-002` | `AC-001-002` | `E2E` | `TASK-001-050` |
| `TEST-001-003` | `AC-001-003` | `E2E` | `TASK-001-050` |
| `TEST-001-004` | `AC-001-004` | `E2E` | `TASK-001-102` |
| `TEST-001-005` | `AC-001-005` | `E2E` | `TASK-001-102` |
| `TEST-001-006` | `AC-001-006` | `INTEGRATION` | `TASK-001-008` |
| `TEST-001-007` | `AC-001-007` | `INTEGRATION` | `TASK-001-008` |
| `TEST-001-008` | `AC-001-008` | `INTEGRATION` | `TASK-001-008` |
| `TEST-001-009` | `AC-001-009` | `API` | `TASK-001-010` |
| `TEST-001-010` | `AC-001-010` | `INTEGRATION` | `TASK-001-068` |
| `TEST-001-011` | `AC-001-011` | `INTEGRATION` | `TASK-001-068` |
| `TEST-001-012` | `AC-001-012` | `API` | `TASK-001-014` |
| `TEST-001-013` | `AC-001-013` | `SECURITY` | `TASK-001-014` |
| `TEST-001-014` | `AC-001-014` | `INTEGRATION` | `TASK-001-070` |
| `TEST-001-015` | `AC-001-015` | `INTEGRATION` | `TASK-001-012` |
| `TEST-001-016` | `AC-001-016` | `INTEGRATION` | `TASK-001-070` |
| `TEST-001-017` | `AC-001-017` | `INTEGRATION` | `TASK-001-015` |
| `TEST-001-018` | `AC-001-018` | `SECURITY` | `TASK-001-092` |
| `TEST-001-019` | `AC-001-019` | `SECURITY` | `TASK-001-051` |
| `TEST-001-020` | `AC-001-020` | `INTEGRATION` | `TASK-001-090` |
| `TEST-001-021` | `AC-001-021` | `INTEGRATION` | `TASK-001-101` |
| `TEST-001-022` | `AC-001-022` | `E2E` | `TASK-001-050` |
| `TEST-001-023` | `AC-001-023` | `E2E` | `TASK-001-050` |
| `TEST-001-024` | `AC-001-024` | `E2E` | `TASK-001-050` |
| `TEST-001-025` | `AC-001-025` | `E2E` | `TASK-001-050` |
| `TEST-001-026` | `AC-001-026` | `ACCESSIBILITY` | `TASK-001-053` |
| `TEST-001-027` | `AC-001-027` | `ACCESSIBILITY` | `TASK-001-053` |
| `TEST-001-028` | `AC-001-028` | `E2E` | `TASK-001-055` |
| `TEST-001-029` | `AC-001-029` | `INTEGRATION` | `TASK-001-061` |
| `TEST-001-030` | `AC-001-030` | `API` | `TASK-001-070` |
| `TEST-001-031` | `AC-001-031` | `SECURITY` | `TASK-001-092` |
| `TEST-001-032` | `AC-001-032` | `SECURITY` | `TASK-001-095` |
| `TEST-001-033` | `AC-001-033` | `INTEGRATION` | `TASK-001-073` |
| `TEST-001-034` | `AC-001-034` | `INTEGRATION` | `TASK-001-064` |
| `TEST-001-035` | `AC-001-035` | `INTEGRATION` | `TASK-001-064` |
| `TEST-001-036` | `AC-001-036` | `INTEGRATION` | `TASK-001-064` |
| `TEST-001-037` | `AC-001-037` | `E2E` | `TASK-001-099` |
| `TEST-001-038` | `AC-001-038` | `INTEGRATION` | `TASK-001-072` |
| `TEST-001-039` | `AC-001-039` | `INTEGRATION` | `TASK-001-071` |
| `TEST-001-040` | `AC-001-040` | `INTEGRATION` | `TASK-001-098` |
| `TEST-001-041` | `AC-001-041` | `E2E` | `TASK-001-099` |
| `TEST-001-042` | `AC-001-042` | `INTEGRATION` | `TASK-001-089` |
| `TEST-001-043` | `AC-001-043` | `INTEGRATION` | `TASK-001-057` |
| `TEST-001-044` | `AC-001-044` | `INTEGRATION` | `TASK-001-052` |
| `TEST-001-045` | `AC-001-045` | `INTEGRATION` | `TASK-001-044` |
| `TEST-001-046` | `AC-001-046` | `ACCESSIBILITY` | `TASK-001-053` |
| `TEST-001-047` | `AC-001-047` | `E2E` | `TASK-001-054` |
| `TEST-001-048` | `AC-001-048` | `E2E` | `TASK-001-055` |
| `TEST-001-049` | `AC-001-049` | `INTEGRATION` | `TASK-001-094` |
| `TEST-001-050` | `AC-001-050` | `INTEGRATION` | `TASK-001-057` |
| `TEST-001-051` | `AC-001-051` | `E2E` | `TASK-001-056` |
| `TEST-001-052` | `AC-001-052` | `E2E` | `TASK-001-056` |

### SEC-TEST ↔ controles

El mapeo de controles reproduce exactamente el de `security.md` y no añade ni
retira ningún control.

| Prueba negativa | Controles aprobados | Responsable final de la evidencia |
|---|---|---|
| `SEC-TEST-001-001` | `SEC-001-001` | `TASK-001-008` |
| `SEC-TEST-001-002` | `SEC-001-001`, `SEC-001-030` | `TASK-001-008` |
| `SEC-TEST-001-003` | `SEC-001-002`, `SEC-001-003`, `SEC-001-008`, `SEC-001-019`, `SEC-001-025`, `SEC-001-028` | `TASK-001-092` |
| `SEC-TEST-001-004` | `SEC-001-006`, `SEC-001-007` | `TASK-001-012` |
| `SEC-TEST-001-005` | `SEC-001-006`, `SEC-001-007` | `TASK-001-012` |
| `SEC-TEST-001-006` | `SEC-001-011`, `SEC-001-012` | `TASK-001-064` |
| `SEC-TEST-001-007` | `SEC-001-013`, `SEC-001-029` | `TASK-001-068` |
| `SEC-TEST-001-008` | `SEC-001-004`, `SEC-001-005` | `TASK-001-068` |
| `SEC-TEST-001-009` | `SEC-001-015`, `SEC-001-016` | `TASK-001-071` |
| `SEC-TEST-001-010` | `SEC-001-009`, `SEC-001-015` | `TASK-001-098` |
| `SEC-TEST-001-011` | `SEC-001-010` | `TASK-001-098` |
| `SEC-TEST-001-012` | `SEC-001-017`, `SEC-001-029` | `TASK-001-096` |
| `SEC-TEST-001-013` | `SEC-001-026` | `TASK-001-069` |
| `SEC-TEST-001-014` | `SEC-001-022` | `TASK-001-095` |
| `SEC-TEST-001-015` | `SEC-001-020`, `SEC-001-024` | `TASK-001-100` |
| `SEC-TEST-001-016` | `SEC-001-025` | `TASK-001-058` |
| `SEC-TEST-001-017` | `SEC-001-018`, `SEC-001-019`, `SEC-001-031` | `TASK-001-099` |
| `SEC-TEST-001-018` | `SEC-001-014` | `TASK-001-064` |
| `SEC-TEST-001-019` | `SEC-001-023` | `TASK-001-056` |
| `SEC-TEST-001-020` | `SEC-001-028` | `TASK-001-070` |
| `SEC-TEST-001-021` | `SEC-001-027` | `TASK-001-051` |
| `SEC-TEST-001-022` | `SEC-001-032` | `TASK-001-092` |
| `SEC-TEST-001-023` | `SEC-001-029`, `SEC-001-030`, `SEC-001-033` | `TASK-001-094` |
| `SEC-TEST-001-024` | `SEC-001-019`, `SEC-001-033` | `TASK-001-056` |
| `SEC-TEST-001-025` | `SEC-001-020`, `SEC-001-024`, `SEC-001-033` | `TASK-001-093` |
| `SEC-TEST-001-026` | `SEC-001-034` | `TASK-001-093` |
| `SEC-TEST-001-027` | `SEC-001-023`, `SEC-001-035` | `TASK-001-056` |
| `SEC-TEST-001-028` | `SEC-001-036` | `TASK-001-057` |
| `SEC-TEST-001-029` | `SEC-001-021` | `TASK-001-097` |

### Cobertura de requisitos, criterios y controles

Cada fila nombra las tareas que responden por la evidencia final de las pruebas
que verifican ese identificador aprobado.

| ID aprobado | Tareas responsables por pruebas |
|---|---|
| `AC-001-001` | `TASK-001-013` |
| `AC-001-002` | `TASK-001-050` |
| `AC-001-003` | `TASK-001-050` |
| `AC-001-004` | `TASK-001-102` |
| `AC-001-005` | `TASK-001-102` |
| `AC-001-006` | `TASK-001-008` |
| `AC-001-007` | `TASK-001-008` |
| `AC-001-008` | `TASK-001-008` |
| `AC-001-009` | `TASK-001-010` |
| `AC-001-010` | `TASK-001-068` |
| `AC-001-011` | `TASK-001-068` |
| `AC-001-012` | `TASK-001-014` |
| `AC-001-013` | `TASK-001-014` |
| `AC-001-014` | `TASK-001-070` |
| `AC-001-015` | `TASK-001-012` |
| `AC-001-016` | `TASK-001-070` |
| `AC-001-017` | `TASK-001-015` |
| `AC-001-018` | `TASK-001-092` |
| `AC-001-019` | `TASK-001-051` |
| `AC-001-020` | `TASK-001-090` |
| `AC-001-021` | `TASK-001-101` |
| `AC-001-022` | `TASK-001-050` |
| `AC-001-023` | `TASK-001-050` |
| `AC-001-024` | `TASK-001-050` |
| `AC-001-025` | `TASK-001-050` |
| `AC-001-026` | `TASK-001-053` |
| `AC-001-027` | `TASK-001-053` |
| `AC-001-028` | `TASK-001-055` |
| `AC-001-029` | `TASK-001-061` |
| `AC-001-030` | `TASK-001-070` |
| `AC-001-031` | `TASK-001-092` |
| `AC-001-032` | `TASK-001-095` |
| `AC-001-033` | `TASK-001-073` |
| `AC-001-034` | `TASK-001-064` |
| `AC-001-035` | `TASK-001-064` |
| `AC-001-036` | `TASK-001-064` |
| `AC-001-037` | `TASK-001-099` |
| `AC-001-038` | `TASK-001-072` |
| `AC-001-039` | `TASK-001-071` |
| `AC-001-040` | `TASK-001-098` |
| `AC-001-041` | `TASK-001-099` |
| `AC-001-042` | `TASK-001-089` |
| `AC-001-043` | `TASK-001-057` |
| `AC-001-044` | `TASK-001-052` |
| `AC-001-045` | `TASK-001-044` |
| `AC-001-046` | `TASK-001-053` |
| `AC-001-047` | `TASK-001-054` |
| `AC-001-048` | `TASK-001-055` |
| `AC-001-049` | `TASK-001-094` |
| `AC-001-050` | `TASK-001-057` |
| `AC-001-051` | `TASK-001-056` |
| `AC-001-052` | `TASK-001-056` |
| `NFR-001-001` | `TASK-001-102` |
| `NFR-001-002` | `TASK-001-090` |
| `NFR-001-003` | `TASK-001-050` |
| `NFR-001-004` | `TASK-001-053` |
| `NFR-001-005` | `TASK-001-012`, `TASK-001-015`, `TASK-001-070` |
| `NFR-001-006` | `TASK-001-008`, `TASK-001-014`, `TASK-001-051`, `TASK-001-092` |
| `NFR-001-007` | `TASK-001-015` |
| `NFR-001-008` | `TASK-001-061` |
| `NFR-001-009` | `TASK-001-092`, `TASK-001-095` |
| `NFR-001-010` | `TASK-001-102` |
| `NFR-001-011` | `TASK-001-055` |
| `NFR-001-012` | `TASK-001-051` |
| `NFR-001-013` | `TASK-001-073` |
| `NFR-001-014` | `TASK-001-064` |
| `NFR-001-015` | `TASK-001-099` |
| `NFR-001-016` | `TASK-001-071`, `TASK-001-072` |
| `NFR-001-017` | `TASK-001-099` |
| `NFR-001-018` | `TASK-001-053`, `TASK-001-056` |
| `NFR-001-019` | `TASK-001-054`, `TASK-001-055` |
| `NFR-001-020` | `TASK-001-044`, `TASK-001-052`, `TASK-001-057`, `TASK-001-094` |
| `NFR-001-021` | `TASK-001-056` |
| `REQ-001-001` | `TASK-001-013` |
| `REQ-001-002` | `TASK-001-050`, `TASK-001-053` |
| `REQ-001-003` | `TASK-001-102` |
| `REQ-001-004` | `TASK-001-008` |
| `REQ-001-005` | `TASK-001-010`, `TASK-001-068` |
| `REQ-001-006` | `TASK-001-068` |
| `REQ-001-007` | `TASK-001-014` |
| `REQ-001-008` | `TASK-001-012`, `TASK-001-070` |
| `REQ-001-009` | `TASK-001-015`, `TASK-001-051`, `TASK-001-092` |
| `REQ-001-010` | `TASK-001-070`, `TASK-001-090`, `TASK-001-092`, `TASK-001-095` |
| `REQ-001-011` | `TASK-001-055`, `TASK-001-101` |
| `REQ-001-012` | `TASK-001-050` |
| `REQ-001-013` | `TASK-001-053` |
| `REQ-001-014` | `TASK-001-061` |
| `REQ-001-015` | `TASK-001-102` |
| `REQ-001-016` | `TASK-001-070` |
| `REQ-001-017` | `TASK-001-073` |
| `REQ-001-018` | `TASK-001-064` |
| `REQ-001-019` | `TASK-001-099` |
| `REQ-001-020` | `TASK-001-071`, `TASK-001-072` |
| `REQ-001-021` | `TASK-001-064`, `TASK-001-098` |
| `REQ-001-022` | `TASK-001-054`, `TASK-001-057` |
| `REQ-001-023` | `TASK-001-052`, `TASK-001-053`, `TASK-001-054`, `TASK-001-055` |
| `REQ-001-024` | `TASK-001-044`, `TASK-001-053`, `TASK-001-054`, `TASK-001-055` |
| `REQ-001-025` | `TASK-001-094` |
| `REQ-001-026` | `TASK-001-057` |
| `REQ-001-027` | `TASK-001-056` |
| `REQ-001-028` | `TASK-001-054`, `TASK-001-057`, `TASK-001-089` |
| `SEC-001-001` | `TASK-001-008` |
| `SEC-001-002` | `TASK-001-092` |
| `SEC-001-003` | `TASK-001-092` |
| `SEC-001-004` | `TASK-001-068` |
| `SEC-001-005` | `TASK-001-068` |
| `SEC-001-006` | `TASK-001-012` |
| `SEC-001-007` | `TASK-001-012` |
| `SEC-001-008` | `TASK-001-092` |
| `SEC-001-009` | `TASK-001-098` |
| `SEC-001-010` | `TASK-001-098` |
| `SEC-001-011` | `TASK-001-064` |
| `SEC-001-012` | `TASK-001-064` |
| `SEC-001-013` | `TASK-001-068` |
| `SEC-001-014` | `TASK-001-064` |
| `SEC-001-015` | `TASK-001-071`, `TASK-001-098` |
| `SEC-001-016` | `TASK-001-071` |
| `SEC-001-017` | `TASK-001-096` |
| `SEC-001-018` | `TASK-001-099` |
| `SEC-001-019` | `TASK-001-056`, `TASK-001-092`, `TASK-001-099` |
| `SEC-001-020` | `TASK-001-093`, `TASK-001-100` |
| `SEC-001-021` | `TASK-001-097` |
| `SEC-001-022` | `TASK-001-095` |
| `SEC-001-023` | `TASK-001-056` |
| `SEC-001-024` | `TASK-001-093`, `TASK-001-100` |
| `SEC-001-025` | `TASK-001-058`, `TASK-001-092` |
| `SEC-001-026` | `TASK-001-069` |
| `SEC-001-027` | `TASK-001-051` |
| `SEC-001-028` | `TASK-001-070`, `TASK-001-092` |
| `SEC-001-029` | `TASK-001-068`, `TASK-001-094`, `TASK-001-096` |
| `SEC-001-030` | `TASK-001-008`, `TASK-001-094` |
| `SEC-001-031` | `TASK-001-099` |
| `SEC-001-032` | `TASK-001-092` |
| `SEC-001-033` | `TASK-001-056`, `TASK-001-093`, `TASK-001-094` |
| `SEC-001-034` | `TASK-001-093` |
| `SEC-001-035` | `TASK-001-056` |
| `SEC-001-036` | `TASK-001-057` |

## Gates, etapa y orden de ejecución

| Gate | PR | Promoción | Evidencia y condición de bloqueo |
|---|---|---|---|
| `G0` | Sí | Sí | Alcance, aprobaciones vigentes, referencias, inventario, lockfiles, SHA y digests; cierra solo después del verificador externo y de la protección de rama |
| `G1` | Sí | Sí | Secretos y cadena de suministro antes de cualquier build privilegiado |
| `G2` | Sí | Sí | Formato, lint, tipos y límites de imports |
| `G3` | Sí | Sí | Pruebas unitarias de reglas puras, configuración, tokens, i18n y componentes |
| `G4` | Sí | Sí | Integración, API, contrato en sus dos mitades, red y observabilidad |
| `G5` | Sí | Sí | Build productiva sin catálogo y catálogo aislado, completo y con referencias resueltas |
| `G6` | Sí | Sí | Accesibilidad automatizada y revisión manual dirigida, independiente de los snapshots |
| `G7` | Sí | Sí | E2E, matriz real de navegadores y regresión visual con diffs revisados |
| `G8` | Sí | Sí | Contenedores, cuota efectiva, SBOM ligada a commit y digest |
| `G9` | No | Sí | Baseline autorizado; requiere contexto confiable y autorización operacional |
| `G10` | No | Sí | Promoción confiable después de `G9`, con manifest, procedencia y aprobación |
| `GC` | Sí | Sí | Completitud; se ejecuta siempre y con independencia del resultado de los demás |

- **Etapa PR:** `G0`–`G8` y `GC`, **diez** entradas. `G9` y `G10` requieren
  contexto confiable y autorización operacional, que un PR no confiable no tiene.
- **Etapa promoción:** `G0`–`G10` y `GC`, **doce** entradas.
- El conjunto obligatorio completo es de **doce** entradas: `G0`–`G10` y `GC`.
  `GC` **pertenece** al conjunto obligatorio y no es un verificador externo a él.

### Orden reconstruido de los gates

1. Existen primero los artefactos y workflows de `G0`–`G8` y `GC`
   (`TASK-001-077` a `TASK-001-085`), sobre el manifiesto versionado
   (`TASK-001-076`), que se crea **antes** de que nadie compruebe checks.
2. La protección de rama se configura como control de integración **externo a
   los jobs** (`TASK-001-086`) y el verificador dedicado
   `scripts/ci/verify-required-checks` se crea a continuación
   (`TASK-001-087`).
3. La verificación externa de los required checks se ejecuta contra la
   configuración real (`TASK-001-088`).
4. Solo entonces `G0` cierra su evidencia final (`TASK-001-089`), que depende de
   su propia implementación, del manifiesto, del verificador, de la verificación
   externa y de la protección de rama.
5. La completitud se cierra después (`TASK-001-090`), ejercitando por separado el
   gate que falla, el gate que no llega a ejecutarse y la protección de rama
   divergente. **`GC` no puede detectar su propia ausencia**: esa comprobación la
   realiza la protección de rama y el verificador dedicado, fuera del workflow.
6. `G9` (`TASK-001-099`) se ejecuta después del conjunto exigido para PR, ya
   cerrado por `TASK-001-090`.
7. `G10` (`TASK-001-100`) se ejecuta después de `G9`, dentro de la promoción.

No existe ninguna dependencia circular: `GC` depende de los gates que verifica,
la protección de rama depende de `GC` para conocer el conjunto reportado, el
verificador depende del manifiesto, y el cierre de `G0` depende de todos ellos
sin que ninguno dependa a su vez de ese cierre.

## Camino crítico

El camino más largo del grafo tiene **31 tareas**:

`TASK-001-001` → `TASK-001-002` → `TASK-001-003` → `TASK-001-008` → `TASK-001-009` → `TASK-001-010` → `TASK-001-022` → `TASK-001-025` → `TASK-001-026` → `TASK-001-027` → `TASK-001-028` → `TASK-001-030` → `TASK-001-039` → `TASK-001-060` → `TASK-001-062` → `TASK-001-063` → `TASK-001-068` → `TASK-001-070` → `TASK-001-080` → `TASK-001-085` → `TASK-001-086` → `TASK-001-088` → `TASK-001-089` → `TASK-001-090` → `TASK-001-093` → `TASK-001-094` → `TASK-001-095` → `TASK-001-097` → `TASK-001-099` → `TASK-001-100` → `TASK-001-103`

Por qué este orden es técnicamente válido y no solo acíclico:

1. **No se puede configurar antes de saber qué se instala.** Las versiones,
   hashes y digests preceden al workspace, y el workspace precede al proyecto
   Python, porque la instalación frozen y los scripts normativos dependen de
   ambos.
2. **No se puede arrancar sin configuración validada.** La estructura modular
   depende de la validación de configuración, que falla cerrado antes del bind,
   y la estructura por sí sola no expone ninguna operación: `TASK-001-009` es
   scaffolding y `TASK-001-013` es la tarea que compone, registra las dos rutas
   y arranca el proceso real.
3. **No se puede servir health en el borde antes de que health exista.** La
   entrada pública depende de la operación implementada, no al revés.
4. **La correlación sigue el flujo aprobado y no puede invertirse.** Capturar el
   candidato precede a generar el sustituto; generar el sustituto precede a
   validarlo y restaurarlo; restaurarlo precede a eliminar la cabecera temporal;
   y solo entonces los filtros restantes y el router consumen el identificador
   final. Restaurar antes de generar dejaría respuestas del borde sin
   identificador válido.
5. **Las respuestas locales del borde necesitan el contrato de errores de la
   aplicación**, porque comparten el objeto cerrado `ErrorResponse` y la igualdad
   entre la cabecera y el campo del cuerpo.
6. **El entorno ejecutable precede a toda evidencia operacional.** Compose
   depende de las imágenes y de su cuota efectiva, y ninguna prueba que atraviesa
   el borde puede cerrarse antes de que exista un despliegue real.
7. **Los entornos no productivos se preparan antes de ejercitarlos.**
   `TASK-001-066` materializa preview y staging con sus dos entradas y su
   validación previa al bind, y solo después `TASK-001-067` ejecuta la batería
   de acceso sobre ellos; el entorno local con Compose sigue siendo un
   prerrequisito distinto y no los sustituye.
8. **El acceso a métricas precede al cierre de readiness**, porque la prueba de
   acceso operacional es una de las condiciones que la evidencia de readiness
   debe poder invocar, y el canal obligatorio ya está integrado y con productores
   reales.
9. **La conformidad del borde depende de los fixtures deterministas y de las dos
   entradas**, porque un estado que el cliente nunca recibe no es un estado
   verificado.
10. **`G4` no puede declararse antes de que existan los cierres que orquesta**, y
    `GC` no puede compararse con un conjunto que todavía no se ejecuta.
11. **La protección de rama y su verificación son externas a los jobs**, de modo
    que preceden al cierre de `G0` y al cierre de completitud.
12. **La cadena de suministro se cierra al final** porque su prueba negativa
    exige que todos los recursos estén ya incorporados, incluidos el catálogo,
    los addons, el Collector y las herramientas de carga.
13. **`G9` exige el conjunto de PR cerrado y `G10` exige `G9`**, de modo que la
    promoción nunca precede al baseline ni a los gates que la habilitan.

### Componentes, raíces y tareas finales

El grafo tiene **dos componentes débilmente conexas**:

- la componente principal, con **103 tareas**, cuya raíz es `TASK-001-001`
  —resuelve versiones y procedencia antes de que exista cualquier artefacto— y
  cuya tarea final es `TASK-001-103`, que concilia toda la evidencia;
- una componente aislada de **una** tarea, `TASK-001-104`, que es la revisión
  documental del propio borrador y que por diseño no depende de evidencia
  ejecutada ni es prerrequisito de ninguna tarea de implementación. Su
  aislamiento es intencional y no indica una dependencia omitida.

## Resumen

| Concepto | Valor |
|---|---|
| Tareas totales | 104, con IDs consecutivos de `TASK-001-001` a `TASK-001-104`, sin huecos ni repeticiones |
| Fases | 8 |
| Aristas de dependencia | Todas apuntan a tareas de numeración inferior, por lo que el grafo es acíclico por construcción |
| Componentes débilmente conexas | 2: la principal de 103 tareas y `TASK-001-104` aislada por diseño |
| Camino crítico | 31 tareas |
| Grupos con paralelismo declarado | 10, con 29 tareas involucradas |
| Pruebas `TEST-*` | 52, cada una con su `AC-*` y su tipo aprobados y un único responsable de evidencia final |
| Pruebas `SEC-TEST-*` | 29, con el mapeo de controles exacto de `security.md` y un único responsable de evidencia final |
| Ownership final | 81 pruebas, cada una con exactamente una tarea responsable |
| Controles `SEC-*` cubiertos | 36 |
| Requisitos `REQ-*` cubiertos | 28 |
| Requisitos `NFR-*` cubiertos | 21 |
| Criterios `AC-*` cubiertos | 52 |
| Gates | `G0`–`G10` y `GC`; PR exige `G0`–`G8` y `GC`; promoción exige `G0`–`G10` y `GC` |
| Operaciones HTTP | Exactamente dos: `GET /health` y `GET /ready` |

## Checklist de cierre

- [ ] Todas las tareas `TASK-*` están implementadas o tienen una excepción
      aprobada.
- [ ] Cada tarea referencia requisitos, aceptación y controles de seguridad
      aplicables.
- [ ] Las pruebas requeridas fueron ejecutadas y reportadas, distinguiendo
      pasadas, fallidas y no ejecutadas con su razón.
- [ ] Cada `AC-*` tiene al menos una prueba y evidencia final a cargo de una
      única tarea.
- [ ] Se verificó la autorización operacional; el aislamiento cross-tenant no
      aplica porque `SPEC-001` no crea datos tenant.
- [ ] Las migraciones no aplican; observabilidad y documentación fueron
      revisadas cuando corresponde.
- [ ] Riesgos restantes y pendientes verificables están documentados.

## Control de cambios

- El borrador inicial se redactó el `2026-09-25` como `1.0.0-draft`, después de
  la aprobación de `plan.md` 1.0.0, con 68 tareas y sin aprobación.
- Una revisión independiente del `2026-09-25` detectó cuatro bloqueantes, seis
  hallazgos altos y dos medios. Su corrección reconstruyó el borrador con 102
  tareas y lo reetiquetó como `1.1.0-draft`: orden de gates, ownership de
  readiness, conformidad del borde, deadline interno de aplicación, owners de
  evidencia final, inventario y procedencia, productores reales de eventos,
  límites de transporte y cuotas OCI, sistema visual completo, flujo de
  `X-Request-ID`, consulta del navegador y rutas concretas.
- Una segunda revisión independiente del `2026-09-25` detectó tres hallazgos. Su
  corrección añadió la tarea de integración y arranque del backend y la de
  preparación de preview y staging, y explicitó la dependencia de la prueba
  integrada de eventos hacia el cableado real de readiness; el borrador pasó a
  104 tareas.
- Una revisión final del `2026-09-25` cerró los dos hallazgos residuales: el
  traslado del ownership final de `SEC-TEST-001-007` a `TASK-001-068` y la
  atribución a `TASK-001-013` de la desactivación de las rutas automáticas de
  documentación y de esquema.
- Ninguna de las versiones anteriores llegó a aprobarse ni a publicarse. Por eso
  la primera versión aprobada de este documento es la `1.0.0` registrada a
  continuación, y las etiquetas `1.0.0-draft` y `1.1.0-draft` pertenecen
  exclusivamente al historial del borrador: la primera fue el borrador inicial
  y la segunda, una etiqueta de trabajo usada durante las revisiones. Ninguna
  se incorporó como versión aprobada; no existió downgrade desde una versión
  `1.1.0` aprobada o publicada.
- El `2026-09-25`, Product Owner aprobó explícitamente este documento como
  versión `1.0.0` y autorizó avanzar `SPEC-001` a `Ready for Implementation`.
  Esa aprobación no autoriza ejecutar tareas, implementar código, instalar
  dependencias ni avanzar a `In Progress`. Referencia histórica: Aprobación
  explícita del Product Owner del `2026-09-25` de `tasks.md` versión `1.0.0` de
  SPEC-001 Platform Foundation, comprendiendo sus 104 tareas, sus dependencias,
  sus fases, sus gates, el ownership de evidencia final de las pruebas y sus
  matrices de trazabilidad.
- El primer intento de ejecutar `TASK-001-001` se detuvo antes de editar o
  implementar: la tarea no especificaba las plataformas OCI y anticipaba el
  inventario de acciones CI aún inexistentes. El `2026-09-29`, Product Owner
  autorizó reabrir `tasks.md` como `1.0.1-draft`, fijó `linux/amd64` como única
  plataforma OCI inicial, trasladó la selección y resolución del SHA completo
  de cada acción CI a la tarea que introduzca su workflow y aclaró que `G0` y
  `G1` consumirán después la evidencia de `TASK-001-001`, sin ser condiciones
  ejecutables para cerrarla. Este borrador correctivo requiere revisión y
  aprobación independientes; ninguna tarea ha sido ejecutada. La revisión
  independiente posterior detectó y corrigió seis tecnologías omitidas del
  control temprano, la falta de evidencia verificable para cada «no aplica» y
  la actualización incremental del inventario cuando `TASK-001-077` incorpore
  acciones CI.
- Una revisión final independiente declaró apto para reaprobación el borrador
  `1.0.1-draft`. El `2026-09-29`, Product Owner aprobó explícitamente
  `tasks.md` versión `1.0.1`, que incorpora las correcciones documentales de
  ejecutabilidad. La versión `1.0.0` fue la aprobación anterior y
  `1.0.1-draft` fue su reapertura correctiva. Ninguna tarea fue ejecutada
  durante la reapertura ni por este acto de aprobación: las 104 permanecen
  `Pendiente`.
- El `2026-09-30`, la investigación de `TASK-001-001` detectó que el servicio
  directo de estáticos mediante el filtro `file_server` de Envoy no es apto
  para producción. El Product Owner aprobó mantener Envoy como único límite
  público, agregar `web-static` NGINX interno, fijar las familias OCI de
  Python, Node y Playwright, reutilizarlas en catálogo/fixtures y resolver la
  versión estable exacta y los digests de NGINX en `TASK-001-001`. Esta
  decisión reabre `plan.md` como `1.0.1-draft` y las presentes tareas como
  `1.0.2-draft`. Ambos documentos requieren aprobación independiente; la
  implementación está pausada y las 104 tareas, incluida `TASK-001-001`,
  permanecen `Pendiente`. Los archivos de evidencia de cadena de suministro
  existentes permanecen incompletos y sin aprobar. Esta revisión crea
  `ADR-0003` como borrador de aprobación pendiente y exige aprobarlo antes del
  plan y, después, las tareas. Precisa que OpenAPI 1.1.0 ya está aprobado desde
  el `2026-09-25`; limita `TASK-001-022` al cierre declarativo, asigna la
  prueba negativa interna de métodos a `TASK-001-070`, integra los logs reales
  de NGINX en `TASK-001-075` y corrige en `TASK-001-061` el cierre de los cuatro
  servicios. `TASK-001-001` fue iniciada parcialmente bajo autorización y solo
  produjo los dos documentos de evidencia aún no aprobados; ninguna tarea
  posterior fue ejecutada ni ningún criterio de cierre quedó satisfecho.
- El `2026-10-01`, después de la aceptación y aprobación independiente de
  `ADR-0003` y de la aprobación de `plan.md` 1.0.1, el Product Owner aprobó
  explícitamente `tasks.md` 1.0.2. Se conservan las 104 tareas, sus
  dependencias, fases, gates, owners de evidencia final y matrices de
  trazabilidad. SPEC-001 pasa de `Plan Approved` a `Ready for Implementation`
  únicamente como disponibilidad documental. `TASK-001-001` fue iniciada
  parcialmente en el pasado, continúa `Pendiente` y no se reanuda por este
  acto; ninguna tarea se ejecutó o completó por esta aprobación. La evidencia
  parcial de `docs/supply-chain/` permanece incompleta y sin aprobar; no se
  creó implementación. Reanudar `TASK-001-001` y avanzar a `In Progress`
  requieren autorización posterior e independiente. La implementación sigue
  pausada.

### Aprobación histórica de `tasks.md` 1.0.1

- **Aprobación:** `Aprobado`
- **Aprobado por:** `Product Owner`
- **Fecha de aprobación:** `2026-09-29`
- **Referencia:** Aprobación explícita del Product Owner del `2026-09-29` de
  `tasks.md` versión `1.0.1` de SPEC-001 Platform Foundation, comprendiendo
  sus 104 tareas, dependencias, fases y gates, el ownership de evidencia final
  de las pruebas y las matrices de trazabilidad; la corrección de
  ejecutabilidad de `TASK-001-001`; `linux/amd64` como plataforma OCI inicial;
  la selección y fijación de acciones CI en la tarea que introduzca el
  workflow; la evidencia verificable de cada campo declarado «no aplica»; y
  la actualización coordinada de procedencia e inventario al incorporar
  acciones.

La aprobación histórica de `tasks.md` 1.0.0 figura en el control de cambios.
La aprobación de `tasks.md` 1.0.1 habilitó entonces su disponibilidad
documental; no ejecutó tareas ni autorizó por sí sola avanzar SPEC-001 a
`In Progress`. La autorización posterior y la pausa actual constan en
`spec.md`.

## Aprobación de tareas

- **Aprobación:** `Aprobado`
- **Aprobado por:** `Product Owner`
- **Fecha de aprobación:** `2026-10-01`
- **Referencia:** Aprobación explícita del Product Owner del `2026-10-01` de
  `tasks.md` 1.0.2, comprendiendo sus 104 tareas, dependencias, fases, gates,
  ownership de evidencia final y matrices de trazabilidad, la incorporación
  de la topología aceptada en `ADR-0003` y la alineación con `plan.md` 1.0.1.
  Esta aprobación hace disponible el plan de tareas; no ejecuta ninguna tarea,
  no completa `TASK-001-001`, no valida la evidencia incompleta de
  `docs/supply-chain/` ni crea implementación. Avanzar a `In Progress`
  requiere autorización posterior e independiente.
