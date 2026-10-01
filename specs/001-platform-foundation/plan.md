# Plan de Platform Foundation

## Metadata

- **Referencia:** `SPEC-001`
- **Versión:** `1.0.1`
- **Estado:** `Approved`
- **Responsable:** `Equipo de desarrollo`
- **Autoridad de aprobación:** `Product Owner`
- **Fecha:** `2026-09-21`
- **Última actualización:** `2026-10-01`

## Resumen técnico

Monorepo con backend Python/FastAPI, frontend React/TypeScript y componentes
compartidos en `packages/ui`. El diseño visual `ui-design.md` 1.0.0 aprobado y
el contrato `contracts/openapi.yaml` 1.1.0 aprobado son las fuentes normativas de
presentación y transporte. La foundation es stateless, sin persistencia ni
negocio. Solo implementa dos operaciones backend: `GET /health` y `GET /ready`.
La primera es la única que este plan propone exponer públicamente, con su
respuesta mínima; la segunda pertenece exclusivamente al plano operacional.
Métricas mediante OpenTelemetry/OTLP, logs JSON a stdout y evidencia
reproducible de calidad, seguridad, accesibilidad y rendimiento.

La topología central de esta revisión tiene cuatro servicios: backend, edge
Envoy, `web-static` interno y OpenTelemetry Collector. Envoy es el único límite
HTTP público y termina TLS; entrega los archivos Vite e Inter mediante proxy
exclusivo hacia `web-static`, que no tiene acceso directo desde redes externas.
Esta corrección arquitectónica fue aprobada por el Product Owner el
`2026-09-30` y registrada en `ADR-0003`, aceptado independientemente el
`2026-10-01`. Este plan `1.0.1` fue aprobado mediante un acto posterior e
independiente el `2026-10-01`; la implementación permanece pausada.

[Seguro] `acceptance.md`, `security.md`, `ui-design.md` 1.0.0,
`data-model.md` 1.0.0 y `contracts/openapi.yaml` 1.1.0 conservan sus
aprobaciones. Al aprobarse este plan, SPEC-001 pasó de `Design Approved` a
`Plan Approved` y `tasks.md` 1.0.2 permanecía en `Draft`, con aprobación
pendiente. Posteriormente, el Product Owner aprobó independientemente
`tasks.md` 1.0.2 el `2026-10-01` y SPEC-001 volvió a
`Ready for Implementation`, solo como disponibilidad documental.
`TASK-001-001` no fue reanudada, ninguna tarea fue completada y la
implementación continúa pausada; avanzar a `In Progress` requiere autorización
posterior e independiente.

**Base normativa de las decisiones originales de este plan.** Salvo las dos decisiones
específicas que el Product Owner autorizó expresamente —el nivel ASVS 5.0.0 L1
como baseline con controles L2/L3 seleccionados, y la ausencia de persistencia
de idioma en SPEC-001, ambas registradas en **Preguntas abiertas, A0**—, este
documento original no invocaba otra autorización del Product Owner. Esas dos
autorizaciones alcanzan a su decisión de contenido y **no aprueban ningún
documento**; no llevan fecha ni referencia documental porque no se emitió
ninguna y este plan no la inventa. Las decisiones que delimitan superficie y
acceso se sustentan en artefactos aprobados que delegan expresamente esa
elección al plan:

- exponer públicamente `GET /health` con su respuesta mínima se sustenta en
  `SEC-001-004` («Si el plan requiere exposición pública, esta es la única
  respuesta operativa que puede ser pública y conserva ese contenido mínimo»),
  en `SEC-001-029` («health para probes u operadores y, solo si el plan lo
  expone, respuesta pública mínima») y en `x-public-exposure:
  plan-dependent-minimal-response-only` del contrato;
- materializar cada límite de red o plataforma se sustenta en `SEC-001-029` y
  en la nota de `security.md` que exige que el plan indique ese límite;
- elegir framework UI, librería de componentes, herramienta de catálogo y
  herramienta de regresión visual se sustenta en `spec.md`, **Requisitos no
  funcionales**, que declara expresamente que esas elecciones no se fijan en la
  spec, y en `ui-design.md`, **Fuera de alcance**;
- fijar valores de límites, timeouts, backpressure, pools y presupuestos se
  sustenta en `SEC-001-015` y `SEC-001-016`, que asignan esos valores al plan.

Las decisiones anteriores se aprobaron históricamente con `plan.md` 1.0.0.
La corrección de topología y bases OCI fue aprobada como decisión por el
Product Owner el `2026-09-30`; ese acto no aprobó documentalmente el plan ni
reanudó la implementación. La aprobación independiente de `plan.md` 1.0.1
se registró posteriormente, el `2026-10-01`.

[Suposición] Los presupuestos iniciales son suficientes para medir esta base;
solo las ejecuciones previstas podrán validarlo. No son capacidad demostrada.
Crear o corregir este archivo no autoriza implementación ni `tasks.md`.

## Alcance y no alcance

- Incluido: arranque reproducible, configuración externa, vida/readiness,
  errores seguros, correlación, métricas, logs, contenedores, CI y baseline.
- Incluido: pantalla mínima SimuCenter, ES/EN, tokens, cinco componentes
  mínimos, catálogo ejecutable, accesibilidad y regresión visual.
- Excluido: Identity, autenticación, sesiones, usuarios, roles y permisos de
  producto, organizaciones, tenancy, datos de negocio, tablas o colecciones.
- Excluido: PostgreSQL, Redis, cualquier base de datos, RLS, migraciones,
  caché de aplicación persistente, outbox, jobs de dominio y carga legacy.
- Excluido: navegación de negocio, formularios complejos, tablas, dashboards,
  logo definitivo, rediseño corporativo y tema oscuro completo.
- La evidencia de CI, logs, métricas, archivos versionados y design tokens
  no son entidades persistentes de dominio. No se almacenan preferencias de
  idioma, request ID ni estado de usuario entre procesos o recargas.
- No se crea un endpoint HTTP de métricas, documentación dinámica, diagnóstico
  ni prueba. El esquema FastAPI se inspecciona dentro del proceso de pruebas y
  además se comprueban los estados realmente observables a través del límite
  HTTP, que no añaden operaciones ni rutas.

## Estructura prevista del repositorio

Esta estructura describe trabajo futuro; no crea archivos en esta etapa.

```text
apps/
  backend/
    src/platform_foundation/
      api/                 # transporte y middleware
      application/         # coordinación de vida y readiness
      domain/              # contratos puros necesarios, sin entidades ficticias
      infrastructure/      # configuración, logging y telemetría
      bootstrap/           # composición y ciclo de vida
    tests/                 # unit, integration, contract y seguridad
  frontend/
    src/                   # pantalla inicial, i18n y diagnóstico permitido
    tests/
packages/
  ui/                      # tokens CSS, componentes, historias y pruebas
tooling/                   # reglas compartidas y validadores de contratos
scripts/                   # comandos reproducibles sin dependencia de CI
tests/                     # E2E, accesibilidad, seguridad y baseline
docs/                      # operación, inventarios y evidencia revisada
specs/001-platform-foundation/ # artefactos normativos existentes
```

Workspace pnpm raíz y `pnpm-lock.yaml`; proyecto Python en `apps/backend`
con `uv.lock`. Configuración compartida de TypeScript, formato, lint y pruebas.
No crear paquetes, dominios o interfaces sin consumidor en esta foundation.
El esquema de directorios admite módulos futuros sin copiarlos del legacy.

## Matriz de tecnologías, propósito, versión y procedencia

Consulta de fuentes oficiales: **2026-09-21 UTC**. Las versiones observadas
no sustituyen la resolución exacta ni demuestran compatibilidad del conjunto.
La comprobación de las familias OCI aprobadas y de la limitación del filtro
de archivos de Envoy se añadió el **2026-09-30 UTC**; la revisión no fija
ningún digest nuevo y `TASK-001-001` debe registrar la evidencia reproducible.
Las líneas fijadas por este draft son Python **3.14**, Node **24.21 LTS** y
pnpm **12.5**. No se admiten otras líneas sin revisión del plan.

| Tecnología | Propósito | Versión o resolución prevista | Fuente oficial |
|---|---|---|---|
| Python | Builder y runtime backend; fixtures Python del borde reutilizan su base OCI | `docker.io/library/python:3.14.7-slim-trixie`; digest hijo `linux/amd64` pendiente en `TASK-001-001` | [Imagen oficial](https://hub.docker.com/_/python), [descargas y estado](https://www.python.org/downloads/) |
| Node.js | Builder Vite y catálogo local/CI; no servidor productivo del frontend | `docker.io/library/node:24.21.0-trixie-slim`; digest hijo `linux/amd64` pendiente en `TASK-001-001` | [Imagen oficial](https://hub.docker.com/_/node), [ciclo de versiones](https://nodejs.org/en/about/previous-releases) |
| pnpm | Workspace e instalación JS reproducible | Línea 12.5; observada 12.5.1, compatible con Node 24 | [Instalación](https://pnpm.io/installation), [release 12.5.1](https://github.com/pnpm/pnpm/releases/tag/v12.5.1) |
| FastAPI, Pydantic | API y validación | FastAPI con soporte Python 3.14; Pydantic 2; versiones exactas pendientes de resolver | [FastAPI](https://fastapi.tiangolo.com/release-notes/), [Pydantic](https://docs.pydantic.dev/latest/) |
| Uvicorn | Servidor ASGI, un proceso por contenedor | Exacta pendiente; comprobar soporte Python 3.14 | [Settings](https://www.uvicorn.org/settings/) |
| uv | Dependencias Python con hashes | Exacta pendiente de resolver | [Documentación](https://docs.astral.sh/uv/) |
| Ruff, Pyright, pytest | Lint/formato, tipos y pruebas Python | Exactas pendientes de resolver | [Ruff](https://docs.astral.sh/ruff/), [Pyright](https://github.com/microsoft/pyright), [pytest](https://docs.pytest.org/) |
| React, TypeScript, Vite | UI y build estática | Exactas compatibles con Node 24.21, pendientes de resolver | [React](https://react.dev/), [TypeScript](https://www.typescriptlang.org/), [Vite](https://vite.dev/guide/) |
| Tailwind CSS | Generación de estilos desde variables semánticas | Exacta pendiente; no paleta normativa propia | [Documentación](https://tailwindcss.com/docs) |
| Storybook | Catálogo local/CI | Exacta compatible con React/Vite; addons mínimos inventariados | [Documentación](https://storybook.js.org/docs) |
| Vitest, Testing Library | Unit/integration UI e interacción | Exactas pendientes de resolver | [Vitest](https://vitest.dev/guide/), [Testing Library](https://testing-library.com/docs/) |
| Playwright, axe-core | E2E, visual y accesibilidad automática | Imagen de ejecución `mcr.microsoft.com/playwright:v1.63.0-noble` por digest hijo `linux/amd64`; verificar Node interno y asegurar Node 24.21.0 para herramientas del proyecto; browsers y SO fijados por ejecución | [Imagen oficial Playwright](https://playwright.dev/docs/docker), [axe-core](https://github.com/dequelabs/axe-core) |
| i18next, react-i18next | ES/EN, fallback y selector | Exactas pendientes de resolver | [i18next](https://www.i18next.com/), [react-i18next](https://react.i18next.com/) |
| Inter | Tipografía local aprobada | Archivos exactos, licencia y hashes pendientes de inventario | [Distribución](https://rsms.me/inter/), [licencia](https://github.com/rsms/inter/blob/master/LICENSE.txt) |
| OpenTelemetry SDK, OTLP, Collector | Métricas sin transporte HTTP público | Exactas y compatibilidad de protocolo pendientes de resolver | [Python](https://opentelemetry.io/docs/languages/python/), [Collector](https://opentelemetry.io/docs/collector/configuration/) |
| k6 | Baseline de carga sintética | Exacta pendiente de resolver | [Documentación](https://grafana.com/docs/k6/latest/) |
| OCI, Compose | Contenedores y entorno portable | Especificaciones y CLI exacta registradas al implementar | [OCI](https://opencontainers.org/), [Compose](https://docs.docker.com/compose/) |
| Envoy | Único borde público: TLS, acceso, correlación, límites, timeouts, rate limiting, respuestas locales y proxy; no sirve archivos directamente | Imagen `envoyproxy/envoy:v1.39.1`, con índice e hijo `linux/amd64` ya documentados en R32; el POC aislado no sustituye las pruebas de entrega | [Imagen oficial](https://hub.docker.com/r/envoyproxy/envoy), [Timeouts](https://www.envoyproxy.io/docs/envoy/latest/faq/configuration/timeouts), [Local reply](https://www.envoyproxy.io/docs/envoy/latest/configuration/http/http_conn_man/local_reply) |
| NGINX | Origen interno `web-static` para artefactos Vite e Inter | Imagen oficial Debian 13 `docker.io/library/nginx:<VERSION_ESTABLE_EXACTA>-trixie`; versión e índices/hijo `linux/amd64` pendientes de `TASK-001-001`, sin tag flotante | [Imagen oficial](https://hub.docker.com/_/nginx) |
| GitHub Actions | Orquestación de los scripts | Cada acción por SHA completo revisado, nunca tag | [Seguridad](https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions) |
| Gitleaks, Trivy, Syft | Secretos, vulnerabilidades e inventario/SBOM | Exactas y binarios/imágenes verificados durante implementación | [Gitleaks](https://github.com/gitleaks/gitleaks), [Trivy](https://trivy.dev/), [Syft](https://github.com/anchore/syft) |
| ESLint, Prettier | Reglas JS/TS y formato compartido | Exactas pendientes, solo plugins necesarios | [ESLint](https://eslint.org/docs/latest/), [Prettier](https://prettier.io/docs/) |

[Probable] Python 3.14 figura estable en mantenimiento bugfix, no LTS, y Node 24
figura LTS. Esta afirmación procede de una consulta a documentación externa en la
fecha indicada; no es evidencia contenida en el repositorio y debe reconfirmarse
contra la fuente oficial antes de fijar versiones. [Probable] La combinación es
compatible según requisitos de runtime y documentación; deberá demostrarse con
instalación limpia, build y suite. Solo la ejecución reproducible registrada en
el repositorio podrá etiquetarse [Seguro].
Las versiones exactas no verificadas se declaran pendientes, no se inventan.
Antes de la primera entrega: registrar runtime patch exacto, `packageManager`
exacto, dependencias directas exactas, transitivas resueltas con integridad,
lockfiles frozen, hashes de distribuciones/fuentes y digests OCI por plataforma.
No instalar herramientas con comandos que resuelvan implícitamente la última
versión. Prohibidos `latest`, rangos flotantes y tags mutables en entrega.
Los enlaces a documentación no son selectores de dependencias.
Actualizar mediante PR separado revisado, SBOM diferencial y todos los gates;
revisar avisos al menos semanalmente y antes de cada promoción. Una versión
pendiente bloquea la entrega, no se sustituye por una referencia flotante.

## Componentes afectados

| Componente futuro | Responsabilidad y límite |
|---|---|
| Backend | Vida, readiness, errores, configuración, logs y métricas; sin negocio |
| Frontend | Pantalla pública y diagnóstico técnico mínimo; no consulta readiness |
| packages/ui | Tokens y cinco componentes compartidos; sin acceso a red implícito |
| Edge Envoy | Único límite público y TLS; acceso público exclusivo a health, proxy de estáticos, límites y headers |
| web-static | Origen NGINX interno de archivos Vite e Inter; sin API, proxy backend ni exposición directa |
| Plano operacional | Readiness, OTLP y acceso de operadores/probes, no Identity |
| Tooling y CI | Verificaciones, trazabilidad, cadena de suministro y promoción |
| Catálogo y pruebas | Aislamiento, ejemplos sintéticos, evidencia no productiva |

## Decisiones

- **ADR-0001 Accepted:** monolito modular, dependencias `API → application →
  domain`; infrastructure implementa puertos y bootstrap realiza composición.
  Los routers no contienen reglas de negocio; no se crean microservicios.
- **ADR-0002 Accepted:** condiciona futuras tablas tenant y RLS, no obliga a
  incorporar PostgreSQL en esta spec. `data-model.md` declara no aplicabilidad.
- **ADR-0003 Accepted:**
  `docs/architecture/adr/ADR-0003-envoy-web-static-topology.md` documenta la
  topología aceptada Envoy público → `web-static` NGINX interno. Fue aprobado
  independientemente por el Product Owner el `2026-10-01`, antes de este plan
  `1.0.1`; la topología todavía no está implementada.
- **Contrato primero:** el contrato versionado tiene precedencia sobre la
  generación automática. La igualdad se verifica semánticamente y mediante
  respuestas reales, incluidas las del límite HTTP. `contracts/openapi.yaml`
  1.1.0 está aprobado por el Product Owner desde el `2026-09-25`;
  `tasks.md` 1.0.2 fue aprobado independientemente el `2026-10-01` y SPEC-001
  volvió a `Ready for Implementation` solo como disponibilidad documental.
  La implementación continúa pausada: reanudar `TASK-001-001` y avanzar
  SPEC-001 a `In Progress` requieren autorización posterior e independiente.
- **Vida pública mínima (decisión aprobada en este plan, no implementación):** exponer
  públicamente solo `GET /health` con su respuesta mínima. Se sustenta en
  `SEC-001-004`, `SEC-001-029` y `x-public-exposure` del contrato, que delegan
  esa elección al plan. No amplía readiness, métricas ni catálogo; su
  aprobación documental no autoriza implementarla.
- **Estáticos y mismo origen:** Envoy como único límite HTTP público. Se elige
  porque expone un **plazo total por solicitud sobre el intento upstream**, que
  es requisito de la cadena de plazos de este plan, y porque permite fijar el
  cuerpo de toda respuesta que el propio límite emite. Sustituirlo en SPEC-001
  requeriría un nuevo cambio arquitectónico aprobado y la misma batería de
  límites, acceso, headers y revisión.
- **Origen de estáticos separado:** `web-static` basado en NGINX oficial Debian
  13 `trixie` sirve únicamente Vite e Inter por la red interna. Envoy solo
  hace proxy hacia él; el filtro `file_server` de Envoy queda prohibido para
  esta entrega por estar oficialmente declarado incompleto y no apto para
  producción. El Product Owner aprobó esta corrección el `2026-09-30`.
- **Evidencia del adaptador y su alcance:** una prueba de viabilidad aislada
  ejercitó `Envoy v1.39.1`, digest
  `sha256:57e14a549d7bd43c8d3f6d03e8cfa653e037d4b38e133acd9b54f38c524401b4`,
  contra un upstream **exclusivamente sintético**, fuera del repositorio y sin
  implementación de SPEC-001. Esa ejecución **no es una instalación productiva
  aprobada** ni sustituye ningún gate. La documentación de trabajo de
  `TASK-001-001` registra por separado la imagen R32 y sus digests de índice
  e hijo; esa evidencia sigue incompleta y sin aprobación hasta cerrar la
  tarea. La resolución de R32 no demuestra la nueva topología ni su runtime.
- **Un worker por réplica:** evita multiplicar memoria, colas y conexiones
  implícitamente. Escala horizontal explícita y presupuesto agregado validado.
- **Diseño normativo independiente:** Tailwind implementa variables de
  `ui-design.md`; no aporta colores, tamaños ni componentes alternativos.
- **Evidencia no equivale a producto:** una prueba visual, un health satisfactorio
  o un baseline operativo no demuestra journeys ni capacidad de negocio.

Referencias legacy leídas: `01_CURRENT_ARCHITECTURE.md`,
`06_SECURITY_ANALYSIS.md`, `07_TECH_DEBT.md`,
`09_SIMUCENTER_NEXT_REQUIREMENTS.md` y `11_PERFORMANCE_EVIDENCE.md`, en
`docs/legacy-analysis/`. Sus hallazgos orientan límites, modularidad, i18n,
reducción de logs y métricas válidas; no autorizan copiar módulos ni capacidades.
La evidencia legacy de CPU/RSS inválidos, saturación y descarte de logs exige
pruebas de validez y conciliación. No se heredan sus cifras como objetivos.

## Backend

### Composición y ciclo de vida

FastAPI y Pydantic con configuración tipada antes de admitir tráfico. Lifespan
construye validadores, logger y proveedor de métricas; marca readiness solo
cuando configuración y recursos obligatorios estén inicializados. No existe
base de datos ni dependencia externa de negocio obligatoria. Collector no es
dependencia de vida; su indisponibilidad produce degradación observable y
reintento acotado, no una consulta remota por cada health/readiness.

**Dependencia obligatoria real de readiness.** La foundation tiene exactamente
una: el canal de eventos obligatorios de seguridad y auditoría descrito en
Observabilidad. Cuando ese canal no puede contabilizar un evento obligatorio
—cola llena que no drena, escritura a stdout fallida tras sus reintentos
acotados, o fallo explícito del contador—, la aplicación retira readiness,
responde `503 {"status":"not_ready"}`, emite la señal independiente al
supervisor y termina controladamente si no se recupera en 10 s. Health sigue
significando vida mientras el proceso exista. Esta es la condición productiva
que `TEST-001-011` y `SEC-TEST-001-008` deben ejercitar en primer lugar, y la
evidencia debe nombrarla expresamente como la dependencia obligatoria
ejercitada. La indisponibilidad, la saturación y el timeout de esa dependencia
se expresan siempre con el 503 contratado, nunca con 500.

El doble sintético de readiness se conserva únicamente como caso adicional y
controlado, para ejercitar transiciones difíciles de forzar sobre el canal real
(oscilación rápida listo/no listo, recuperación tras restablecerse). Es
exclusivo de pruebas, no añade una dependencia productiva ficticia y no puede
sustituir al canal real como sujeto primario de la evidencia.

Uvicorn, un worker, sin reload/debug, sin access log individual ni identificación
de versión en headers. ASGI asíncrono; sin tareas CPU pesadas en el request.
Estado compartido limitado a configuración inmutable, contadores y colas acotadas;
no requiere afinidad entre réplicas. No se inicia thread pool de aplicación.
El cierre SIGTERM retira readiness, detiene admisión, drena requests y telemetría,
registra pendientes/fallos y termina antes del límite del contenedor.

### Correlación, errores y contrato mínimo

Middleware exterior de correlación/errores, luego admisión/timeout, instrumentación
y rutas. `X-Request-ID` opcional se recibe como candidato, no como un campo
Pydantic restringido que produzca 400/422. Válido: 1–128 ASCII y
`^[A-Za-z0-9._-]+$`; se devuelve idéntico. Ausente, inválido o ambiguo por
duplicación: descartar completo y generar un identificador nuevo de **16 bytes
obtenidos de un CSPRNG del sistema, codificados como 32 caracteres
hexadecimales en minúscula** (128 bits de entropía). No es un UUID: no lleva
versión, variante, marca de tiempo ni identificador de nodo, y no se deriva en
ninguna parte del valor recibido. Nunca truncar, normalizar ni registrar el
candidato inválido. El formato resultante satisface `ValidRequestId` del
contrato. Requisito ASVS aplicable verificado: **11.5.1 (L2)**, que exige
CSPRNG para todo valor destinado a ser no adivinable; los requisitos L1 de V11
(11.3.1, 11.3.2 y 11.4.1) no aplican porque esta generación no emplea cifrado
de bloque, modo de operación ni función hash. Esta construcción y su cobertura
ASVS describen **el generador de la aplicación**, que es el que ejercitan
`TEST-001-015` y `SEC-TEST-001-004`; no describen el sustituto que genera el
límite HTTP, tratado más abajo.
129 caracteres dentro del presupuesto HTTP deben llegar a este middleware y
obtener respuesta normal con nuevo ID, no rechazo por validación del ID.
Caracteres que hacen inválido el protocolo se prueban también directamente en
la frontera ASGI; no se exige a un parser HTTP aceptar framing inválido.

**El límite HTTP resuelve el identificador antes de contactar al backend.**
La aplicación conserva su middleware para el contexto directo, pero en el
despliegue el candidato del cliente no llega intacto al backend: el límite
decide antes. El mecanismo tiene cuatro pasos, en este orden estricto:

1. **Mutación temprana.** Antes del enrutado y antes de que el límite genere su
   propio identificador, una mutación de cabeceras copia el candidato recibido
   a una cabecera temporal. Si el cliente no envió `X-Request-ID`, esa copia
   queda vacía y la cabecera temporal no se añade, de modo que «ausente» y
   «presente pero vacío» no se confunden.
2. **Generación del sustituto.** El límite escribe incondicionalmente un
   identificador propio en `X-Request-ID`. Ese valor es el sustituto y existe
   antes de cualquier byte hacia el upstream, de modo que toda respuesta que el
   límite emita por su cuenta ya dispone de uno válido.
3. **Filtro de validación, primero de la cadena.** Lee la cabecera temporal y
   la acepta solo si tiene entre 1 y 128 caracteres y cumple
   `^[A-Za-z0-9._-]+$`. Si la acepta, restaura ese valor sobre `X-Request-ID`,
   **carácter a carácter**; si no, deja el sustituto. El filtro **no** aplica
   ningún centinela sobre valores concretos: un identificador válido formado
   por un único guion (`-`) es un `ValidRequestId` y se conserva como cualquier
   otro. Una cabecera repetida se resuelve por la misma regla de validez, sin
   un caso especial: la lectura de cabeceras del adaptador **une los valores
   repetidos con una coma**, la coma no pertenece a `^[A-Za-z0-9._-]+$` y por
   tanto el candidato completo se descarta y queda el sustituto generado. No se
   elige uno de los valores ni se conserva un fragmento. El POC lo midió: con
   `alpha` y `beta` el filtro recibió `alpha,beta` y devolvió un ID generado.
4. **Eliminación de la cabecera temporal.** El filtro la borra siempre, acepte
   o rechace el candidato. El valor hostil no se reenvía al backend, no entra
   en ningún cuerpo y no se registra.

Como el filtro es el primero de la cadena y precede a acceso, frecuencia,
tamaño y enrutado, **el identificador que ve el upstream, el que viaja en la
cabecera `X-Request-ID` de la respuesta y el que aparece en el campo
`request_id` del JSON de cualquier respuesta emitida por el límite son el mismo
valor**, porque los tres se leen del mismo mapa de cabeceras ya resuelto. La
respuesta lleva una sola cabecera `X-Request-ID`, no dos.

[Probable] El sustituto que genera el límite es un identificador con formato de
UUID que satisface `ValidRequestId`. Su generador **no** es el CSPRNG de la
aplicación y la documentación oficial del adaptador no lo caracteriza como
criptográfico. La afirmación ASVS 11.5.1 de este plan se refiere al generador
de la aplicación y no se extiende a este sustituto; determinar si el generador
del límite es adecuado, o sustituirlo, es un pendiente verificable de
implementación y no se da por resuelto aquí.

Toda respuesta documentada, de aplicación o de borde, contiene `X-Request-ID`
válido. Errores públicos: objeto cerrado `ErrorResponse` con `code`, `message`
y `request_id`, sin excepción cruda, conforme a los ejemplos del contrato.

**Taxonomía de resultados no satisfactorios.** Cada condición tiene exactamente
una representación contratada; ninguna se disfraza de otra y ninguna informa
éxito falso:

| Condición | Estado | `code` | Emisor |
|---|---|---|---|
| Rechazo deliberado por frecuencia o admisión agotada | 429 | `RATE_LIMITED` | Borde o middleware de admisión |
| Rechazo por exceder el presupuesto del cuerpo | 413 | `REQUEST_TOO_LARGE` | Borde |
| Acceso operacional denegado y materializado como HTTP | 403 | `ACCESS_DENIED` | Borde o plataforma |
| Vencimiento del plazo de recepción de una solicitud ya encaminada | 408 | `REQUEST_TIMEOUT` | Borde |
| Dependencia obligatoria de readiness no disponible, saturada o expirada | 503 | — (`{"status":"not_ready"}`) | Aplicación |
| Fallo upstream detectado por el borde antes de recibir encabezados válidos del upstream y antes de comprometer encabezados de respuesta downstream (UF, UC, UR) | 502 | `UPSTREAM_UNAVAILABLE` | Borde |
| Vencimiento del plazo upstream total con respuesta completa aún emitible (UT), o de la ociosidad del flujo antes de comprometer encabezados (SI) | 504 | `GATEWAY_TIMEOUT` | Borde |
| Excepción interna no controlada | 500 | `INTERNAL_ERROR` | Aplicación |
| Conexión cerrada antes de poder emitir una respuesta | — | — (no existe estado HTTP) | Transporte |
| Encabezados ya emitidos y transmisión interrumpida después | el estado ya emitido | — (cuerpo incompleto) | Transporte |

Cada fila tiene **un** emisor y **un** código, y ninguna condición se
representa con el código de otra:

- **429** queda reservado **exclusivamente** a límites de frecuencia y de
  admisión. No representa vencimientos de plazo: la propuesta anterior de
  devolver 429 a una solicitud ya admitida que agotó su plazo **queda
  retirada**, porque confundía un vencimiento con un rechazo deliberado.
- **408 `REQUEST_TIMEOUT`** lo emite **el borde** cuando ya resolvió la ruta y
  el destino de una operación contratada pero el cliente no terminó de enviar
  la solicitud dentro del plazo de recepción. El borde **no contacta al
  backend** en este caso, de modo que la respuesta no dice nada de la
  aplicación. No es transporte: la operación ya estaba encaminada.
- **503 `{"status":"not_ready"}`** queda reservado **exclusivamente** a la
  dependencia obligatoria de readiness, y solo lo emite la aplicación. El borde
  **no** lo genera ni lo reescribe: cuando el backend lo produce, atraviesa el
  borde con su cuerpo mínimo intacto y sin convertirse en `ErrorResponse`.
- **502 `UPSTREAM_UNAVAILABLE`** lo emite **el borde** ante un **fallo upstream
  detectado antes de recibir encabezados válidos del upstream y antes de
  comprometer encabezados de respuesta downstream**. **Ambas condiciones deben
  cumplirse**: si el upstream ya entregó encabezados o el borde ya comprometió
  encabezados de respuesta downstream, el fallo posterior **se trata como
  transporte y no puede convertirse en 502**. Esa clase comprende exactamente
  tres causas: **UF**, fallo o timeout de conexión; **UC**, terminación de la
  conexión upstream antes de encabezados; y **UR**, reset upstream antes de
  encabezados. **UF, UC y UR son indicadores distintos y pueden distinguirse
  individualmente**; el contrato los agrupa **deliberadamente** bajo el mismo
  502 cuando ocurren dentro de esa ventana, y **cada indicador se remapea
  explícitamente por separado**. La respuesta pública comparte código y cuerpo,
  mientras el **flag** y el **detalle operacional** conservan la causa
  concreta. **Solo UF se observó en el POC**; **UC y UR son comportamiento
  contratado y quedan pendientes de verificación de implementación**.
- **504 `GATEWAY_TIMEOUT`** lo genera **el borde**, no la aplicación, cuando
  vence el **plazo upstream total** configurado y todavía puede emitirse una
  respuesta completa (**UT**), o cuando vence la **ociosidad del flujo antes de
  comprometer encabezados** y el borde la traduce a este 504 contratado
  (**SI**). Ese plazo cubre desde el fin de la solicitud downstream hasta
  procesar completamente la respuesta upstream, de modo que un 504 **no
  demuestra por sí solo** que el deadline interno de la aplicación haya
  vencido. No cubre el fallo upstream anterior a encabezados, que es 502.
- **500 `INTERNAL_ERROR`** queda reservado **exclusivamente** a excepciones
  internas no controladas. No representa saturación, admisión agotada, timeout
  de readiness, fallo de conexión ni vencimiento de plazo.

**Cierre de conexión sin respuesta.** Si el cliente aborta, la red se corta o
el borde no llega a formar una respuesta, **no existe ningún estado HTTP**. El
cliente observa un fallo de transporte, no un código. Este caso **no se
contabiliza ni se registra como 504**, ni como ningún otro código: las métricas
lo clasifican con `status_class = transport` y `outcome = error`, y los logs
registran el resultado de transporte. Ninguna evidencia puede presentarlo como
un código observado, porque no se observó ninguno. Confundirlo con 504 haría
inverificable la propia prueba del 504.

Las métricas y los logs permitidos siguen diferenciando timeout, admisión y
excepción con `reason` enumerado, y el cliente distingue el rechazo deliberado
del vencimiento y del defecto interno.

**Tamaño del cuerpo, URI y cabeceras son tres presupuestos distintos.** Solo el
primero produce 413:

| Excedente | Tratamiento | Representación |
|---|---|---|
| Cuerpo por encima del presupuesto de 1 KiB | Rechazo deliberado y verificable en el borde | 413 `REQUEST_TOO_LARGE` |
| Línea de solicitud o URI por encima de 2 KiB | Política de transporte del límite HTTP | No contratada; se prueba como transporte |
| Campo de cabecera >8 KiB, agregado >16 KiB o >64 campos | Política de transporte del límite HTTP | No contratada; se prueba como transporte |
| Cuerpo inesperado en GET **dentro** del presupuesto | El borde lo acepta y **lo reenvía al backend**; quien lo consume y lo descarta es la aplicación, no el borde | Respuesta normal de la operación (200) |
| Framing ambiguo (`Content-Length` y `Transfer-Encoding`, o longitud incoherente) | Rechazo de transporte, puede cerrar la conexión | No contratada; se prueba como transporte |

Ninguna de las dos operaciones declara cuerpo de solicitud. Un GET con cuerpo
pequeño no es un rechazo deliberado y, por tanto, **no puede devolver 413**:
inventar ese rechazo produciría un estado no verificable. Los excedentes de URI
y cabeceras tampoco son 413, porque 413 nombra el cuerpo; se resuelven en la
frontera de transporte igual que el framing inválido y se prueban allí, no como
respuestas de una operación contratada.

**Qué hace exactamente el borde con ese cuerpo pequeño.** El POC lo midió y el
resultado corrige una afirmación anterior de este plan: el borde **no lo
descarta**. Lo acepta dentro del presupuesto y lo entrega al backend, que fue
quien lo recibió íntegro en la prueba. El descarte es responsabilidad de la
aplicación, cuyas dos operaciones no declaran cuerpo. El rechazo por tamaño
**se conserva sin cambios**: por encima del presupuesto de 1 KiB el borde
responde 413 `REQUEST_TOO_LARGE` y no reenvía nada. Ambos extremos se probaron:
un cuerpo dentro del presupuesto llegó al upstream y la operación respondió
200; un cuerpo por encima obtuvo 413 con `ErrorResponse` cerrado.

El handler final captura excepciones esperadas y no esperadas, construye la
respuesta segura que corresponda y nunca retorna éxito falso. Desactivar
páginas de debug y las rutas de documentación/esquema automáticas. No añadir
respuestas 422 automáticas ni 400 por un request ID inválido. Fixtures de fallo
se inyectan en pruebas, no por flags accesibles ni rutas adicionales en
producción.

### GET /health

- Única operación backend que este plan propone exponer públicamente, conforme
  a `SEC-001-004` y `SEC-001-029`. La pantalla puede detectar vida o pérdida de
  conexión, sin credenciales, cookies ni sesión, preferentemente same-origin.
- **No entra en comprobaciones de dependencias ni en ninguna cola de trabajo de
  negocio.** Se resuelve en memoria a partir del estado del proceso; no evalúa
  readiness, no consulta el Collector y no comparte la vía de admisión
  reservada al plano operacional.
- 200 exclusivamente `{"status":"alive"}`; no consulta ni enumera dependencias.
- No revela versión, hostname, configuración, causas o detalles internos.
- `Cache-Control: no-store` y `X-Request-ID` conforme al contrato.
- Límites de tamaño, concurrencia y frecuencia configurables y acotados.
- Respuestas contratadas: 200, 408, 413, 429, 500, 502 y 504. El rechazo por
  frecuencia o por capacidad de admisión agotada usa 429 `RATE_LIMITED`; el
  rechazo por exceder el presupuesto del cuerpo usa 413 `REQUEST_TOO_LARGE`;
  408 `REQUEST_TIMEOUT` lo emite el borde si la solicitud, ya encaminada a esta
  operación, no termina de recibirse dentro del plazo de recepción, sin
  contactar al backend; 500 `INTERNAL_ERROR` queda reservado a excepción
  interna no controlada; 502 `UPSTREAM_UNAVAILABLE` lo emite el borde ante un
  fallo upstream detectado antes de recibir encabezados válidos del upstream y
  antes de comprometer encabezados de respuesta downstream (UF, UC o UR); 504
  `GATEWAY_TIMEOUT` lo genera el borde
  al vencer el plazo upstream total con respuesta completa aún emitible (UT) o
  la ociosidad del flujo antes de comprometer encabezados (SI).
- Esta operación es la única que la **entrada pública** sirve. En ella,
  `/ready` no figura en el mapa de rutas: una solicitud a esa ruta obtiene la
  respuesta de ausencia de ruta de la entrada pública, que pertenece al
  transporte de esa entrada y **no** es una respuesta de la operación de
  readiness contratada en la entrada operacional.
- Solicitudes satisfactorias contabilizadas en métricas, sin INFO individual.
- Un fallo de conexión o respuesta no satisfactoria permite indicar
  indisponibilidad, nunca readiness, dependencias ni capacidad de negocio.
- Los cuerpos provienen de las respuestas reutilizables del contrato; no
  inventar un cuerpo alternativo ni añadir cabeceras que revelen límites.

### GET /ready

- Exclusivo del plano operacional, deny by default, probes/operadores
  autorizados por red/plataforma. Nunca se publica al navegador.
- 200 `{"status":"ready"}` cuando está inicializado y listo; 503
  `{"status":"not_ready"}` durante inicio, cierre y ante indisponibilidad,
  saturación o timeout de la dependencia obligatoria de readiness.
- El 503 es resultado normal no satisfactorio de una comprobación procesada,
  no error de API de REQ-001-007; conserva su cuerpo mínimo, no ErrorResponse.
  Ninguna de esas tres condiciones se representa con 500.
- El aislamiento preferente es de red: el cliente no autorizado no alcanza esta
  operación. Cuando el límite elegido materialice la denegación como respuesta
  HTTP en lugar de impedir la conexión, usa 403 `ACCESS_DENIED`, sin indicar si
  la aplicación está lista ni qué regla denegó el acceso.
- Respuestas contratadas: 200, 403, 408, 413, 429, 500, 502, 503 y 504. 408,
  413 y 429 se aplican igual que en health; la vía de admisión operacional
  conserva sus reservas.
- **Esta operación existe únicamente en la entrada operacional.** El contrato
  de readiness se delimita por listener: la entrada pública no la sirve y su
  404 con indicador de ruta no encontrada significa exactamente eso —ausencia
  de ruta en esa entrada—, no un resultado de la operación. Ninguna respuesta
  contratada de `/ready` describe ese caso, y ninguna evidencia puede
  presentarlo como respuesta de readiness.
- Excepción interna no controlada: 500 con `ErrorResponse`. Sin nombres de
  dependencias ni causas internas. `Cache-Control: no-store` y correlación
  también aquí. Ninguna condición de readiness se representa con 500.
- 504 `GATEWAY_TIMEOUT` lo genera el borde al vencer el plazo upstream total
  con respuesta completa aún emitible (UT) o la ociosidad del flujo antes de
  comprometer encabezados (SI); ese plazo cubre desde el fin de la solicitud
  downstream hasta procesar completamente la respuesta upstream, por lo que no
  demuestra por sí solo que el deadline interno de la aplicación haya vencido.
  502 `UPSTREAM_UNAVAILABLE` lo emite ante un fallo upstream detectado antes de
  recibir encabezados válidos del upstream y antes de comprometer encabezados
  de respuesta downstream (UF, UC o UR).
  Ninguno informa del estado de readiness ni nombra dependencia alguna, y ambos son
  distintos del 503, que sí es una comprobación procesada por la aplicación y
  cuyo cuerpo mínimo atraviesa el borde sin alteración.

## Frontend

React/TypeScript/Vite, build estática. Sin menús de negocio ni rutas
funcionales futuras. Consume exclusivamente `packages/ui` para capacidades
cubiertas, sin otra librería visual completa.

El builder usa la base oficial `docker.io/library/node:24.21.0-trixie-slim`
fijada por digest hijo `linux/amd64`. Node no permanece en la imagen productiva.
Los archivos Vite e Inter se copian a la imagen derivada de NGINX de
`web-static`; Envoy es el único punto público que los entrega mediante proxy.
Los archivos estáticos no son operaciones HTTP de la API: esta conserva solo
`GET /health` y `GET /ready`.

### Anatomía de la pantalla inicial

La pantalla inicial se compone de dos regiones, ambas construidas únicamente
con tokens y primitivas aprobadas de `packages/ui`:

1. **Franja sólida azul tinta.** Se implementa como un elemento `<header>`
   semántico compuesto con las primitivas de layout aprobadas (`Container` en
   modo `application`, `Inline`/`Cluster` para alinear su contenido). No se
   crea un componente de navegación nuevo, un sistema visual paralelo ni una
   librería adicional. Su superficie usa `color.navigation`, opaca y sin
   transparencia. Contiene el wordmark tipográfico `SimuCenter`
   (`Inter` 700, 24/32 px, tracking `-0.01em`, color `text.inverse`, exactamente
   como lo fija `ui-design.md` 1.0.0 en **Identidad inicial**) y los controles
   globales permitidos por `ui-design.md`, cuyo texto e iconos usan
   `navigation.text`. Los dos tokens conviven y no son intercambiables:
   `text.inverse` es el color aprobado del wordmark sobre `color.navigation`, y
   `navigation.text` es el de los demás textos e iconos de la franja.
   **No incorpora navegación
   de negocio**, destinos, menús ni agrupaciones: esa capacidad pertenece a la
   spec que introduzca navegación.
2. **Bloque de lectura.** `Container` en modo `reading` (720 px) con el mensaje
   localizado y el indicador de estado técnico.

Dentro de la franja, el `Language selector` usa su variante `on-navigation` y
todo control enfocado en esa superficie dibuja su anillo con
`color.focus.on-dark`, 3 px de grosor y 2 px de separación, inmediato y sin
animación. Sobre las superficies claras se usa `color.focus.on-light`. La
franja y sus controles entran en la cobertura obligatoria de pruebas
responsive (`TEST-001-047`), de accesibilidad (`TEST-001-046`, incluidos el
contraste del wordmark `text.inverse` sobre `color.navigation` y el de
`navigation.text` sobre `color.navigation` y sus estados de navegación) y de
regresión visual (`TEST-001-051`), en cada breakpoint
documentado; la variante `on-navigation` y el token `color.focus.on-dark` no
pueden quedar sin ejemplo ejecutable ni sin baseline.

#### Contraste del wordmark: cálculo y comprobación prevista

El wordmark usa `text.inverse` sobre `color.navigation`, un par que la tabla de
contrastes de `ui-design.md` 1.0.0 **no registra**: esa tabla recoge
`text.inverse` sobre los teales y `navigation.text` sobre las superficies de
navegación, pero no esta combinación. Este plan calcula el valor y fija su
comprobación; **no modifica `ui-design.md`**, que sigue siendo la fuente
normativa.

Cálculo según WCAG 2.2, luminancia relativa sRGB y
`(L_claro + 0.05) / (L_oscuro + 0.05)`:

| Par | Primer plano | Fondo | Luminancia relativa | Contraste | Umbral aplicable | Resultado |
|---|---|---|---:|---:|---|---|
| **Wordmark** | `text.inverse` `#FFFFFF` | `color.navigation` `#0B1F33` | `1.00000` / `0.01290` | **`16.6928:1`** | `4.5:1` texto normal; `3:1` texto grande | **Pasa** |

El wordmark es `Inter` 700 a 24 px, que WCAG considera *texto grande* por ser
negrita ≥18.66 px, de modo que su umbral estricto sería `3:1`; aun aplicando el
umbral más exigente de texto normal, `4.5:1`, el margen es de más de tres veces.
Por comparación, `navigation.text` `#F7FBFC` sobre el mismo fondo da
`16.0246:1`: el wordmark en blanco puro contrasta **algo más**, no menos, de
modo que sustituir `navigation.text` por `text.inverse` en el wordmark no
degrada la accesibilidad.

**Validación del método.** El mismo cálculo reproduce los valores ya aprobados
en `ui-design.md` —`16.0246` frente al `16.0245` registrado para
`navigation.text` sobre `navigation`, y `4.9872` frente al `4.9871` registrado
para `text.inverse` sobre `teal.primary`—, con diferencias solo en el último
decimal por redondeo. El método es por tanto el mismo que produjo la tabla
aprobada.

**Comprobación prevista.** `TEST-001-046` mide este par sobre el render real,
no sobre el valor documentado, y falla si el contraste medido baja del umbral
aplicable; la evidencia registra el par, el valor medido y el umbral. El cálculo
anterior es **previsto por diseño**, no una medición ejecutada: no existe
implementación todavía. Incorporar el par a la tabla normativa corresponde a
`ui-design.md` y a su reaprobación, no a este plan.

### Consulta de health

Consultar health al montar y cada 30 s mientras la página está visible, una
solicitud en vuelo, timeout de cliente 4 s, sin reintentos inmediatos. Pausar
en pestaña oculta; reanudar con una consulta, sin avalancha. Usar
`credentials: omit` y sin caché; no enviar datos, request ID artificial ni
headers innecesarios. Cualquier respuesta distinta de 200 —incluidas 413, 429,
500 y 504— y cualquier fallo de conexión o vencimiento del timeout se presentan como
un único estado de indisponibilidad: no se muestran el código, el cuerpo, la
causa ni detalles de red, y no se infiere readiness ni capacidad de negocio.
Mostrar comprobando/vida detectada/sin conexión con texto técnico inequívoco,
sin exponer detalles de la excepción ni anunciar repetidamente el mismo estado.
El frontend nunca obtiene ruta ni credencial para readiness.

### Idioma

i18next/react-i18next. **La selección manual de idioma no se persiste en
SPEC-001**: vive únicamente en el estado actual de la página. Una recarga
descarta esa selección y vuelve a resolver el idioma según la regla aprobada
—navegador ES o EN, incluidas variantes regionales, y fallback EN—. No se
introduce `localStorage`, `sessionStorage`, IndexedDB, cookies, almacenamiento
de servidor, preferencia de usuario ni preferencia de tenant; esas capas
pertenecen a las specs de Identity y tenancy, que aún no existen, y el orden
futuro usuario → tenant → navegador → fallback queda solo declarado.
No se hace ninguna petición a un proveedor de traducción.
Cambiar idioma sin perder foco ni estado visible; actualizar el idioma del
documento. Catálogos ES/EN completos y revisión de claves faltantes;
pseudolocalización solo en catálogo/pruebas. Expansión general de al menos 30%,
etiquetas breves hasta 100%, wrap permitido; nunca truncar
acciones/instrucciones ni reducir tipografía.

Producción sin llamadas de consola fuera del adaptador de diagnóstico permitido,
deshabilitado por defecto. Cuando esté habilitado operacionalmente, solo eventos
finitos de componente/severidad/estado técnico, sin cuerpos, headers, tokens,
IDs, URLs, stacks ni telemetría remota obligatoria. Pruebas interceptan consola,
red, DOM y bundles con centinelas. No se envían diagnósticos a nuevas APIs.

## Sistema visual, catálogo y regresión visual

### Implementación del diseño aprobado

`ui-design.md` 1.0.0 es fuente normativa: inventariar todos sus tokens y
transformarlos sin reinterpretación en variables CSS de `packages/ui`.
Comparación mecánica nombre/valor/categoría y versión; revisión de semántica.
Tailwind solo consume estas variables; prohibir valores paralelos, utilidades
arbitrarias que sustituyan tokens y estilos duplicados. Una excepción necesita
necesidad, justificación, alcance y criterio de eliminación/incorporación.
No toda duplicación conceptual se automatiza: revisión estructural documentada.

Inter empaquetada y servida localmente, sin CDN: archivos WOFF2 necesarios,
pesos 400/500/600/700, licencia, origen y hashes inventariados; fallback aprobado.
No descargar fuentes de destinos externos al abrir la página. Verificar carga
de los archivos locales antes de capturas.
Aplicar exactamente escala, line-height, espaciado, radios, bordes y sombras
aprobados; superficies opacas. Sin overlays, glassmorphism ni tema oscuro.
Foco de 3 px separado 2 px con `color.focus.on-light` o `color.focus.on-dark`
según fondo adyacente, inmediato sin animación. Reduced motion conserva
información y elimina movimiento no esencial. Recalcular contrastes aprobados.

Button, Language selector, Status indicator, Alert/message y Layout primitives
incluyen anatomía, variantes y estados del diseño, semántica nativa y nombres
accesibles. Matriz explícita default/hover/focus-visible/active/disabled/loading/
success/warning/error con N/A justificado, sin forzar estados a primitivas.
Teclado y foco se verifican en cada estado aplicable, no solo en el ejemplo base.
Language selector nativo con etiqueta, sin banderas; feedback de estado con
texto e icono cuando corresponda, nunca solo color. No modificar alcance.

### Catálogo ejecutable y evidencia visual

Storybook disponible localmente y en CI, nunca en la imagen/bundle productivo.
Su contenedor aislado reutiliza la misma base Node fijada para el builder Vite;
no incorpora una base OCI externa adicional ni se despliega en preview o staging.
Build y ejecución aislados, sin secretos ni montajes amplios del host, sin egress por
defecto; solo código revisado y addons imprescindibles inventariados. Dependencias
se obtienen en fase separada verificada; ejemplos usan recursos locales.
Historias versionadas para tokens, cinco componentes, variantes, estados,
breakpoints, expansión y pseudolocalización; referencias válidas y datos
sintéticos. No existe navegación ni contenido de negocio.

Playwright compara snapshots por navegador, versión, SO/plataforma, viewport,
escala y versión de fuente fijos. Congelar reloj/datos, esperar fuentes/layout,
desactivar animación no esencial. Umbral inicial cero píxeles diferentes dentro
del mismo contexto; cualquier tolerancia posterior exige justificación, revisión
y prueba de que sigue detectando la mutación controlada. No enmascarar controles.
Controles nativos pueden variar: cada navegador/plataforma aprobada usa su propio
baseline; no se exige igualdad entre plataformas, sí contenido, jerarquía,
estados, interacción, accesibilidad y responsive equivalentes.

Inventario de cobertura deriva del diseño: componente × variante × estado
aplicable × breakpoint; adjuntar también idioma/expansión relevantes. Cambiar
controladamente una propiedad visual debe fallar el gate. Mutación de teclado,
semántica o seguridad con captura idéntica debe fallar otro gate independiente.
Nunca actualizar snapshots automáticamente para hacer pasar CI.

Manifest de evidencia enlaza versión/hash del diseño, token/componente/historia,
commit revisado, ejecución, contexto, hashes de baseline/actual/diff, resultado
y revisión/aprobación. Promoción solo desde commit confiable revisado, con
aprobación explícita de los cambios esperados vinculada al PR y sin fallos de
los demás gates. No ejecutar contenido de artefactos para validarlos.
Capturas, diffs y metadatos se inspeccionan con centinelas y controles automáticos;
la imagen requiere además revisión documentada cuando no se analice íntegramente.
Snapshots y baselines no son logs ni métricas. Publicación denegada por defecto;
lectura solo de revisores/procesos autorizados. No contenido real, URLs internas,
tokens ni información sensible, aunque la captura parezca inocua.

## Contrato OpenAPI

Normativo: `contracts/openapi.yaml`, OpenAPI 3.1.0, contrato **1.1.0**,
`x-contract-status: approved`, aprobado por el Product Owner el
`2026-09-25`. La aprobación histórica de 1.0.0 y la vigente de 1.1.0 constan
en `x-approval-history`. Conservar `servers: []`, operationId, schemas,
ejemplos sintéticos y metadata.

La versión aprobada 1.1.0 no añade rutas, métodos ni capacidades: incorpora
exclusivamente las respuestas que el límite HTTP de esta foundation emitirá de forma
deliberada y configurada sobre las rutas ya contratadas. Esas respuestas están
**previstas por el diseño**, no observadas en ejecución: SPEC-001 no tiene
implementación y este plan no puede invocar ninguna ejecución como evidencia.
La versión 1.0.0 no las documentaba, de modo que `AC-001-030` y `SEC-001-028`
no podían verificar el contrato completo de cada operación.

| Operación | operationId | Respuestas contratadas |
|---|---|---|
| GET /health | getProcessHealth | 200 HealthAlive; 408 RequestTimeout; 413 RequestTooLarge; 429 TooManyRequests; 500 InternalServerError; 502 UpstreamUnavailable; 504 GatewayTimeout |
| GET /ready | getApplicationReadiness | 200 ReadinessReady; 403 AccessDenied; 408 RequestTimeout; 413 RequestTooLarge; 429 TooManyRequests; 500 InternalServerError; 502 UpstreamUnavailable; 503 ReadinessNotReady; 504 GatewayTimeout |

Respuestas reutilizables nuevas, todas sobre el `ErrorResponse` cerrado, con
`X-Request-ID` válido y obligatorio y mensajes públicos estables:
`AccessDenied` (403, `ACCESS_DENIED`), `RequestTimeout` (408,
`REQUEST_TIMEOUT`), `RequestTooLarge` (413, `REQUEST_TOO_LARGE`),
`TooManyRequests` (429, `RATE_LIMITED`), `UpstreamUnavailable` (502,
`UPSTREAM_UNAVAILABLE`) y `GatewayTimeout` (504, `GATEWAY_TIMEOUT`). Ninguna
revela límites configurados, ventanas, claves de origen, dependencias, reglas
de acceso ni configuración, y ninguna añade cabeceras que permitan inferirlos.
En particular, `GatewayTimeout` no expone el valor del plazo, cuál de sus dos
causas venció ni la identidad del upstream, y `UpstreamUnavailable` no expone
cuál de sus tres causas se produjo. `ReadinessNotReady` **no cambia**: conserva su cuerpo mínimo
`{status: not_ready}` y no se convierte en `ErrorResponse`.

Respecto del borrador anterior, la revisión contractual 1.1.0 incorporó
`RequestTimeout` y `UpstreamUnavailable` y **delimitó** `GatewayTimeout` a los vencimientos de
plazo de upstream. La declaración previa de que SPEC-001 no documentaría 504
sigue sustituida. `SERVICE_OVERLOADED` sigue sin existir: no se contrata.
Ningún requisito, criterio de aceptación ni control aprobado prohibía estos
códigos; `spec.md`, `acceptance.md` y `security.md` no los mencionan, por lo
que la incorporación no contradice ningún artefacto aprobado. La
reaprobación del contrato se registró el `2026-09-25`.

**Qué demostró el POC y qué no.** La prueba de viabilidad aislada observó
directamente, a través del límite y contra un upstream sintético, las
respuestas 403, 408, 413, 429, 502 y 504, la conservación exacta y la
sustitución íntegra del identificador, y el paso sin alteración del 503
`{"status":"not_ready"}`. El 502 se observó **solo en su causa UF** —rechazo de
conexión y timeout de conexión—, y el 504 en sus causas **UT** y **SI**.
**No ejercitó** el 200 de `/ready`, el 500 `INTERNAL_ERROR` ni las causas **UC**
—terminación de la conexión upstream antes de encabezados— y **UR** —reset
upstream antes de encabezados—: son **comportamiento contratado pendiente de
verificación de implementación**, no observaciones. Ninguna de esas
observaciones es evidencia de conformidad de SPEC-001, que no tiene
implementación, y ninguna se extiende a la implementación futura.

Usar los nombres reales de componentes del contrato al implementar; validar
parameters/headers/schemas/responses reutilizables y objetos cerrados. Separar
IncomingRequestId de ValidRequestId: entrada sin restricciones contractuales
de longitud/patrón; salida y ErrorResponse con restricciones estrictas.

**La verificación de contrato tiene dos mitades y ninguna sustituye a la otra.**
La primera compara el esquema generado en memoria con el contrato normalizado:
operaciones exactas, códigos, `required`, `additionalProperties` y headers. Esa
comparación, por sí sola, es ciega a lo que emite el borde. La segunda ejercita
el despliegue real a través del límite HTTP y comprueba que el conjunto de
estados y cuerpos realmente observables sobre `/health` y `/ready` coincide
exactamente con el contratado: provoca 413 con una solicitud sobredimensionada,
429 superando la frecuencia configurada, 403 desde un contexto operacional no
autorizado cuando la denegación se materializa como HTTP, **408 enviando
lentamente un cuerpo pequeño de una solicitud ya encaminada**, **502 en sus
cuatro casos separados —UF por rechazo de conexión, UF por timeout de conexión,
UC por terminación de la conexión upstream antes de encabezados y UR por reset
upstream antes de encabezados—**, **504 con un upstream que no responde dentro
del plazo upstream total (UT) y con otro que agota la ociosidad del flujo antes
de comprometer encabezados (SI)**, y verifica que no aparece ningún estado ni cuerpo fuera del
contrato. Comprueba además, en la entrada operacional, que un 503
`{"status":"not_ready"}` producido por el backend **llega al cliente sin
remapeo, con su cuerpo mínimo y sin `ErrorResponse`**. Los casos de 408, 502 y
504 exigen comprobar que la respuesta llega **completa**, porque un estado que
el cliente nunca recibe no es un estado verificado; que un cierre de conexión
sin respuesta se registra como transporte y no como 504; y que una respuesta
cuyos encabezados ya se emitieron y luego se interrumpe conserva ese estado con
cuerpo incompleto y tampoco se contabiliza como 504. Un estado observable no
documentado hace fallar G4. La comparación nunca sobrescribe el contrato para
aceptar diferencias.

El borde debe además garantizar que **ningún estado propio del adaptador
escapa al contrato** sobre estas dos rutas: los estados que el adaptador
produce de forma nativa ante un fallo o un vencimiento de upstream se traducen
a los códigos contratados mediante su configuración de respuestas locales, y
esa traducción se selecciona **solo por el indicador de resultado del propio
adaptador**. Queda prohibido seleccionar el código por umbrales de duración:
esa técnica se probó y clasifica mal en silencio en cuanto el plazo de conexión
baja del umbral.

**Remapeo exigido de las respuestas locales nativas.** El adaptador emite
respuestas locales propias para las tres causas de fallo upstream anteriores a
encabezados. **UF, UC y UR son indicadores distintos y pueden distinguirse
individualmente**, de modo que **cada indicador se remapea explícitamente por
separado** al 502 `UPSTREAM_UNAVAILABLE` contratado, con `ErrorResponse`
cerrado y `X-Request-ID` obligatorio, según esta correspondencia:

| Causa | Condición exacta | Respuesta local nativa del adaptador | Remapeo exigido |
|---|---|---|---|
| **UF** | Fallo o timeout de conexión upstream | Estado local del adaptador por conexión no establecida | 502 `UPSTREAM_UNAVAILABLE` |
| **UC** | Terminación de la conexión upstream **antes de encabezados** | Estado local del adaptador por conexión terminada sin respuesta | 502 `UPSTREAM_UNAVAILABLE` |
| **UR** | Reset upstream **antes de encabezados** | Estado local del adaptador por reset del flujo upstream | 502 `UPSTREAM_UNAVAILABLE` |

El remapeo se aplica **únicamente cuando la causa ocurre antes de recibir
encabezados válidos del upstream y antes de comprometer encabezados de
respuesta downstream**. **Ambas condiciones deben cumplirse** para que el borde
pueda generar el 502 contratado: si el upstream ya entregó encabezados o el
borde ya comprometió encabezados de respuesta downstream, el fallo posterior se
trata como transporte y **no puede convertirse en 502**. Tres delimitaciones lo
acotan y ninguna es opcional:

- El **503 `{"status":"not_ready"}` originado por la aplicación no entra en
  este remapeo**: es una respuesta upstream con encabezados propios ya
  recibidos y **atraviesa el borde sin alteración**, con su cuerpo mínimo y sin
  convertirse en `ErrorResponse`.
- Una **terminación o un reset posteriores a unos encabezados ya
  comprometidos** quedan **fuera** del remapeo: son un **fallo de transporte**
  con estado previo y **no se convierten en 502 ni en 504**. El estado ya
  emitido no puede sustituirse.
- Un **vencimiento de plazo** nunca se remapea a 502: UT y SI usan 504, según
  la sección de plazos.

**UF se observó en el POC. UC y UR son comportamiento contratado y quedan
pendientes de verificación de implementación**; el remapeo de las tres se
declara aquí como contrato exigible al adaptador, no como resultado medido.

**Alcance exacto de «ningún estado fuera del contrato».** La exigencia se
aplica a las dos operaciones contratadas, identificadas por la coincidencia
exacta de ruta y método sobre el listener que las sirve. No convierte en
operación contratada lo que no lo es: una ruta que un listener no sirve, un
método no permitido, un framing inválido o un URI fuera de presupuesto son
**política de transporte** y se prueban como tal. En particular, `GET /ready`
dirigido al listener público **no invoca la operación de readiness**: esa ruta
no existe en su mapa y la respuesta pertenece al transporte, no al contrato.
Sin esta delimitación, `AC-001-030` sería inverificable, porque cualquier ruta
desconocida parecería un estado no documentado de una operación.

El límite HTTP puede además rechazar framing y métodos no permitidos; un
protocolo irrecuperable puede cerrar la conexión sin formar respuesta, y ese
caso se prueba como política de transporte. El request ID inválido aislado no
activa ninguno de estos rechazos. No usar el limitador nativo de Uvicorn si
emite un cuerpo distinto del contratado. Ninguna respuesta de transporte puede
reutilizar un código contratado para disfrazarse de respuesta de operación.

## Base de datos

**No aplica**, conforme a `data-model.md` 1.0.0 aprobado. Ninguna entidad,
tabla, colección, relación, índice, migración, tenant_id, RLS, auditoría ni
retención de dominio. Ninguna DB es dependencia obligatoria de readiness.
No confundir colas en memoria, archivos de configuración, request ID, traducciones,
tokens, logs o evidencia con persistencia de aplicación. Consultas, duración
SQL y pools de DB se registran N/A en inventario y reportes, no como ceros.
Si surge persistencia: detener ese trabajo; actualizar y aprobar spec,
acceptance, security y data-model; diseñar ownership, transacciones, constraints,
índices, aislamiento, retención y migraciones; aplicar ADR-0002 si hay datos tenant.

## Configuración y arranque

Pydantic valida tipos, rangos, longitudes, enumeraciones y relaciones antes del
bind operativo. Configuración externa por variables y archivos montados de
solo lectura; secretos operacionales solo donde se necesitan, nunca en build
args, imágenes, frontend ni valores de ejemplo utilizables. Cargar secretos
desde archivos restringidos; el log de rechazo solo nombra campo y causa segura.
No ejecutar expansión de shell ni evaluar contenido de configuración.

Inventario obligatorio: entorno (local/ci/nonprod/production), rol de superficie,
orígenes/hosts permitidos, límites completos, presupuesto agregado y número de
réplicas, destino OTLP y confianza TLS, niveles/colas de logs, catálogo habilitado
solo local/CI, autorización/destino/expiración de carga y política de retención.
Backend no necesita credenciales de producto. El frontend recibe solo configuración
pública allowlisted; ni un prefijo de variable basta para considerarla segura.

Fallo cerrado si faltan obligatorios, hay valores fuera de rango, destino
arbitrario, debug productivo, límites ilimitados, readiness/OTLP públicos,
catálogo en build de producción o presupuesto excedido. Entradas inesperadas
se rechazan dentro del namespace de configuración de cada componente.
Un validador común de inventario se ejecuta en local/CI/despliegue; no se confía
solo en variables declaradas: inspeccionar límites efectivos del contenedor.
Config inválida impide arranque listo y emite señal sanitizada; no imprimir dumps.

Arranque reproducible futuro: verificar runtimes exactos, instalar frozen con
uv/pnpm, validar configuración, construir imágenes, arrancar servicios mínimos,
comprobar health público y ready desde plano operacional, abrir pantalla.
Documentar comandos idénticos en scripts y CI, puertos locales, prerequisitos,
variables requeridas y recuperación sin secretos; probar checkout limpio.

## Seguridad

### Acceso operacional portable

| Superficie | Local | CI | Preview/staging | Despliegue |
|---|---|---|---|---|
| Pantalla pública | Envoy en loopback → `web-static` interno | Envoy en red efímera → `web-static` interno | TLS solo en Envoy público → `web-static` interno | TLS solo en Envoy público → `web-static` interno |
| Health público mínimo | Proxy exacto desde mismo origen | Probar desde red pública simulada | Única ruta backend en el mapa del listener público | Única ruta backend autorizada en listener público |
| Readiness | Backend sin puerto publicado; probe/operador entra por proceso autorizado | Red interna distinta, prueba denegada desde cliente público | **Listener operacional separado** y obligatorio (ver abajo); ausente del mapa público | Puerto/backend sin ruta pública; reglas de red solo probes/operador |
| Métricas OTLP | Collector en red interna sin puerto publicado | Receptor interno y lectura de evidencia autorizada | Collector sin puerto publicado; solo red operacional | Red operacional y TLS/mTLS fuera del host; ninguna ruta pública |
| Origen `web-static` | Solo red Envoy–estáticos; sin puerto publicado | Igual, en red efímera | Sin puerto publicado ni DNS público; solo Envoy lo alcanza | Sin acceso directo desde redes externas; solo Envoy lo alcanza |
| Catálogo | Loopback, contenedor aislado, inicio explícito | Solo job aislado; no publicación | Excluido del artefacto; no se publica | Excluido; publicación separada requiere autorización nueva |
| Carga | Perfil apagado, autorización y destino de prueba explícitos | Job manual confiable y aislado | Solo con autorización operacional vigente y destino permitido | Solo entorno no productivo autorizado; producción bloqueada |
| Evidencia/promoción | Directorio privado a operador/revisor | Acceso restringido, promoción confiable revisada | Acceso restringido a operador/revisor | Denegada salvo proceso y revisión autorizados |

#### Topología pública y origen de estáticos

```text
Cliente → Envoy público (TLS, acceso, correlación, límites y proxy)
             ├→ backend: solo rutas API aprobadas
             └→ web-static: solo Vite e Inter por red interna
```

Envoy conserva el listener operacional separado, RBAC/allowlist, timeouts,
rate limiting, headers y mapeo de respuestas locales. Es el único límite
HTTP público. Su mapa público evalúa, en este orden conceptual:

1. `/ready` se trata explícitamente como **ruta ausente** en esta entrada: no
   se reenvía ni al backend ni al fallback SPA y nunca produce un 200 de SPA.
   La exclusión se expresa como guardia negativa anterior al catch-all, no
   como una operación API ni como una ruta upstream pública para `/ready`.
2. La coincidencia exacta `GET /health` se dirige al backend; se conserva la
   respuesta mínima y la taxonomía contratada, incluido el 503
   `{"status":"not_ready"}` cuando lo origine la aplicación en la entrada
   operacional correspondiente.
3. Solo los métodos `GET` y `HEAD` de archivos estáticos se envían a
   `web-static`; un fallback SPA restringido sirve la pantalla, sin capturar
   `/ready`, rutas de API ni archivos sensibles.
4. Ninguna otra operación de API se crea. El servicio de archivos no añade
   rutas a OpenAPI ni un endpoint público de health o métricas.

`web-static` es un origen NGINX de responsabilidad única: entrega únicamente
los artefactos inmutables Vite y la tipografía Inter. No termina TLS, no hace
proxy al backend, no incorpora lógica de negocio ni sirve documentación,
catálogo o archivos fuente. Su configuración también excluye `/ready` del
fallback SPA, incluso ante una solicitud desde la red interna: no devuelve
`200` ni la pantalla por esa ruta. Su imagen deriva de la imagen oficial NGINX sobre
Debian 13 `trixie`; la versión estable exacta, el índice y el hijo
`linux/amd64` siguen pendientes de `TASK-001-001`. La referencia de entrega
usa el digest hijo y plataforma explícita, nunca un tag mutable.

Solo Envoy puede alcanzarlo mediante una red interna dedicada o segmentación
equivalente con política deny by default. No tiene puerto publicado, DNS
público ni conexión directa desde redes externas. Escucha en un puerto interno
no privilegiado y ejecuta como usuario numérico no root, con rootfs de solo
lectura, capacidades eliminadas, `no-new-privileges` y `tmpfs` únicamente en
las rutas de estado temporal necesarias. Un único worker, autoindex apagado,
solo `GET`/`HEAD`, configuración mínima sin módulos ni funciones dinámicas
innecesarias. Si se necesita healthcheck de contenedor, es estrictamente
interno y no añade operación ni ruta pública.

Envoy aplica los headers de seguridad y de caché apropiados a HTML, archivos
Vite versionados y fuentes, con `Content-Type` correcto y sin revelar versión;
NGINX no introduce una política pública contradictoria. Sus logs de acceso y
error van a stdout/stderr y usan el esquema estructurado cuando corresponda,
con correlación propagada desde Envoy cuando exista contexto válido. Nunca
registran cuerpos, secretos, credenciales ni query strings sensibles. Se
limitan y revisan por volumen; los eventos de rechazo y sus contadores siguen
siendo responsabilidad de Envoy. No se abre `/metrics`: cualquier métrica
necesaria usa únicamente el canal interno aprobado. Imagen, archivos y
configuración se inventarían, escanean, incluyen en SBOM y promueven por
digest con trazabilidad de procedencia.

#### Mecanismo obligatorio y portable de acceso a `/ready` en preview/staging

`SEC-001-029` exige que el plan materialice cada límite y que las pruebas
ejerzan contextos permitidos y denegados. Local y CI ya lo tienen. Preview y
staging carecían de un mecanismo obligatorio, de modo que el aislamiento
quedaba a discreción del despliegue. Este plan **decide** el mecanismo y lo
expresa **como contrato de configuración, no como producto**: es exigible y
verificable sin depender de un proveedor concreto, y ya no se ofrece como una
lista de opciones abiertas.

**Dos entradas distintas, nunca la misma.**

| | Entrada pública | Entrada operacional |
|---|---|---|
| Quién entra | Cualquier cliente de Internet, incluido el navegador | Probes de plataforma y principales de operador nombrados |
| Qué sirve | Proxy de estáticos desde `web-static` y coincidencia exacta `GET /health`; nunca lee archivos locales | La coincidencia exacta `GET /ready` |
| Dirección de escucha | Interfaz enrutable públicamente, con TLS | Dirección **no enrutable públicamente** del segmento operacional |
| `/ready` en su mapa de rutas | **Ausente**; la ruta no existe en este listener | Presente |
| Restricción del tráfico | Ninguna más allá de límites y TLS | Segmento no público **más** allowlist de peers evaluada sobre la dirección del peer validado, nunca sobre cabeceras de forwarding |
| Por defecto | Activo | **Denegado**: si no se configura, el listener operacional no se crea y `/ready` queda inalcanzable |

**Campos obligatorios del inventario de configuración.** Cualquier plataforma
debe poder satisfacerlos; ninguno nombra un proveedor:

```text
edge.public.listen            # dirección y puerto públicos
edge.public.routes[]          # debe contener /health; NO puede contener /ready
edge.operational.listen       # dirección y puerto operacionales
edge.operational.routes[]     # debe contener /ready
edge.operational.allowed_peers[]   # CIDR/identidades; no vacío, no 0.0.0.0/0
```

**Decisión adoptada para preview/staging.** Ya no se enumeran alternativas: el
mecanismo es **un listener operacional enlazado exclusivamente a la red privada
del entorno, sin ninguna ruta pública a `/ready`**, que admite únicamente
probes y operadores inventariados mediante reglas de red `deny by default`. En
concreto:

1. **Enlace exclusivo a la red privada.** `edge.operational.listen` se enlaza a
   una dirección de la red privada del entorno de preview/staging. No se
   publica puerto, no se crea registro DNS público y no se asocia a ningún
   balanceador o ingress con alcance a Internet.
2. **Sin ruta pública a `/ready`.** El listener público no contiene `/ready` en
   su mapa de rutas: no es que lo deniegue, es que **no lo sirve**. No se
   admite prefijo, alias, reescritura, redirección ni passthrough que pueda
   alcanzarlo desde la entrada pública.
3. **Reglas de red deny by default.** La política del segmento privado deniega
   todo por defecto y solo permite los orígenes **inventariados**: los probes
   de la plataforma y los principales de operador registrados. La decisión se
   evalúa sobre la **dirección del peer validado**; las cabeceras de forwarding
   recibidas del cliente se descartan y se reconstruyen desde ese peer.
4. **Sin identidad de producto.** No se introduce login, API key ni sesión: la
   autorización es de red y de plataforma, conforme a `SEC-001-029`, y se
   documenta con principal, recurso, acción, vencimiento y evidencia.

Local, CI y el alcance productivo **conservan sus decisiones ya existentes**:
loopback y contenedor aislado en local, red interna distinta con prueba
denegada desde cliente público en CI, y puerto/backend sin ruta pública con
reglas de red solo para probes y operador en despliegue. Esta decisión añade el
eslabón que faltaba —preview/staging— sin alterar los otros tres.

**Campos obligatorios y validación previa al despliegue.** A los cinco campos
anteriores se añaden los que hacen exigible esta decisión:

```text
edge.operational.network          # identificador de la red privada del entorno
edge.operational.public_exposure  # debe ser false; no admite otro valor
edge.operational.inventory[]      # principal, tipo (probe|operator), origen, vencimiento
```

El validador común **falla cerrado antes del bind**, y por tanto antes de que
el entorno acepte tráfico, si: `/ready` aparece en `edge.public.routes`;
`edge.operational.listen` resuelve a una dirección públicamente enrutable o
fuera de `edge.operational.network`; `public_exposure` no es `false`;
`allowed_peers` está vacío, abarca todo el espacio de direcciones o incluye un
origen que no figura en `inventory[]`; o cualquier entrada del inventario
carece de principal, tipo, origen o vencimiento, o está vencida. Ninguna de
estas comprobaciones depende del proveedor: se resuelven sobre el propio
inventario de configuración antes del despliegue.

**Pruebas desde contextos permitidos, denegados y con origen falsificado.** La
batería de conformidad se ejecuta contra el entorno de preview/staging y solo
afirma comportamiento observable en las dos entradas:

| # | Caso | Contexto de la prueba | Resultado exigido |
|---|---|---|---|
| 1 | **Permitido: probe** | Probe inventariado dentro de la red privada | `GET /ready` → 200 `{"status":"ready"}` o 503 `{"status":"not_ready"}`, con `X-Request-ID` válido |
| 2 | **Permitido: operador** | Principal de operador inventariado y vigente | Igual que el anterior; la evidencia registra principal y vencimiento |
| 3 | **Denegado por red** | Contexto público hacia el listener operacional | La conexión TCP **no se establece**; no hay respuesta HTTP que inspeccionar |
| 4 | **Denegado por ruta** | Contexto público hacia el listener público | `GET /ready` no alcanza la operación; respuesta de transporte, nunca 200/503 de readiness |
| 5 | **Denegado por inventario** | Origen dentro de la red privada pero **no inventariado** | Denegado; estar en la red no basta, la allowlist es explícita |
| 6 | **Denegado por vencimiento** | Principal inventariado pero **vencido** | Denegado; el vencimiento se evalúa, no se ignora |
| 7 | **Denegado materializado como HTTP** | Plataforma que responde en vez de descartar | 403 `ACCESS_DENIED`, sin indicar si la aplicación está lista ni qué regla denegó |
| 8 | **Origen falsificado: `X-Forwarded-For`** | Contexto público con `X-Forwarded-For` de un peer inventariado | Denegado; la cabecera recibida se descarta y se reconstruye desde el peer validado |
| 9 | **Origen falsificado: `X-Real-IP` / `Forwarded`** | Contexto público con esas cabeceras suplantando un origen permitido | Denegado por la misma razón; ninguna cabecera de origen concede acceso |
| 10 | **Origen falsificado hacia el listener público** | Cabeceras de origen operacional contra la entrada pública | Sigue sin existir la ruta; la falsificación no crea `/ready` donde no está |
| 11 | **Por defecto** | `edge.operational.*` sin configurar | `/ready` inalcanzable desde todos los contextos |

Los casos 8 a 10 son los que impiden que la restricción se degrade a confianza
en cabeceras, que es el modo habitual de fallo de este control. Esta batería es
la prueba de contextos permitidos y denegados que `SEC-001-029` exige, y
extiende `TEST-001-010` y `SEC-TEST-001-007` a preview/staging.

Separar listener público de acceso directo al backend. Proxy público permite
solo la coincidencia exacta health, no prefijos, normalizaciones ambiguas ni
passthrough genérico; probar codificación, segmentos y rutas desconocidas.
Las redes internas no publicadas contienen backend, Collector y el segmento
aislado de `web-static`, al que solo llega Envoy; el operador usa acceso
administrativo de la plataforma al contenedor/red. En producción sin aislamiento
comprobable no se permite arrancar la entrega. No inventar login/API key de
producto. Autorización operacional documenta principal de plataforma, recurso,
acción, vencimiento y evidencia; no significa autorización de negocio.
El frontend no obtiene ruta ni credencial para readiness. La prueba externa
debe negar acceso directo al backend y a superficies operacionales.
El borde elimina forwarding recibido del cliente y lo reconstruye desde el
peer validado; ASGI confía solo en la allowlist explícita del proxy, nunca en
todos los emisores. Rate limiting usa ese origen comprobado, sin conservarlo
en logs ni métricas. La IP no se considera identidad ni autorización de producto.

### Headers, CORS y contenido

- Same-origin por defecto: allowlist cross-origin vacía. Configuración excepcional
  solo con orígenes exactos autorizados, nunca reflejar Origin arbitrario.
  Credenciales deshabilitadas; no cookies. Método de aplicación permitido GET,
  header solicitado opcional `X-Request-ID`; no Authorization ni headers arbitrarios.
- Si se habilita cross-origin, el límite resuelve preflight OPTIONS como protocolo,
  no crea operación FastAPI: valida origen/método/headers, sin credenciales,
  `Vary: Origin`, expone solo `X-Request-ID`, max-age 600 s. Origen no permitido
  sin ACAO y rechazado. CORS no sustituye aislamiento de readiness.
- CSP productiva: `default-src 'none'; script-src 'self'; style-src 'self';
  img-src 'self'; font-src 'self'; connect-src 'self'; object-src 'none';
  base-uri 'none'; frame-ancestors 'none'; form-action 'none'`.
  Sin inline/eval ni conexiones externas; no endpoint de reportes CSP en esta spec.
- `X-Content-Type-Options: nosniff`, `Referrer-Policy: no-referrer`,
  `Permissions-Policy: camera=(), microphone=(), geolocation=(), payment=(), usb=()`;
  Content-Type correcto, JSON para API; `X-Frame-Options: DENY` como compatibilidad.
- HSTS solo en límite TLS productivo: `max-age=31536000`; no preload ni
  includeSubDomains implícitos sin validar propiedad de todos los subdominios.
  API no acepta transporte público sin TLS; no redirigir transparentemente
  requests de API inseguros. TLS 1.2 mínimo, preferir 1.3, certificados validados
  y públicamente confiables para exposición externa. Fuera del host, también
  cifrar proxy-backend y comunicaciones operacionales, sin fallback inseguro.
- Sin headers de versión detallada, directorios listables, TRACE, archivos
  fuente/configuración/mapas sensibles ni metadatos del repositorio desplegados.
- Storybook puede requerir frames propios: política separada solo local/CI,
  origen propio y frame-ancestors propio, nunca comodines ni herencia en producción.
  HMR solo local aislado; cada excepción técnica queda inventariada y probada.
- HTML/SVG/iconos/fuentes se revisan antes de empaquetar; rechazar scripts,
  handlers, referencias externas, contenido activo no autorizado y HTML sin
  saneamiento. Preferir texto React y atributos seguros, no HTML crudo ni eval.

Controles SEC-001-001 a SEC-001-036 se materializan en esta sección y las de
recursos, observabilidad, CI y catálogo. La matriz de seguridad identifica su
verificación, sin sustituir ni reducir el diseño aprobado. Riesgos aceptados:
**Ninguno**. No se presupone excepción por estar en foundation o en un draft.

## Límites, timeouts, backpressure y presupuestos

[Suposición] Valores iniciales conservadores para medición, no promesa de
capacidad. Se configuran externamente dentro de presupuestos; cambios deben
validarse y quedar registrados con la ejecución. No elevarlos automáticamente.

| Recurso | Valor inicial y límite aplicable | Mecanismo y observación |
|---|---|---|
| Backend por réplica | 1 vCPU; 512 MiB; 1 worker; 128 PIDs | Límites OCI efectivos, CPU acumulada/RSS/eventos OOM |
| Edge Envoy | 0.5 vCPU; 128 MiB; 128 PIDs | Único límite público; imagen mínima no root, conexiones y rechazos |
| `web-static` | 0.25 vCPU; 64 MiB; 1 worker; límite PIDs efectivo documentado | NGINX interno no root; CPU/RSS/cuota efectiva, `tmpfs` acotado |
| Collector | 0.5 vCPU; 256 MiB; 128 PIDs | Memoria, cola y exportación acotadas |
| Generador k6 separado | 1 vCPU; 256 MiB | No comparte cuota con backend; registrar utilización |
| Catálogo/build o browser CI | 2 vCPU; 2 GiB por job; un browser worker | Excluidos runtime; secuenciar según presupuesto runner |
| Admisión backend | 64 activos: 60 públicos y 4 reservados operacionales | Semáforos independientes, no pool de DB |
| Espera de admisión | Cola pública 32, operacional 4; espera máxima 100 ms | Al llenarse o expirar, fallo seguro y contador |
| Conexiones | 256 sockets por réplica; backlog 128; 100 requests por conexión | Límite de plataforma/servidor verificado bajo carga |
| Headers | 8 KiB por campo; 16 KiB agregado; 64 campos; línea 2 KiB | Frontera HTTP, sin reducir a 128 el header recibido |
| Body | Máximo 1 KiB de lectura; las dos operaciones no consumen body | Exceso → 413; cuerpo pequeño inesperado se descarta; framing ambiguo → transporte |
| Frecuencia health | 20 requests/s por clave de origen; burst 40; global 100/s por límite HTTP | Rate limit en borde; no es una tasa soportada demostrada |
| Estado del rate limiter | 4096 claves máximo, TTL 60 s; clave no registrada ni exportada | IP solo en memoria operacional; nunca dimensión/log; límite global permanece |
| **Plazo de encabezados de la solicitud** | **1 s** desde el primer byte de los encabezados | Defensa antislowloris; al vencer, el borde cancela el flujo y **no** produce estado HTTP |
| **Plazo total de recepción de la solicitud** | **2 s** desde el inicio de la solicitud; al vencer, el borde emite 408 | Acota encabezados **y** cuerpo; ver precedencia más abajo |
| Conexión upstream | 500 ms; al fallar o vencer, el borde emite 502 (causa UF) | Total de su fase; incluye rechazo y timeout, que el borde no distingue al elegir el código. UC y UR usan el mismo 502 sin depender de este valor |
| Ociosidad del flujo upstream (`SI`) | 2.5 s entre eventos; al vencer **antes de comprometer encabezados**, el borde lo traduce al 504 contratado | Guarda **entre lecturas**; **nunca** el límite de duración total. Vencida tras comprometer encabezados, es fallo de transporte, no 504 |
| **Plazo upstream total del borde** (`route.timeout`, `UT`) | **3 s** desde que la solicitud downstream está **completamente recibida**; al vencer **con respuesta completa aún emitible**, el borde emite 504 | Límite **total**; cubre desde el fin de la solicitud downstream hasta procesar completamente la respuesta upstream —conexión, envío, procesamiento y lectura íntegra—. Verificado en POC: un upstream que gotea un byte cada 400 ms fue cortado a los 3.001 s |
| Emisión de una respuesta local del borde | Presupuesto 50 ms desde el vencimiento | Cuerpo fijo pequeño más `X-Request-ID`; sin consultar el upstream |
| Deadline interno de aplicación | 2 s desde la admisión del request, con cancelación | **Contenido** en el plazo upstream de 3 s una vez admitida la solicitud. Su vencimiento no es observable como 504: un 504 no demuestra por sí solo que este deadline haya vencido |
| Cliente frontend | Timeout de la consulta de health 4 s | Plazo **del cliente**, no una garantía del borde; ver la advertencia de composición más abajo |
| Readiness | Evaluación local máxima 500 ms; probes cada 10 s | No son consultas a DB ni dependencia remota por request |
| Cierre | Drain requests 5 s; telemetría/logs hasta 5 s; total 15 s; gracia OCI 20 s | Cancelar, contabilizar pendientes y avisar al supervisor |
| OTLP por proceso | Un canal gRPC; máximo 2 exports en vuelo; colección cada 5 s | Sin pool de DB; timeout 2 s y sin threads ilimitados |
| Cola de métricas | 10 lotes de hasta 512 KiB; máximo 5 MiB por proceso | Sin etiquetas arbitrarias; pérdida observable al llenarse |
| Collector | Cola 20 lotes de hasta 512 KiB; máximo 10 MiB; export timeout 2 s | Reintentos 1/2/4 s, máximo 30 s, sin cola en disco |
| Logs normales | 1024 registros, máximo 2 KiB cada uno | Cola en memoria acotada; agregación y contador |
| Logs obligatorios seguridad | Cola separada 1024 × 2 KiB; espera máxima 100 ms | No sampling; fallo explícito y retirada de readiness |
| Escritura stdout | Deadline 100 ms; reintentos 1/2/4 s, ventana máxima 10 s | Worker de salida acotado, sin bloquear event loop |

### Fases, timeouts y deadline total

**Tres clases de límite, no una.** Confundirlas es lo que hacía inválida la
cadena anterior:

- **Timeout de conexión:** tiempo máximo para *establecer* una conexión.
  Ocurre una vez y sí es un total de su fase.
- **Timeout entre operaciones (inter-read / inter-write):** tiempo máximo entre
  dos lecturas o escrituras *sucesivas*. **Se reinicia con cada operación
  exitosa**, de modo que **no acota la duración total**: un emisor que entregue
  un byte justo antes de cada vencimiento puede extender la fase
  indefinidamente. La guarda de ociosidad del flujo upstream es de esta clase.
- **Deadline total:** tiempo máximo de una fase completa, medido desde un
  instante declarado y aplicado por cancelación.

Una guarda entre operaciones **no es el límite total del borde** y este plan no
la presenta como tal.

**Fases secuenciales, instante de inicio y clase de cada límite.** La recepción
de la solicitud y el intento upstream son fases **consecutivas**, no anidadas:
la segunda no empieza hasta que la primera termina.

| # | Fase | Valor | Empieza a contar desde | Clase | Resultado al vencer |
|---|---|---|---|---|---|
| 1 | Recepción de encabezados (cliente → borde) | **1 s** | El primer byte de los encabezados | Total de su fase | Flujo cancelado; **sin estado HTTP** |
| 2 | Recepción total de la solicitud (cliente → borde) | **2 s** | El inicio de la solicitud | Total de su fase | **408 `REQUEST_TIMEOUT`**, sin contactar al backend |
| 3 | Conexión upstream (borde → backend) | 500 ms | Que el borde decide reenviar | Total de su fase | **502 `UPSTREAM_UNAVAILABLE`** (causa UF) |
| 4 | Envío upstream (borde → backend) | — | Que la conexión upstream está establecida | Dentro del total de la fase 6 | Lo acota el plazo total de upstream |
| 5 | Procesamiento (backend), deadline interno de aplicación | **2 s, deadline total** | **La admisión del request en el middleware** | **Total, por cancelación** | Respuesta propia de la aplicación |
| 6 | **Intento upstream completo** (`route.timeout`, causa UT) | **3 s** | **Que la solicitud downstream está completamente recibida** | **Total, por cancelación** | **504 `GATEWAY_TIMEOUT`** si todavía puede emitirse una respuesta completa |
| 7 | Ociosidad del flujo upstream (causa SI) | 2.5 s | Cada evento de lectura o escritura la reinicia | Entre operaciones | **504 `GATEWAY_TIMEOUT`** si vence **antes de comprometer encabezados**, dentro del total de la fase 6 |
| 8 | Envío de la respuesta (borde → cliente) | — | Los encabezados ya emitidos | No acotable a un estado | Interrupción = fallo de transporte |

**Precedencia entre las fases 1 y 2, que hay que fijar y no copiar.** Las dos
corren a la vez sobre la misma solicitud y **gana la que vence primero**. Si el
plazo total de recepción se fija por debajo del de encabezados, el segundo
queda inalcanzable: una solicitud que se atasca en los encabezados producirá
siempre el 408 del total y la defensa antislowloris nunca actuará. Esa
inversión es exactamente la que tenía la configuración del POC, con 1 s de
total y 5 s de encabezados, y por eso este plan fija **encabezados 1 s ≤
recepción total 2 s**. El validador rechaza cualquier configuración en la que
el plazo de encabezados supere al de recepción total.

**Precedencia entre las fases 7 y 6.** La ociosidad es una guarda **dentro** del
total y nunca lo extiende, pero sí puede adelantarlo: con 2.5 s de ociosidad, un
upstream que enmudece produce el 504 a los 2.5 s y no a los 3 s. El POC lo
midió. El plazo realmente observable es por tanto `min(ociosidad, total)`, y la
evidencia debe declarar cuál de los dos venció.

**El plazo total de upstream es un límite propio, no una guarda de ociosidad.**
`edge.upstream.total_deadline = 3 s` acota el intento upstream **completo**
—conexión, envío, procesamiento y lectura íntegra de la respuesta— desde un
único instante de inicio: **el momento en que la solicitud está completamente
recibida**, no el primer byte del cliente. La guarda de ociosidad (2.5 s)
permanece **dentro** de ese total y **nunca lo extiende**: un upstream que
entregue un byte antes de cada vencimiento reinicia la guarda pero no el total,
que sigue corriendo y vence igual. Es un requisito exigible del adaptador: un
borde que no ofrezca un total nativo por solicitud **no es admisible**. El
adaptador elegido lo ofrece de forma nativa y el POC lo midió: un upstream que
goteó un byte cada 400 ms fue cancelado a los **3.001 s** con el indicador de
vencimiento total, sin que el goteo lo prolongara.

**Relaciones exigibles entre plazos, y lo que el borde no puede prometer.**
Este plan sostiene **exclusivamente** tres relaciones de contención y
precedencia, todas comprobables sobre la configuración del borde y de la
aplicación:

```text
request_headers_timeout = 1 s   ⊂   request_timeout = 2 s
(request_headers_timeout, request_timeout)   preceden al inicio de   route.timeout = 3 s
deadline interno de aplicación = 2 s   ⊂   plazo upstream = 3 s   (una vez admitida la solicitud)
```

- `request_headers_timeout = 1 s` está **contenido** en
  `request_timeout = 2 s`: ambos corren sobre la misma solicitud downstream y
  el de encabezados nunca puede superar al total, según la precedencia fijada
  arriba.
- Ambas fases de recepción **preceden** al inicio de `route.timeout = 3 s`, que
  empieza a contar cuando la solicitud downstream está completamente recibida.
- El **deadline interno de aplicación de 2 s** está **contenido** en el plazo
  upstream de 3 s **una vez admitida la solicitud**.

**Recepción downstream y procesamiento upstream son fases consecutivas**, no
anidadas: la segunda no empieza hasta que la primera termina. De ahí que
ninguna de las relaciones anteriores se componga en una cota única, y este plan
**no la presenta como suma ni como garantía**.

**No existe garantía end-to-end de menos de 4 s desde el navegador, ni garantía
alguna sobre la velocidad de entrega al cliente.** El borde impone cada plazo
por separado; **no impone su composición**, ni ningún plazo medido desde el
primer byte del cliente, y **no impone en absoluto** la entrega de la respuesta
al navegador: una vez comprometidos los encabezados, la velocidad de lectura
del cliente remoto no está acotada por configuración alguna. Ninguna evidencia
puede afirmar que una respuesta se emita o se entregue completa antes de los
4 s del timeout del navegador.

El timeout de 4 s del frontend es una decisión **del cliente** para dejar de
esperar y presentar indisponibilidad; no es una garantía del borde ni un
presupuesto que el borde reparta. Cuando el cliente abandone, observará un
fallo de transporte, que es exactamente lo que ya prevé la sección de consulta
de health.

**Propiedades que deben probarse, no suponerse.**

1. **Cancelación en la aplicación** a los 2 s contados **desde la admisión**,
   no desde la conexión ni desde la llegada de headers.
2. **Cancelación en el borde** a los 3 s contados **desde que la solicitud está
   completamente recibida**, con independencia de que el upstream siga
   entregando bytes.
3. **Respuesta de un solo tramo**: cuerpo JSON pequeño, sin *streaming* ni
   `Transfer-Encoding: chunked`, en una sola escritura.
4. **Precedencia de los plazos de recepción**: encabezados ≤ recepción total, y
   rechazo de la configuración invertida.

`TEST-001-039` verifica los instantes de inicio, el ordenamiento estricto
2 s < 3 s entre aplicación y borde, la precedencia de los plazos de recepción,
el 408 de una solicitud ya encaminada cuyo envío se prolonga, y que un upstream
que **gotea bytes indefinidamente** no extiende el intento más allá del plazo
total. `TEST-001-030` verifica 504 en sus causas UT y SI, 502 en sus causas UF
—por rechazo y por timeout—, UC y UR, el 408 y la preservación del 503
**atravesando el borde** contra un despliegue real, y comprueba el código, el
`ErrorResponse` cerrado, el `X-Request-ID` válido y la ausencia de detalles del
upstream. Cambiar cualquiera de estos valores exige recomprobar las relaciones
de contención y precedencia declaradas arriba y rechazar la configuración si
dejan de cumplirse, lo que el validador comprueba antes del arranque. Ninguna
de esas relaciones se presenta como cota end-to-end hacia el navegador.

### Vencimientos controlados: decisión cerrada

Rechazos en el borde por frecuencia, tamaño de cuerpo y acceso usan los códigos
contratados 429, 413 y 403. Para solicitudes admitidas, el middleware devuelve
429 `RATE_LIMITED` **solo** cuando la cola de admisión está llena o la espera
máxima de 100 ms expira, es decir, ante un límite de frecuencia o admisión.
Readiness no listo conserva su 503, incluido el caso de saturación o timeout de
su dependencia obligatoria. 500 queda reservado a excepción interna no
controlada.

**La propuesta de devolver 429 a una solicitud ya admitida que agotó su plazo
queda retirada.** Era incorrecta: un vencimiento de plazo no es un rechazo
deliberado de frecuencia, y representarlo con 429 habría hecho indistinguibles
dos condiciones con causas y respuestas operativas distintas, además de inflar
las métricas de rate limiting con eventos que no son rechazos.

**Decisión adoptada: el vencimiento se representa con 504 `GATEWAY_TIMEOUT`**,
emitido por el borde al agotarse su deadline total de upstream, y contratado en
ambas operaciones. Sus propiedades:

| Aspecto | Resolución |
|---|---|
| Emisor | El borde lo **genera**; no lo produce la aplicación |
| Cuándo | **UT:** vence el plazo upstream total configurado (3 s) y todavía puede emitirse una respuesta completa. **SI:** vence la ociosidad del flujo (2.5 s) **antes de comprometer encabezados** y el borde la traduce al 504 contratado |
| Qué cubre el plazo | `route.timeout` abarca desde el **fin de la solicitud downstream** hasta **procesar completamente la respuesta upstream**: conexión, envío, procesamiento y lectura íntegra |
| Qué **no** demuestra | Un 504 **no demuestra por sí solo** que el deadline interno de la aplicación haya vencido: el plazo del borde cubre fases que la aplicación no controla |
| Cuerpo | `ErrorResponse` cerrado, `code: GATEWAY_TIMEOUT`, mensaje público estable |
| Correlación | `X-Request-ID` válido y **obligatorio**, igual que toda respuesta documentada |
| Qué no revela | El valor del plazo, cuál de las dos causas venció, la identidad o el estado del upstream, y nada sobre readiness |
| Evidencia del POC | **UT y SI fueron observadas** en la prueba de viabilidad aislada. Esa evidencia **no se extiende** a la implementación futura |
| Prueba | `TEST-001-030` lo ejercita **atravesando el borde** contra un despliegue real |

**Decisión adoptada: el fallo upstream anterior a los encabezados se representa
con 502 `UPSTREAM_UNAVAILABLE`**, contratado en ambas operaciones:

| Aspecto | Resolución |
|---|---|
| Emisor | El borde, no la aplicación |
| Cuándo | Ante un **fallo upstream detectado por el borde antes de recibir encabezados válidos del upstream y antes de comprometer encabezados de respuesta downstream**. **Ambas condiciones deben cumplirse**: si el upstream ya entregó encabezados o el borde ya comprometió encabezados downstream, el fallo posterior es **transporte** y no puede convertirse en 502 |
| Qué comprende | **UF:** fallo o timeout de conexión. **UC:** terminación de la conexión upstream antes de encabezados. **UR:** reset upstream antes de encabezados |
| Por qué se agrupan | **UF, UC y UR son indicadores distintos y pueden distinguirse individualmente.** El contrato los agrupa **deliberadamente** bajo el mismo 502 `UPSTREAM_UNAVAILABLE` cuando ocurren dentro de esa ventana. **Cada indicador se remapea explícitamente por separado.** La respuesta pública comparte código y cuerpo, mientras el **flag** y el **detalle operacional** conservan la causa concreta |
| Estado de cada causa | **UF: observada en el POC.** **UC y UR: comportamiento contratado, pendiente de verificación de implementación**; no se presentan como observadas |
| Remapeo | Las respuestas locales nativas del adaptador asociadas a UF, UC y UR se remapean a este 502 **cada una explícitamente por separado**, y **únicamente cuando la causa ocurre antes de recibir encabezados válidos del upstream y antes de comprometer encabezados de respuesta downstream**; la tabla de correspondencia está en la sección de contrato |
| Fuera de alcance | El 503 `{"status":"not_ready"}` de la aplicación, que **atraviesa el borde sin remapeo**; y la interrupción posterior a encabezados ya comprometidos, que es **fallo de transporte** y no se convierte en 502 ni en 504 |
| Compromiso declarado | La **respuesta pública comparte código y cuerpo** para las tres causas; el **flag** y el **detalle operacional conservan la causa concreta** en el registro por solicitud y en contadores separados. Representar cualquiera de ellas como 504 queda excluido: afirmaría un vencimiento que no ocurrió |
| Prohibido | Seleccionar entre 502 y 504 mediante un umbral de duración. Se probó y clasifica mal en silencio en cuanto el plazo de conexión baja del umbral |
| Cuerpo y correlación | `ErrorResponse` cerrado, `code: UPSTREAM_UNAVAILABLE`, `X-Request-ID` obligatorio |

**Decisión adoptada: el vencimiento de la recepción de una solicitud ya
encaminada se representa con 408 `REQUEST_TIMEOUT`**, contratado en ambas
operaciones:

| Aspecto | Resolución |
|---|---|
| Emisor | El borde, sin contactar al backend |
| Cuándo | Al vencer el plazo total de recepción (2 s) sobre una solicitud cuya ruta y destino el borde ya resolvió |
| Por qué no es transporte | La operación ya estaba encaminada cuando el plazo venció, de modo que el estado es observable sobre una coincidencia exacta de ruta y método y entra en el alcance de `AC-001-030` |
| Qué no dice | Nada sobre la aplicación, su readiness ni sus dependencias: el backend no llegó a recibir la solicitud |
| Condición de existencia | Si el plazo total de recepción no se configura, esta respuesta no puede producirse; contratarlo y no configurarlo deja una respuesta documentada inalcanzable |

En `/health` el 504 tampoco atribuye causa: la operación se resuelve en memoria
sin dependencias ni colas, pero el plazo upstream del borde cubre también
conexión, envío y lectura íntegra de la respuesta, fases que la aplicación no
controla. Por eso **un 504 sobre `/health` no demuestra por sí solo que el
deadline interno de la aplicación haya vencido**, y en ningún caso afirma algo
sobre readiness o dependencias. La aplicación conserva su deadline interno de
2 s, contenido en el plazo upstream de 3 s una vez admitida la solicitud; esa
contención es una relación de configuración exigible, no una predicción sobre
qué respuesta observará el cliente.

**Si la conexión se cierra antes de poder emitir una respuesta HTTP.** Puede
ocurrir: el cliente aborta, la red se corta, el proceso muere o el borde pierde
la conexión antes de escribir el estado. En ese caso **no existe ningún código
HTTP**, y este plan no finge lo contrario:

- El cliente observa un **fallo de transporte** —conexión reiniciada, cerrada o
  expirada—, no un estado. El frontend lo presenta como indisponibilidad, igual
  que cualquier otra respuesta no satisfactoria, sin mostrar detalles de red.
- Las métricas lo registran con `status_class = transport` y
  `outcome = error`, **nunca** como 504 ni como ningún otro código. El catálogo
  ya contempla `transport` precisamente para esto.
- Los logs registran el resultado de transporte con su `reason` enumerado y el
  request ID si existía contexto.
- Ninguna evidencia, informe o prueba puede presentarlo como «504 observado»:
  contabilizar un cierre de conexión como 504 haría inverificable la prueba del
  504 real, que exige recibir el estado y el cuerpo contratados.

**Si los encabezados ya se emitieron y la transmisión se interrumpe después.**
Es un caso distinto del anterior y tiene una consecuencia que este plan no
disimula: **el estado ya emitido no puede sustituirse**. Los encabezados HTTP
no se envían dos veces sobre el mismo flujo, de modo que un fallo posterior no
puede convertirse en 504 ni en ningún otro código. El cliente recibe el estado
que ya se le entregó, con el cuerpo **incompleto**.

- **No se promete ninguna retención de respuesta**, ni de 1 KiB ni de ningún
  otro tamaño, como forma de mantener abierta la ventana de sustitución. Se
  comprobó que el adaptador **no ofrece un límite por respuesta** que permita
  acotar esa retención de forma verificable, y prometer una retención sin un
  tope exigible sería una capacidad inventada.
- El caso se clasifica como **fallo de transporte con estado previo**: las
  métricas usan `status_class = transport` y `outcome = error`, registrando
  aparte el estado que sí se había emitido, y **nunca** como 504.
- La evidencia exigible es la entrada de registro del borde con su indicador de
  resultado, los bytes de cuerpo realmente enviados frente a los declarados, y
  el contador de flujos reiniciados localmente; la solicitud **no** cuenta como
  completada.
- El frontend lo presenta como indisponibilidad, igual que cualquier otra
  respuesta no satisfactoria, sin mostrar detalles de red.

Medir separadamente rechazo de borde, rechazo de admisión, vencimiento de
recepción, fallo upstream anterior a encabezados, vencimiento de upstream,
fallo de aplicación, error de transporte sin estado y error de transporte con
estado previo: son categorías distintas y ninguna se suma a otra. Reservas
evitan que polling público consuma toda la capacidad operacional, no garantizan
vida ante OOM.

### Cálculo agregado y condiciones de rechazo

Sea R réplicas backend, W workers por réplica, C=64 activos, Q=36 en espera,
K=256 conexiones, L=4 MiB para ambas colas de logs, T=5 MiB de cola OTLP.
`P = R × W`; `activos_total = P × C`; `espera_total = P × Q`;
`conexiones_total = R × K`; `canales_OTLP = P`; `exports_en_vuelo = 2 × P`;
`buffers_backend_MiB = P × (L + T)`; `mem_backend_MiB = R × 512`.
W=1 es obligatorio inicialmente; aumentar W exige revisar memoria/CPU/colas
por réplica, no conservar arbitrariamente 512 MiB. El validador rechaza W distinto
de 1 mientras no exista esa revisión. Pruebas ejercitan también el rechazo.

**Techo de buffers.** La fórmula anterior se conserva sin cambios y describe el
consumo calculado: `L + T = 9 MiB` por proceso con los valores actuales. Sobre
ella se fija además un techo inicial verificable de **16 MiB por proceso**, con
presupuesto agregado **`P × 16 MiB`**, que deja margen para cabeceras de lote,
serialización y fragmentación sin permitir que las colas crezcan sin cota. Con
`R = 4` y `W = 1` el agregado permitido es 64 MiB, dentro de
`mem_backend_MiB = 2048`. Una configuración cuyo `buffers_backend_MiB`
calculado exceda `P × 16 MiB`, o cuyo consumo por proceso exceda 16 MiB, se
rechaza antes del arranque aunque la memoria total de la réplica lo admita.
`TEST-001-038` comprueba las dos cosas por separado: que el cálculo real
coincida con la fórmula al variar `R` y `W`, y que el techo por proceso y el
agregado rechacen la configuración excedida.

Presupuesto inicial backend: máximo 4 vCPU, 2048 MiB, 1024 conexiones,
256 activos, 144 en espera, 4 canales OTLP y 16 MiB de buffers por proceso
(64 MiB agregados con R=4); R permitido 1–4, inicial 1. Los demás presupuestos
ya verificados no se modifican.
Con E réplicas Envoy, S réplicas `web-static` y O réplicas Collector, todas
inicialmente 1: `CPU_runtime = R × 1 + E × 0.5 + S × 0.25 + O × 0.5`;
`RAM_runtime_MiB = R × 512 + E × 128 + S × 64 + O × 256`.
R=1, E=S=O=1 implica 2.25 vCPU/960 MiB; R=4 implica 5.25 vCPU/2496 MiB.
El validador inspecciona las cuotas OCI efectivas de los cuatro servicios,
incluido el único worker y los 64 MiB de `web-static`, y rechaza toda
divergencia o proceso no inventariado. Cambiar E, S u O requiere recalcular
el presupuesto y revisar la segmentación y conectividad correspondiente.
Host reserva adicional mínima 25% para sistema;
`capacidad_host >= recursos_asignados / 0.75`, más jobs/generador concurrentes.
No sumar jobs de 2 GiB a un runner sin recalcular; el baseline excluye esos jobs.
Rate limit global agregado `E × 100/s`, con E límites HTTP; inicial E=1.
Cambiar E requiere revisar presupuesto; no asumir que varias réplicas de borde
comparten contadores ni introducir Redis para coordinarlos.

Rechazar valores no positivos/no finitos, colas sin cota, timeouts incompatibles
con shutdown, cadenas de timeout que dejen de ser estrictamente crecientes,
buffers por proceso o agregados por encima del techo declarado, headers
insuficientes para probar ID de 129 caracteres, límites del host inferiores al
agregado, R fuera de presupuesto o procesos no inventariados. Pool de dependencia, si existiera, requeriría
`conexiones_pool = R × W × (tamaño + overflow)` y límite total explícito;
en esta implementación no existe tal pool y la fórmula es condicional, no
autorización para añadirlo. Inventario y evidencia marcan pools de DB N/A.

## Observabilidad

### Logs y eventos obligatorios

JSON a stdout, un objeto por línea. Campos permitidos: timestamp UTC, nivel,
evento estable, componente, resultado, duración, plantilla de ruta, método,
clase de estado y request ID validado cuando haya contexto; agregados con conteo
y motivo enumerado. No cuerpos, cookies, tokens, contraseñas, secretos, IDs
personales/tenant, URL cruda/query, SQL, configuración completa ni stacks públicos.
Serializar, no concatenar. Fallos de serialización también se contabilizan.
DEBUG solo local explícito sin relajar sanitización; INFO para ciclo de vida y
resúmenes; WARNING para degradación; ERROR para fallos. Sin access logs de
éxito por request en aplicación ni proxy. Request ID solo logs/respuestas.
`web-static` dirige acceso y error a stdout/stderr, aplica el esquema cerrado
cuando emite registros estructurados y propaga únicamente el ID ya validado
por Envoy cuando aplique. Reduce el volumen de éxitos informativos; no registra
cuerpos, secretos ni query strings. Los rechazos de acceso público se registran
y contabilizan en Envoy; no se inventa una ruta de métricas del origen estático.

**Artefacto normativo del esquema de logs.** `NFR-001-007` y `AC-001-017`
exigen que *cada* registro pueda validarse contra «el esquema documentado». Una
lista de campos en prosa no es validable mecánicamente, de modo que este plan
declara un artefacto futuro obligatorio y versionado:

```text
contracts/observability/log-event.schema.json
```

- Implementa como JSON Schema el esquema **cerrado** de los campos permitidos
  enumerados arriba: `additionalProperties: false`, campos obligatorios
  `timestamp` (UTC), `level`, `event` y `component`; `request_id` obligatorio
  cuando el registro pertenece al ciclo de una solicitud y restringido al mismo
  patrón que `ValidRequestId` del contrato HTTP; enumeraciones cerradas para
  `level`, `outcome`, `route` y el motivo de reducción o fallo.
- `TEST-001-017` valida **cada** registro capturado contra este archivo, no
  contra una comprobación ad hoc en el código de prueba. Un registro que no
  valide, o un campo emitido fuera del esquema, hace fallar la prueba.
- El gate G4 verifica además que el archivo exista, sea un JSON Schema válido,
  esté versionado en el repositorio y sea el que la suite usa realmente; su
  ausencia, invalidez o no uso bloquea el gate en lugar de degradarse a una
  comprobación más débil.
- No contiene ni autoriza secretos, credenciales, identificadores personales o
  de tenant, URLs crudas ni valores de configuración: define exclusivamente la
  forma permitida de los registros.
- No se crea en esta etapa. Su creación corresponde a la implementación,
  después de aprobar plan y tareas.

Eventos obligatorios de seguridad de foundation: configuración rechazada,
request ID inválido (sin candidato), acceso operacional denegado, carga
autorizada/denegada, recurso externo bloqueado, fallo del canal de seguridad,
hallazgo bloqueante de secretos/supply chain y aprobación/promoción de artefacto
o baseline. Aplicación, borde y CI registran los eventos que les corresponden;
no se inventa auditoría de dominio. Los registros de revisión/actor operacional
pertenecen a evidencia protegida, no se añaden IDs personales a métricas.

Para errores NO de seguridad: emitir hasta 10 por segundo y tipo estable,
conservar las primeras 3 muestras con request ID válido por ventana de 10 s,
agregar el resto por evento/resultado; máximo 16 tipos catalogados. Éxitos INFO
individuales deshabilitados, su supresión se cuenta. No muestrear seguridad.
Cada evento observado tiene exactamente un destino contable; muestras de
agregados son referencias, no duplican el conteo. Al cerrar cada ventana:
`pendientes_iniciales + observados = emitidos + agregados + suprimidos +
pendientes_finales + fallos_explícitos`.
Emitido significa aceptado por stdout, no almacenado durablemente; el colector
operacional y supervisor deben registrar recepción/errores de transporte.
Conciliar incrementos con el saldo pendiente anterior, no sumar gauges como
si fueran contadores. Muestreo/agrupación jamás oculta el total de errores.

Colas/deadlines según presupuestos. Si stdout o su colector fallan, reintentos
acotados; contadores de fallo y señal independiente al supervisor por estado
de proceso/canal operacional, no solo otro log en la misma cola rota.
Cola obligatoria llena: no continuar acciones de promoción/carga; retirar
readiness, señalizar y terminar controladamente si no se recupera en 10 s.
Health sigue significando vida mientras el proceso exista. Durante una caída
total, el supervisor registra terminación y ventana de entrega incierta; no
declarar entrega garantizada de buffers en memoria ante SIGKILL. Reconciliar
con recepción externa y registrar intervalo de posible pérdida, nunca silencio.
La plataforma debe demostrar captura supervisada de stdout antes de desplegar;
no se resuelve añadiendo una tabla, spool de aplicación o persistencia de dominio.

### Métricas y validez

OpenTelemetry SDK exporta OTLP gRPC al Collector interno. Desactivar exporters
HTTP de lectura, páginas de diagnóstico y telemetría interna HTTP del Collector;
su diagnóstico propio usa stdout/OTLP. No se crea `/metrics` ni otra operación.
En local/CI, receptor de prueba y capturas de lotes permiten verificar métricas;
en despliegue, destino operacional autorizado con TLS/certificados verificados.
Sin proveedor obligatorio ni acceso navegador. Tracing distribuido y exportación
de logs por OTLP no se habilitan en esta foundation; correlación vía request ID.

| Familia semántica propuesta | Tipo, unidad y cálculo | Dimensiones permitidas |
|---|---|---|
| foundation.http.requests | Contador de requests finalizados | route, method, status_class, outcome |
| foundation.http.duration | Histograma segundos desde admisión hasta fin | route, method, status_class |
| foundation.http.errors | Contador de resultados no satisfactorios | route, reason enumerado |
| foundation.http.throughput | Derivado: delta requests / segundos de ventana | route; sin duplicar conteo |
| foundation.process.cpu.time | Contador segundos user+system del proceso | component |
| foundation.process.cpu.utilization | Derivado: delta CPU / (tiempo × cuota CPU), razón | component |
| foundation.process.memory.rss | Gauge bytes resident set; no memoria virtual | component |
| foundation.container.memory | Gauge bytes y límite efectivo | component |
| foundation.admission.active / capacity | Gauges de solicitudes activas/capacidad | lane = public/operational |
| foundation.admission.queued / wait | Gauge elementos / histograma segundos | lane |
| foundation.admission.rejected / timeout | Contadores de saturación/deadline | lane, reason enumerado |
| foundation.edge.rejected | Contador de rechazo antes de backend | reason = rate/size/access/protocol |
| foundation.edge.upstream.timeout | Contador de vencimientos del deadline total de upstream | route; resultado 504 emitido o transporte sin respuesta |
| foundation.telemetry.export | Contador lotes con resultado | component, outcome = ok/retry/fail/drop |
| foundation.telemetry.queue | Gauge lotes/bytes/pendientes | component, kind enumerado |
| foundation.logging.events | Contador observado/emitido/agregado/suprimido/fallido | class = ordinary/security; disposition enumerado |
| foundation.logging.pending / queue | Gauge eventos pendientes/capacidad | class |

No habilitar atributos automáticos sin filtrar. Catálogo HTTP: route solo
`/health`, `/ready`, `unmatched`; method GET/OTHER; status_class 2xx/3xx/4xx/5xx/
transport; outcome ok/not_ready/error/rejected/timeout; reason en inventario
cerrado, máximo 8 valores por familia. El vencimiento del deadline total de
upstream usa `outcome = timeout` y `status_class = 5xx` cuando el borde llega a
emitir el 504; un cierre de conexión sin respuesta usa
`status_class = transport` con `outcome = error` y **nunca** se contabiliza
como 504. Recursos component backend/frontend/edge/collector;
entorno local/ci/nonprod/production. Prohibidos request_id, user_id, tenant_id,
URL cruda, IP, queries, valores arbitrarios e identificadores únicos incluso
en resource attributes o exemplars. Deshabilitar exemplars correlacionados.
Exportar contadores/histogramas con temporalidad delta y agregarlos por ventana
en Collector, sin confundir resets ni sobrescribir emisores. Para gauges,
separar conexiones de origen en memoria operacional acotada a R, conservar
última muestra por origen durante dos intervalos y sumar por componente;
expirar una muestra debe marcar incompletitud, no simular consumo cero.
Esa identidad de transporte no se exporta como atributo o dimensión. La prueba
con dos réplicas exige suma correcta y detecta colisiones; no basta eliminar
etiquetas de métricas acumulativas. Si la configuración de Collector no logra
esa semántica, el gate falla antes de desplegar, sin atribuirle agregación implícita.

Cardinalidad máxima por familia es producto de tamaños de dimensiones permitidas;
histograma con 12 límites finitos más bucket infinito, count y sum: factor 15
en representación por series. Presupuesto inicial 10000 series por despliegue;
validator suma familias, resource attributes permitidos y representaciones antes
de arrancar; rechaza exceso o dimensión nueva. Probar requests con miles de
candidatos de ID/query sin crecimiento de series. Buckets de duración en segundos:
0.001, 0.005, 0.01, 0.025, 0.05, 0.1, 0.25, 0.5, 1, 2, 5, 10.
En el cálculo, entorno es constante por despliegue y component solo toma los
valores realmente emisores de cada familia; no inventar productos cartesianos
de etiquetas que esa familia no utiliza. La representación inicial debe caber
en el presupuesto; cualquier ampliación requiere recalcular antes de exportar.

CPU medido desde contadores reales de proceso y cuota; RSS desde SO/contenedor.
Prueba controlada de CPU y asignación/liberación de memoria demuestra respuesta
del medidor, sin endpoint productivo de carga. Diferenciar cero válido en reposo
de cero constante bajo perturbación. Invalidar faltantes, NaN/infinito, negativos,
unidades erróneas, resets no tratados o constantes pese a variación ejercida.
Memoria no tiene que bajar inmediatamente al liberar; validar aumento por una
asignación suficiente y coherencia con límite. Reportar throughput de medición,
errores de aplicación/transporte y 503 de readiness separadamente.
Consultas, tiempos SQL, persistencia y pools de DB: N/A, no métricas simuladas.

### Retención, acceso y respuesta operacional

Local: logs rotados por plataforma, máximo 7 días o 100 MiB por servicio, lo
primero que ocurra; evidencia en directorio privado, eliminación a los 7 días.
CI: logs y reportes 14 días; snapshots/diffs no promovidos 14 días; informes
de rendimiento 30 días; baseline aprobado versionado conserva historial de
revisión mientras la versión esté soportada. Verificar expiración y borrado.
No cargar evidencia a almacenamiento público; si la plataforma no garantiza
acceso privado, bloquear upload y conservar solo el resultado sanitizado.

Contrato portable productivo inicial: logs ordinarios 14 días, eventos de
seguridad 90 días, métricas 30 días; cifrado en tránsito y reposo, reloj UTC
sincronizado, escritores sin permiso de borrar, lectura operacional/revisores
autorizados y eliminación al expirar. No implica datos de dominio. Operador
dimensiona almacenamiento por tasa real × retención × margen 1.25; alerta al
70% y acción al 85%, nunca rotación que borre eventos obligatorios sin señal.
Revisión de acceso mensual; responsable operativo verifica recepción y purga.
Alertas: export fallido por 30 s, cola al 80% por 10 s, evento obligatorio
fallido inmediato, OOM o CPU sostenida al 90% por 60 s. Sin proveedor elegido;
antes de promoción hay que demostrar este contrato y el canal de alerta.

## Baseline de rendimiento

Procedimiento k6 explícito y denegado por defecto, nunca parte automática de
un PR no confiable. Autorización operacional registra autorizador, revisión,
entorno, destino permitido, parámetros, comienzo/fin y vencimiento (máximo
2 h). Wrapper valida destino por allowlist de host/puerto/red, resolución
inicial y antes de conectar, sin redirects, sin salida a producción ni Internet.
Variables no bastan: red del generador solo alcanza el entorno autorizado.
Prueba negativa desde contexto sin autorización debe fallar antes de enviar carga.

Escenario inicial de esta propuesta: 1 réplica backend, recursos de la tabla,
generador separado, warm-up 30 s excluido, medición 120 s, 5 usuarios virtuales
constantes, think time 250 ms después de cada operación, timeout cliente 3 s,
mezcla determinista 80% health y 20% readiness desde plano operacional de prueba.
Health se mide atravesando el límite público; readiness usa el destino interno
permitido al generador. Ambos destinos están explícitamente autorizados. Cinco
usuarios y ese think time evitan que el escenario nominal demande más de 20
operaciones/s aun con latencia cero; health representa como máximo 16/s, por
debajo de su límite de 20/s por origen. No es una capacidad observada del servicio.
No activos estáticos en esta mezcla; agregarlos exigiría escenario revisado y
separar resultados. No tráfico de negocio, credenciales, IDs personales ni
datos reales. Registrar versión de k6, host de ejecución sanitizado como clase
de entorno, kernel/arquitectura, cuotas efectivas y configuración sin destinos.

Ejecutar dos veces con revisión, recursos, mezcla, semilla y parámetros iguales;
reiniciar estado efímero y dejar 30 s sin carga entre ejecuciones. Sin otros
builds o browsers compitiendo por CPU. Registrar limitaciones del host compartido.
Capturar throughput, p50/p95/p99 end-to-end, requests, errores por clase, CPU y
memoria, concurrencia, espera, colas, rechazos, OTLP y contadores de logs.
Métricas cada 5 s, series presentes durante toda la ventana; reportar gaps.
Validación de sensores con perturbación controlada se ejecuta separada y se
enlaza, no contamina los percentiles del baseline. Pools/persistencia N/A.

Éxito del procedimiento: dos reportes completos, sanitizados y válidos, no un
umbral de RPS. Error inesperado se investiga; datos inválidos impiden conclusiones.
Diferencias entre ejecuciones superiores a 20% en p95 o throughput disparan
investigación de entorno/repetición, no prueban automáticamente una regresión
de capacidad. La prueba de saturación es otro escenario con cota y autorización,
no se eleva carga indiscriminadamente buscando el límite de producción.
Reporte prohíbe request IDs, URLs crudas, usuarios/tenants, bodies y secretos;
contiene commit, clase de entorno, recursos, parámetros, agregados y conclusión
de alcance: **solo baseline operativo; no capacidad de journeys de negocio**.
No afirmar soporte de 100 ni 1000 requests por segundo.

## Contenedores y entorno local

Imágenes OCI multi-stage backend y frontend estático, con edge Envoy separado;
usuario numérico no root,
read-only root filesystem, capacidades eliminadas, no-new-privileges, límites
CPU/memoria/PIDs y tmpfs acotado para temporales. Sin socket del motor ni
montajes amplios del host, herramientas de desarrollo o secretos en capas.
Inter y estáticos locales únicamente en la imagen `web-static`; Storybook,
tests, Node y fuentes no necesarias fuera de las imágenes de entrega. Verificar
archivos/capas y usuario efectivo de backend, Envoy, `web-static` y Collector.

El builder y runtime backend parten de
`docker.io/library/python:3.14.7-slim-trixie` por digest hijo `linux/amd64`.
El builder puede añadir temporalmente compiladores y dependencias nativas;
el runtime copia solo artefactos necesarios y no los conserva. El builder Vite
y el catálogo aislado parten de `docker.io/library/node:24.21.0-trixie-slim`
por digest hijo. Fixtures Python reutilizan la base Python, sin otra base
externa. La fase final `web-static` parte de
`docker.io/library/nginx:<VERSION_ESTABLE_EXACTA>-trixie` por digest hijo,
pendiente de resolución oficial en `TASK-001-001`. Playwright de CI usa
`mcr.microsoft.com/playwright:v1.63.0-noble` por digest hijo; se verifica y
documenta su Node efectivo y se asegura Node 24.21.0 para las herramientas
del proyecto. No se usa Alpine para Python, Node, Playwright o `web-static`.
Collector, k6 y Envoy conservan sus imágenes ya resueltas. No se crea una
imagen OCI por cada CLI o dependencia ni se anticipan acciones de CI.

Compose central: cuatro servicios, backend, edge Envoy, `web-static` y
Collector. Envoy tiene la única entrada pública en loopback local y es el
único terminador TLS externo. Backend, `web-static` y OTLP carecen de puertos
publicados; `web-static` solo está en la red interna accesible por Envoy y no
dispone de DNS público. El listener operacional separado conserva su red y
allowlist; las rutas públicas bloquean `/ready` antes del catch-all SPA.
Preview y staging despliegan los mismos cuatro servicios por digest y la
misma separación de entradas y redes, sin exponer directamente los orígenes.
Catálogo y k6 en perfiles explícitos apagados; el catálogo limita montaje a
código necesario de solo lectura, caché efímera propia y sin credenciales.
Credenciales operacionales TLS se montan solo en procesos que las necesitan,
nunca en frontend. No volumen de datos de aplicación ni servicio de DB/cache.
Reproducibilidad exige imágenes por digest, plataforma explícita y documentación
de diferencias de arquitectura; portabilidad no equivale a igualdad binaria.

## CI y supply chain

Los scripts serán la interfaz normativa; GitHub Actions solo los orquesta.
Permisos por job mínimos; checkout sin credenciales persistentes. Acciones por
SHA completo y revisión de procedencia, permisos y transitorios. Instalaciones
frozen; scripts de postinstall denegados salvo allowlist revisada y sandbox.
No ejecutar PR no confiable con secretos, permiso de escritura, red interna ni
runner persistente. No usar eventos privilegiados para ejecutar su código.

| Gate | Trabajo y orden | Evidencia y condición de bloqueo |
|---|---|---|
| G0 alcance/aprobaciones | Primero: documentos, referencias, inventario, lockfiles/SHA/digests | Contratos vigentes; sin alcance nuevo, placeholders fuera de aprobación ni referencias mutables |
| G1 secretos/supply-chain | Antes de build privilegiado: Gitleaks, revisión de deps/acciones/recursos, Trivy | Centinelas detectados, ninguna vulnerabilidad crítica/alta explotable; revisión de excepciones |
| G2 estático | Ruff/ESLint/Prettier, Pyright/TypeScript, límites de imports | Formato, lint y tipos; sin duplicaciones mecánicas o dependencias cruzadas |
| G3 unit | pytest/Vitest/Testing Library | Reglas puras, configuración, tokens, i18n y componentes |
| G4 integration/API | pytest, contrato, red y observabilidad en entorno efímero | TEST operacionales y seguridad; OpenAPI exacto y métricas válidas |
| G5 build/catálogo | Vite y Storybook, aislamiento, referencias e inventario | Build productiva sin catálogo; historias completas, versión/diseño concordantes |
| G6 accesibilidad | axe-core + revisión manual de teclado/foco/semántica | Cobertura de variantes/estados; fallos no dispensados por snapshots |
| G7 E2E/visual | Playwright y evidencia de matriz real | Contextos fijados, diffs revisados; gates funcional y visual separados |
| G8 contenedores/SBOM | Construcción OCI, Trivy imágenes, Syft SBOM y prueba ejecución | Usuario/permisos/capas, SBOM ligada a commit y digest; diferencias revisadas |
| G9 baseline | Solo ejecución autorizada confiable, después de gates previos | Dos reportes válidos y sanitizados; no gate por capacidad inventada |
| G10 promoción | Confiable, revisión aprobada y todos los gates requeridos verdes | Manifest hashes/commit/run/aprobación; registro/evidencia privados |
| GC completitud | Siempre, al final y con independencia del resultado de los demás; **pertenece al conjunto obligatorio** | Conjunto reportado idéntico al declarado para la etapa en `tooling/ci/required-gates.yaml`; gate fallado, ausente, omitido, saltado o cancelado bloquea. La protección de rama, externa a los jobs, exige `GC` y los demás checks de PR |

### Manifiesto de gates obligatorios y comprobación de completitud

`AC-001-020` y `NFR-001-002` exigen bloquear la integración cuando un control
obligatorio falla **o no se ejecuta**. La segunda condición no se satisface
observando resultados: un gate desactivado, saltado por una condición, omitido
de la matriz o eliminado del workflow no produce ningún resultado que inspeccionar.
`spec.md`, **Casos límite**, lo declara expresamente: «Si un control obligatorio
de CI no puede ejecutarse, el resultado debe considerarse no satisfactorio, no
equivalente a una prueba pasada».

Por eso este plan define un artefacto futuro versionado en el repositorio:

```text
tooling/ci/required-gates.yaml
```

**El conjunto obligatorio es exactamente de doce entradas: `G0`–`G10` y `GC`.**
`GC` **sí pertenece** al conjunto obligatorio; no es un verificador externo a
él. El manifiesto declara, por cada entrada, su identificador estable, su
nombre, su carácter bloqueante, la condición de aplicabilidad cuando la tenga y
la **etapa** en que se exige:

| Etapa | Conjunto exigible | Motivo |
|---|---|---|
| **PR** (`stage: pr`) | `G0`–`G8` y `GC` — **diez** entradas | `G9` y `G10` requieren contexto confiable y autorización operacional; un PR no confiable no puede ejecutarlos |
| **Promoción** (`stage: promotion`) | `G0`–`G10` y `GC` — **doce** entradas | Promoción ocurre desde commit revisado y confiable, con baseline autorizado |

El manifiesto es la única fuente de verdad; el workflow no puede ampliarlo ni
reducirlo implícitamente.

**Comprobación en dos niveles, porque `GC` no puede validarse a sí mismo.**

*Nivel 1 — dentro del workflow (`GC`).* Un job final de completitud, que no
depende del éxito de los demás y se ejecuta siempre, compara el conjunto
declarado para la etapa con los resultados realmente reportados y bloquea si:

1. **un gate obligatorio ejecutado termina en fallo**; o
2. **un gate obligatorio está ausente, omitido, saltado, cancelado,
   deshabilitado o condicionado de manera que no llega a ejecutarse**, y por lo
   tanto no reporta resultado.

Un estado `skipped`, `cancelled` o ausente nunca se contabiliza como
satisfactorio, y ninguna agregación convierte un resultado faltante en
aprobado.

*Nivel 2 — control de integración externo a los jobs.* `GC` vive dentro del
workflow: si alguien suprimiera el workflow entero, o `GC` mismo, no quedaría
nada que ejecutar y la ausencia pasaría inadvertida. Por eso el control que
verifica que **`GC` y todos los demás gates exigibles hayan reportado resultado
satisfactorio** no es un job, sino la **configuración de protección de rama del
repositorio**: su lista de *required status checks* debe ser exactamente igual
al conjunto `stage: pr` del manifiesto, `GC` incluido. La plataforma impide la
integración mientras cualquiera de esos checks no haya reportado éxito —y un
check que nunca se ejecuta nunca reporta éxito, de modo que su omisión bloquea
sin depender de que ningún job lo detecte.

Un verificador dedicado, `scripts/ci/verify-required-checks`, lee esa
configuración por API y **falla si difiere del manifiesto** en cualquier
sentido: check requerido que falta, check requerido de más, o `GC` ausente de
la lista. Se ejecuta en `G0` y, además, desde el contexto confiable antes de
promover, de modo que una relajación de la protección de rama se detecta
aunque el workflow esté intacto.

`TEST-001-020` ejercita los dos modos de fallo por separado, con fixtures de
pipeline que (a) hacen fallar un gate ejecutado y (b) suprimen un gate
obligatorio del workflow, y comprueba que ambos bloquean; y añade (c) la
divergencia entre la protección de rama y el manifiesto, incluida la supresión
de `GC` de los checks requeridos.

G2/G3 pueden paralelizarse tras G0/G1; builds de PR siguen sin privilegios.
G10 jamás consume directamente scripts, HTML activo, addons o resultados no
confiables para ejecutarlos o publicarlos. Rerun de commit revisado en job limpio
sin secretos de publicación; publisher separado valida formato cerrado, hashes,
provenance y aprobación, sin ejecutar contenido, con permiso limitado al destino.
No promover baselines desde fork ni por actualización automática. Artefactos de
PR son no confiables aunque coincida su nombre con una ejecución confiable.

SBOM SPDX o CycloneDX con dependencias transitivas, fuentes, iconos, addons y
procedencia; metadatos enlazan commit completo, pipeline/run y digest de imagen.
Escáner y base de vulnerabilidades identificados por versión/hash/fecha;
base no disponible o desactualizada más de 24 h bloquea promoción, no pasa en
silencio. Hallazgos críticos/altos explotables bloquean release según Constitución;
triage de criticidad en 24 h y corrección antes de promover. Medio: análisis
en 7 días y corrección propuesta en 30 días; bajo en 90 días. No hay riesgos
aceptados por defecto; excepciones solo por proceso constitucional explícito.

## Pruebas

Las siguientes son **pruebas planificadas, no ejecutadas**. No se modifican IDs,
tipos, estados ni evidencia pendiente de `acceptance.md`, **ni el mapeo de
controles de las pruebas negativas de `security.md`**: la columna «Controles
cubiertos» reproduce exactamente el mapeo normativo, incluida
`SEC-TEST-001-029`, incorporada a `security.md` el `2026-09-21`. Un borrador
anterior de este plan había añadido `SEC-001-021` a `SEC-TEST-001-014` y
`SEC-TEST-001-019` para declarar cobertura completa; esa alteración se revirtió
y la brecha se cerró en su fuente normativa.

API se verifica con pytest y cliente HTTP/ASGI de pruebas, sin añadir rutas de
fallo, y además contra el despliegue real cuando el estado contratado solo es
observable atravesando el límite HTTP. Integración incluye componentes reales
salvo dependencias inexistentes simuladas explícitamente. E2E incluye
procedimientos reproducibles de operador cuando el AC es operacional. Cada fila
nombra evidencia adicional al resultado JUnit/JSON sanitizado. SEC es
complementario a los REQ/NFR ya trazados por el AC, que permanecen normativos.

### Matriz TEST-001: backend, operación y base frontend

| Prueba | AC | Nivel | Componente / herramienta | Evidencia esperada | Gate | SEC relacionados |
|---|---|---|---|---|---|---|
| TEST-001-001 | AC-001-001 | INTEGRATION | Backend / pytest | Arranque y dos operaciones únicamente | G4 | SEC-001-001, SEC-001-032 |
| TEST-001-002 | AC-001-002 | E2E | Pantalla / Playwright | Identidad/idioma/estado, sin negocio | G7 | SEC-001-027, SEC-001-032 |
| TEST-001-003 | AC-001-003 | E2E | Pantalla sin backend / Playwright | Indisponibilidad segura y recuperación | G7 | SEC-001-026, SEC-001-027 |
| TEST-001-004 | AC-001-004 | E2E | Documentación / scripts + Playwright | Pasos documentados reproducidos | G7 | SEC-001-001 |
| TEST-001-005 | AC-001-005 | E2E | Checkout limpio / scripts, Compose | Registro de arranque sin conocimiento implícito | G7 | SEC-001-002, SEC-001-025 |
| TEST-001-006 | AC-001-006 | INTEGRATION | Configuración / pytest | Externa válida, arranque listo | G4 | SEC-001-001, SEC-001-002 |
| TEST-001-007 | AC-001-007 | INTEGRATION | Configuración / pytest | Ausencia obligatoria impide readiness | G4 | SEC-001-001 |
| TEST-001-008 | AC-001-008 | INTEGRATION | Configuración / pytest | Formato/rango inseguro rechazados | G4 | SEC-001-001, SEC-001-030 |
| TEST-001-009 | AC-001-009 | API | Health / pytest | 200 mínimo, sin tocar dependencias; además a través de la **entrada pública**, única que sirve esta operación | G4 | SEC-001-004, SEC-001-029 |
| TEST-001-010 | AC-001-010 | INTEGRATION | Readiness / pytest | Listo desde contexto permitido, a través de la **entrada operacional**, única que sirve esta operación; en la entrada pública la misma ruta no existe y su respuesta de ausencia de ruta no es un resultado de readiness | G4 | SEC-001-005, SEC-001-029 |
| TEST-001-011 | AC-001-011 | INTEGRATION | Ciclo de vida / pytest | Canal obligatorio de seguridad sin capacidad de contabilizar: ready 503, health vivo; doble sintético solo como caso adicional; la evidencia nombra la dependencia obligatoria ejercitada | G4 | SEC-001-004, SEC-001-005, SEC-001-010 |
| TEST-001-012 | AC-001-012 | API | Errores / pytest | Fallo esperado con tres campos cerrados | G4 | SEC-001-003, SEC-001-028 |
| TEST-001-013 | AC-001-013 | SECURITY | Error no controlado / pytest | 500 sanitizado con centinela ausente | G4 | SEC-001-003 |
| TEST-001-014 | AC-001-014 | INTEGRATION | Correlación / pytest | ID válido idéntico en respuesta/contexto | G4 | SEC-001-006 |
| TEST-001-015 | AC-001-015 | INTEGRATION | Correlación / pytest | ID generado válido al faltar | G4 | SEC-001-006 |
| TEST-001-016 | AC-001-016 | INTEGRATION | Correlación / pytest | 129/control/inyección reemplazados completos | G4 | SEC-001-006, SEC-001-007 |
| TEST-001-017 | AC-001-017 | INTEGRATION | Logs / pytest + JSON Schema | Cada registro validado contra `contracts/observability/log-event.schema.json`; allowlist, esquema cerrado y correlación | G4 | SEC-001-007, SEC-001-008 |
| TEST-001-018 | AC-001-018 | SECURITY | Backend / pytest, Gitleaks | Centinelas ausentes de respuestas/logs | G1/G4 | SEC-001-002, SEC-001-003, SEC-001-019 |
| TEST-001-019 | AC-001-019 | SECURITY | Diagnóstico UI / Playwright | Consola/red/DOM sin contenido prohibido | G7 | SEC-001-027 |
| TEST-001-020 | AC-001-020 | INTEGRATION | CI / scripts + fixtures de pipeline | Tres fallos independientes: (a) gate obligatorio ejecutado que falla, (b) gate obligatorio suprimido, saltado o condicionado que no se ejecuta y (c) protección de rama divergente del manifiesto, incluida la supresión de `GC` de los checks requeridos; los tres bloquean la integración | G0/G4/GC | SEC-001-020, SEC-001-021, SEC-001-024 |
| TEST-001-021 | AC-001-021 | INTEGRATION | Scripts local/CI / pytest, Vitest | Mismos comandos y resultados reproducibles | G3/G4 | SEC-001-019, SEC-001-031 |
| TEST-001-022 | AC-001-022 | E2E | i18n ES / Playwright | Preferencia es y variantes regionales | G7 | SEC-001-027 |
| TEST-001-023 | AC-001-023 | E2E | i18n EN / Playwright | Preferencia en y variantes regionales | G7 | SEC-001-027 |
| TEST-001-024 | AC-001-024 | E2E | Fallback / Playwright | Idioma no soportado/ausente produce EN | G7 | SEC-001-027 |
| TEST-001-025 | AC-001-025 | E2E | Selector / Playwright | Cambio ES/EN completo sin perder foco | G7 | SEC-001-036 |
| TEST-001-026 | AC-001-026 | ACCESSIBILITY | Pantalla / axe-core, Playwright | Informe automático WCAG 2.2 AA | G6 | SEC-001-035 |
| TEST-001-027 | AC-001-027 | ACCESSIBILITY | Teclado / revisión manual + Playwright | Secuencia, foco visible y nombres | G6 | SEC-001-035 |
| TEST-001-028 | AC-001-028 | E2E | Navegadores reales / Playwright + revisión | Matriz completa con versión/SO/evidencia | G7 | SEC-001-036 |
| TEST-001-029 | AC-001-029 | INTEGRATION | OCI / Compose, scripts | Build y ejecución no root, cierre y límites | G8 | SEC-001-021, SEC-001-025 |
| TEST-001-030 | AC-001-030 | API | Contrato / pytest, comparador OpenAPI, despliegue real | Esquema en memoria más estados realmente observables tras el borde, **por entrada**: health 200/408/413/429/500/502/504 en la entrada pública y ready 200/403/408/413/429/500/502/503/504 en la entrada operacional, sin estado ni cuerpo fuera del contrato. **504 en sus dos causas:** UT, un upstream que no responde dentro del plazo upstream total; SI, otro que agota la ociosidad del flujo antes de comprometer encabezados. **502 en cuatro casos separados, uno por condición:** (a) **UF por rechazo** —el upstream rechaza activamente la conexión—; (b) **UF por timeout** —el upstream no acepta la conexión dentro del plazo—; (c) **UC** —la conexión upstream termina antes de encabezados—; (d) **UR** —reset upstream antes de encabezados—. Cada uno de los cuatro es un caso propio y su evidencia verifica, por separado: estado **502**, `ErrorResponse` **cerrado** con `code: UPSTREAM_UNAVAILABLE`, **coincidencia exacta de `X-Request-ID` con `request_id`**, el **flag** de resultado del borde y el **detalle operacional** de la causa concreta registrado por solicitud y contabilizado en su contador separado. **UF por rechazo y UF por timeout se observaron en el POC; UC y UR siguen siendo comportamiento contratado no observado en el POC y esta prueba es su verificación de implementación pendiente.** 408 enviando lentamente el cuerpo de una solicitud ya encaminada. En todos, ausencia de detalles del upstream. **Preservación del 503:** un `{"status":"not_ready"}` producido por la aplicación atraviesa el borde **sin remapeo**, con cuerpo mínimo y sin `ErrorResponse`, y no se convierte en 502. `GET /ready` contra la entrada pública obtiene ausencia de ruta y no se registra como respuesta de readiness. Un cierre de conexión sin respuesta se registra como transporte, nunca como 504; una respuesta interrumpida tras unos encabezados ya comprometidos conserva el estado emitido con cuerpo incompleto y **no es 502 ni 504** | G4 | SEC-001-028 |
| TEST-001-031 | AC-001-031 | SECURITY | Repositorio/artefactos / Gitleaks, scripts | Detección centinela sin revelar su valor | G1/G8 | SEC-001-002, SEC-001-019 |
| TEST-001-032 | AC-001-032 | SECURITY | Dependencias/OCI / Trivy | Fixture vulnerable bloquea promoción | G1/G8 | SEC-001-021, SEC-001-022 |
| TEST-001-033 | AC-001-033 | INTEGRATION | Dos réplicas / pytest, Compose | Solicitudes sucesivas sin afinidad | G4 | SEC-001-016, SEC-001-032 |
| TEST-001-034 | AC-001-034 | INTEGRATION | OTel / pytest, receptor OTLP | Tráfico/CPU/RSS válidos; persistencia N/A | G4 | SEC-001-011, SEC-001-013, SEC-001-014 |
| TEST-001-035 | AC-001-035 | INTEGRATION | Inventario / pytest | Sin pools de dependencias; N/A justificado | G4 | SEC-001-014, SEC-001-016 |
| TEST-001-036 | AC-001-036 | INTEGRATION | Series / pytest, receptor OTLP | Cardinalidad y dimensiones finitas | G4 | SEC-001-011, SEC-001-012 |
| TEST-001-037 | AC-001-037 | E2E | Baseline / k6 + scripts | Dos reportes válidos iguales parámetros | G9 | SEC-001-014, SEC-001-017, SEC-001-018, SEC-001-031 |
| TEST-001-038 | AC-001-038 | INTEGRATION | Presupuesto / pytest | Cálculo real de las fórmulas al variar réplicas y workers, techo de buffers por proceso (16 MiB) y agregado (P × 16 MiB), y rechazo de toda configuración excedida | G4 | SEC-001-015, SEC-001-016, SEC-001-030 |
| TEST-001-039 | AC-001-039 | INTEGRATION | Admisión y tiempos / pytest, carga acotada | Capacidad, espera, 429 al agotar admisión, 503 por dependencia obligatoria y recuperación; además fases de timeout: inicio del deadline de aplicación en la admisión, plazo total de upstream a los 3 s contados **desde que la solicitud está completamente recibida**, ordenamiento estricto 2 s < 3 s entre aplicación y borde, precedencia encabezados ≤ recepción total con rechazo de la configuración invertida, 408 sobre una solicitud ya encaminada cuyo envío se prolonga, respuesta en una sola escritura sin chunked, y upstream que gotea indefinidamente sin extender el intento más allá del plazo total. La comprobación no afirma ninguna cota desde el primer byte del cliente ni hasta la entrega al navegador | G4 | SEC-001-015, SEC-001-016 |
| TEST-001-040 | AC-001-040 | INTEGRATION | Logs / pytest, carga acotada | Tormenta y conciliación incluyendo seguridad | G4 | SEC-001-009, SEC-001-010 |
| TEST-001-041 | AC-001-041 | E2E | Informe / k6 + revisión estructural | Prohíbe inferir capacidad de negocio | G9 | SEC-001-004, SEC-001-018 |

### Matriz TEST-001: sistema visual

| Prueba | AC | Nivel | Componente / herramienta | Evidencia esperada | Gate | SEC relacionados |
|---|---|---|---|---|---|---|
| TEST-001-042 | AC-001-042 | INTEGRATION | Diseño / validador documental | Existencia, versión, contenido y aprobación previa al plan | G0 | SEC-001-036 |
| TEST-001-043 | AC-001-043 | INTEGRATION | Tokens / Vitest, comparación normativa | Todas las categorías/valores/semántica, sin acoplar contrato | G3/G5 | SEC-001-021, SEC-001-034, SEC-001-036 |
| TEST-001-044 | AC-001-044 | INTEGRATION | packages/ui / Testing Library | Cinco componentes y consumo compartido en pantalla | G3/G5 | SEC-001-034, SEC-001-036 |
| TEST-001-045 | AC-001-045 | INTEGRATION | Historias / Vitest, Storybook | Matriz completa de estados y N/A justificados | G5 | SEC-001-034, SEC-001-036 |
| TEST-001-046 | AC-001-046 | ACCESSIBILITY | Variantes/estados / axe-core + revisión | Teclado, foco, semántica, nombre y contraste por caso, incluido el par del wordmark `text.inverse` sobre `color.navigation` medido sobre el render real contra su umbral aplicable | G6 | SEC-001-034, SEC-001-035, SEC-001-036 |
| TEST-001-047 | AC-001-047 | E2E | Layout / Playwright | Reflow, expansión y orden DOM sin overflow | G7 | SEC-001-034, SEC-001-036 |
| TEST-001-048 | AC-001-048 | E2E | Sistema visual / matriz de TEST-001-028 | Extensión de casos UI, sin duplicar infraestructura | G7 | SEC-001-034, SEC-001-036 |
| TEST-001-049 | AC-001-049 | INTEGRATION | Catálogo / Storybook, scripts | Local/CI, contenido completo, referencias, sin negocio | G5 | SEC-001-019, SEC-001-020, SEC-001-033, SEC-001-034 |
| TEST-001-050 | AC-001-050 | INTEGRATION | Reutilización / reglas ESLint + revisión | Duplicados mecánicos bloqueados; conceptuales revisados; excepciones completas | G2/G5 | SEC-001-036 |
| TEST-001-051 | AC-001-051 | E2E | Visual / Playwright | Mutación detectada, baseline revisado y evidencia ligada a PR | G7/G10 | SEC-001-019, SEC-001-023, SEC-001-035 |
| TEST-001-052 | AC-001-052 | E2E | Gates / Playwright, axe-core, scripts | Captura igual no permite fallo funcional/accesible/seguridad | G6/G7/G10 | SEC-001-035 |

### Matriz de pruebas negativas de seguridad

Todas se ejecutan con datos exclusivamente sintéticos. Los addons/ejemplos
hostiles corren solo en sandbox sin credenciales, red real ni acceso al host.
No se introducen datasets reales para demostrar la detección: se usan marcadores
de las categorías prohibidas y revisión de procedencia de fixtures.

| Prueba | Negativa y resultado esperado | Herramienta / evidencia | Gate | Controles cubiertos |
|---|---|---|---|---|
| SEC-TEST-001-001 | Falta obligatoria: arranque rechazado sin revelar valor | pytest / rechazo sanitizado | G4 | SEC-001-001 |
| SEC-TEST-001-002 | Config maliciosa/insegura, públicos indebidos o límites infinitos: rechazo | pytest / matriz de configuraciones | G4 | SEC-001-001, SEC-001-030 |
| SEC-TEST-001-003 | Centinela en respuestas/logs/métricas/bundle/imagen/OpenAPI/artefactos: gate falla | pytest, Playwright, Gitleaks / inspección | G1/G4/G8 | SEC-001-002, SEC-001-003, SEC-001-008, SEC-001-019, SEC-001-025, SEC-001-028 |
| SEC-TEST-001-004 | ID de 129 caracteres: descarte completo y nuevo ID, no 400 | pytest / respuesta y logs parseados | G4 | SEC-001-006, SEC-001-007 |
| SEC-TEST-001-005 | Control/inyección en ID: no líneas/claves añadidas | pytest / frontera ASGI y HTTP válido | G4 | SEC-001-006, SEC-001-007 |
| SEC-TEST-001-006 | URL cruda o IDs como dimensión: rechazo y sin crecimiento | pytest, receptor OTLP / series | G4 | SEC-001-011, SEC-001-012 |
| SEC-TEST-001-007 | Cliente no autorizado intenta leer/recibir métricas: denegado | Compose, scripts / prueba de red | G4 | SEC-001-013, SEC-001-029 |
| SEC-TEST-001-008 | Derribar la dependencia obligatoria real, el canal de eventos obligatorios de seguridad: ready no listo y sin enumerarla; health sigue indicando vida; doble sintético solo como caso adicional | pytest / respuestas mínimas y dependencia nombrada en la evidencia | G4 | SEC-001-004, SEC-001-005 |
| SEC-TEST-001-009 | Pool sintético de prueba y admisión real saturados: espera/timeout acotados, sin falso éxito | pytest / métricas y presupuesto; ningún pool de DB productivo | G4 | SEC-001-015, SEC-001-016 |
| SEC-TEST-001-010 | Tormenta de logs/errores: reducción cuantificada y recursos acotados | pytest / conciliación y carga | G4 | SEC-001-009, SEC-001-015 |
| SEC-TEST-001-011 | Canal/cola obligatoria falla: reintento o fallo explícito, señal y no listo | pytest / supervisor, contadores y recuperación | G4 | SEC-001-010 |
| SEC-TEST-001-012 | Carga sin autorización o destino no permitido: cero tráfico | scripts, k6 / preflight y aislamiento | G9 | SEC-001-017, SEC-001-029 |
| SEC-TEST-001-013 | Origin/método/header no permitido, wildcard con credenciales o CSP alterada: rechazo/gate fallido | pytest, Playwright / preflight y headers efectivos | G4/G7 | SEC-001-026 |
| SEC-TEST-001-014 | Dependencia, imagen, dependencia UI, addon, fuente, icono o recurso visual con vulnerabilidad crítica o alta explotable: el gate bloquea la entrega sin aceptación implícita | Trivy, inventario / fixture y revisión | G1/G8 | SEC-001-022 |
| SEC-TEST-001-015 | PR no confiable intenta obtener secretos o publicar indirectamente: denegado | fixtures de pipeline / permisos y aislamiento | G1/G10 | SEC-001-020, SEC-001-024 |
| SEC-TEST-001-016 | Contenedor root/privilegiado/capacidades indebidas: bloqueado | inspección OCI y ejecución / reporte | G8 | SEC-001-025 |
| SEC-TEST-001-017 | Identificador/dato prohibido sintético en reporte: no se publica | scripts / sanitización y procedencia | G9 | SEC-001-018, SEC-001-019, SEC-001-031 |
| SEC-TEST-001-018 | CPU/RSS ausentes, constantes o no finitos: evidencia inválida | pytest, receptor OTLP / perturbación | G4/G9 | SEC-001-014 |
| SEC-TEST-001-019 | Alterar o sustituir un artefacto, snapshot, diff o baseline respecto de la revisión y ejecución declaradas: integridad y vínculo fallan y bloquean su uso | scripts / manifest e integridad | G8/G10 | SEC-001-023 |
| SEC-TEST-001-020 | Operación/esquema/ejemplo OpenAPI divergente: falla contrato | pytest / diff semántico | G4 | SEC-001-028 |
| SEC-TEST-001-021 | Diagnóstico UI contiene cuerpo/token/detalle interno: falla | Playwright / consola, DOM y red | G7 | SEC-001-027 |
| SEC-TEST-001-022 | Aparece Identity/tenant/persistencia/negocio: detener trabajo | scripts + revisión estructural / inventario | G0 | SEC-001-032 |
| SEC-TEST-001-023 | Catálogo accesible indebidamente o incluido en producto: bloqueado | Compose, Playwright / acceso y bundle | G5/G8 | SEC-001-029, SEC-001-030, SEC-001-033 |
| SEC-TEST-001-024 | Centinelas en ejemplos/render/snapshot/diff/metadatos: falla | scripts + revisión visual / evidencia sanitizada | G5/G7 | SEC-001-019, SEC-001-033 |
| SEC-TEST-001-025 | Addon hostil intenta leer/escribir/publicar fuera de sandbox: denegado | sandbox sintético / intentos y límites | G5/G10 | SEC-001-020, SEC-001-024, SEC-001-033 |
| SEC-TEST-001-026 | HTML/SVG/recurso externo intenta ejecución o transmisión: bloqueado | Playwright / red y revisión de recursos | G5/G7 | SEC-001-034 |
| SEC-TEST-001-027 | Snapshot igual con fallo de seguridad o baseline sin revisión: no promoción | Playwright, scripts / gates independientes | G7/G10 | SEC-001-023, SEC-001-035 |
| SEC-TEST-001-028 | Divergencia diseño/catálogo/componentes o excepción incompleta: bloqueada | Vitest, scripts + revisión / matriz normativa | G0/G5 | SEC-001-036 |
| SEC-TEST-001-029 | Dependencia, acción de CI, imagen, dependencia UI, addon, fuente, icono o recurso visual no inventariado, con procedencia ausente o no verificable, con referencia flotante donde se exige fijación inmutable, o actualizado sin revisión: el gate bloquea la entrega | scripts, inventario, Syft + revisión de procedencia / fijación verificable | G0/G1/G8 | SEC-001-021 |

Cobertura documental: 52 TEST (`TEST-001-001` a `TEST-001-052`) relacionados uno
a uno con los 52 AC y con el mismo tipo de prueba declarado en `acceptance.md`,
y 29 pruebas negativas (`SEC-TEST-001-001` a `SEC-TEST-001-029`) cuya unión de
controles comprende `SEC-001-001` a `SEC-001-036`. Esa cobertura completa se
obtiene ahora con el mapeo normativo íntegro de `security.md`, sin ninguna
alteración introducida por este plan: `SEC-TEST-001-029` es la prueba negativa
dedicada a `SEC-001-021`, el único control que antes carecía de ella.
La implementación deberá probar este conteo automáticamente y mantener enlaces
a resultados, no confundir presencia de un ID con que una prueba haya pasado.

## Matriz de navegadores y plataformas

Mantener la matriz aprobada: Chrome, Edge, Firefox y Safari desktop, versiones
estable actual y anterior; Safari iOS y Chrome Android actuales. Registrar al
ejecutar versiones exactas, SO, arquitectura, escala y viewport; actualizar
matriz por revisión, no cambiar binarios silenciosamente en un baseline.

| Contexto | Verificación prevista | Cierre de aceptación |
|---|---|---|
| Chrome y Edge desktop | Playwright sobre `mcr.microsoft.com/playwright:v1.63.0-noble` fijada por hijo `linux/amd64`, con binarios de canal oficial actual/anterior; verificar Node de la imagen y usar Node 24.21.0 para herramientas del proyecto | E2E, a11y, foco y snapshots por navegador/SO |
| Firefox desktop | Playwright Firefox para automatización y contraste con Firefox estable real actual/anterior | El browser parcheado no sustituye por sí solo al estable requerido |
| Safari desktop | WebKit Playwright como señal temprana; Safari real actual/anterior en macOS correspondiente | Revisión dirigida reproducible y capturas reales, evidencia obligatoria |
| Safari iOS | Dispositivo/plataforma iOS real con Safari actual | Táctil, teclado aplicable, orientación, reflow y controles nativos |
| Chrome Android | Chrome real actual en Android; automatización donde se soporte | Evidencia del dispositivo/contexto real, no solo emulación desktop |

No equiparar WebKit con Safari ni emulación móvil con browser real. Donde
Playwright no controle el browser requerido, el procedimiento manual dirigido
registra cada caso y captura nativa; Playwright compara los archivos de imagen
contra su baseline autorizado del mismo contexto. Aprobación del revisor enlazada
al gate G7; falta de entorno/evidencia bloquea aceptación, no se marca pasado.
Las verificaciones manuales complementan automatización, no quedan implícitas.

Viewports canónicos: mobile 375×812, tablet 768×1024, desktop 1280×800,
wide 1920×1080. Añadir 320 px y bordes 599/600, 1023/1024, 1439/1440 px.
Breakpoints normativos aprobados, reproducidos íntegros desde `ui-design.md`
1.0.0 sin alterar rangos, gutters ni columnas:

| Nombre | Rango | Gutter | Columnas | Ancho máximo de contenido |
|---|---:|---:|---:|---:|
| `mobile` | 0–599 px | 16 px | 4 | 100% |
| `tablet` | 600–1023 px | 24 px | 8 | 960 px |
| `desktop` | 1024–1439 px | 32 px | 12 | 1200 px |
| `wide` | ≥1440 px | 40 px | 12 | 1600 px |

Los máximos por breakpoint y los máximos de modo de `Container` son contratos
distintos y ambos aplican: `reading` limita a 720 px el bloque de lectura y
`application` limita a 1600 px el contenedor general, mientras el ancho máximo
de contenido del breakpoint acota además la franja y cualquier composición.
El bloque de lectura de la pantalla inicial usa `reading`; la franja usa
`application`. No se autoriza ninguna superficie de datos fluida sin ancho
máximo. Orden DOM/foco preservado, sin scroll horizontal accidental ni
superposición; zoom y expansión sin perder instrucciones.
TEST-001-048 extiende TEST-001-028, no duplica la matriz.

## Evidencia y trazabilidad

Cadena: REQ/NFR → AC → TEST → ejecución → artefacto/hash → revisión/aprobación.
Seguridad añade THREAT → SEC → SEC-TEST. Los enlaces normativos de
`security.md` se conservan sin alteración: las 29 filas `SEC-TEST-001-001` a
`SEC-TEST-001-029` de este plan reproducen exactamente el mapeo de controles de
`security.md`, y una comprobación mecánica debe verificar esa identidad y
fallar si este documento añade o retira un control de cualquiera de ellas.
Ampliar la cobertura requiere modificar y reaprobar `security.md`, nunca
anotarla distinto aquí. Sistema visual enlaza además versión de diseño, token,
historia, componente, estado y contexto de baseline. No alterar aprobaciones
históricas.
REQ-001-001 a REQ-001-021 y NFR previos se verifican por AC-001-001 a AC-001-041;
REQ-001-022 a REQ-001-028 y NFR-001-018 a NFR-001-021 por AC-001-042 a AC-001-052.
En particular: REQ-001-022→AC-001-043; REQ-001-023→AC-001-044;
REQ-001-024→AC-001-045; REQ-001-025→AC-001-049;
REQ-001-026→AC-001-050; REQ-001-027→AC-001-051/052;
REQ-001-028→AC-001-042; NFR-001-018→AC-001-046/052;
NFR-001-019→AC-001-047/048; NFR-001-020→AC-001-049;
NFR-001-021→AC-001-051/052. Aplican además las relaciones del AC normativo.

Manifest cerrado por ejecución: commit, plan/diseño/contrato y hashes, versiones
exactas de herramientas, escenario, recursos, resultados por ID, archivos
sanitizados y hashes, revisión y autorización cuando corresponda. Sin secretos,
identificadores personales ni URLs internas. Aprobaciones operacionales privadas
pueden enlazarse por referencia opaca del proceso de revisión, nunca dimensión.
Un test faltante, evidencia ilegible, manual pendiente o fixture no ejecutado
se reporta como pendiente/fallido, no se convierte en aprobado por agregación.
La evidencia de pruebas aquí descrita no existe todavía; será producida tras
aprobación de plan y tasks, sin actualizar anticipadamente `acceptance.md`.

## ASVS 5.0

Fuente única normativa: publicación oficial **OWASP ASVS 5.0.0**, tag
`v5.0.0`, consultada 2026-09-21. [Publicación oficial](https://github.com/OWASP/ASVS/tree/v5.0.0/5.0/en).
Se verificó el texto oficial de cada ID siguiente; las descripciones son síntesis,
no citas literales ni IDs extrapolados desde otra versión.

**Nivel base: decisión específica autorizada por el Product Owner.**
**OWASP ASVS 5.0.0 nivel L1** es el baseline de SPEC-001, complementado con
requisitos concretos L2 y L3 seleccionados donde el riesgo real de esta
foundation lo exige: exposición pública de una operación, logging y su canal
obligatorio, cadena de suministro y CI, y defensas frente a denegación de
servicio. La Constitución fija «ASVS 5.0 baseline» sin fijar nivel, y
`security.md` asigna expresamente al plan seleccionar y revisar el nivel
aplicable. El Product Owner **autorizó expresamente esta selección de nivel**
como decisión específica; por eso deja de figurar como pregunta abierta. No hay
datos de negocio, credenciales de producto ni autenticación que justifiquen
declarar toda la aplicación L2 en esta etapa. Una spec de Identity, tenancy o
datos sensibles deberá revisar nivel, alcance y requisitos antes de
implementarse.

Esa autorización, por sí sola, **no aprobó este documento**: alcanzó a la
decisión de nivel, no al contenido del plan. La aprobación independiente de
`plan.md` 1.0.1 se registró el `2026-10-01`. Las aprobaciones de `security.md`
y del contrato están vigentes. **No se afirma
certificación ni cumplimiento integral de ASVS**; demostrarlo exige
implementación y evidencia.
Fuente del criterio de niveles: [Qué es ASVS](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x03-What-is-the-ASVS.md).

### Disposición de aplicabilidad por capítulo

Los diecisiete capítulos de ASVS 5.0.0 reciben una disposición explícita. Los
títulos proceden de la publicación oficial en el tag `v5.0.0`.

| Capítulo | Disposición | Razón concreta |
|---|---|---|
| V1 Encoding and Sanitization | Parcialmente aplicable | Aplica a codificación contextual de la salida JSON, a la construcción de logs estructurados y a SVG e iconos empaquetados. No hay plantillas HTML con contenido hostil, XML, deserialización ni código nativo. |
| V2 Validation and Business Logic | Parcialmente aplicable | Aplica a la validación documentada de configuración y del candidato de request ID en el servidor, y a la coherencia entre valores relacionados de presupuestos. No existe lógica ni flujo de negocio que validar. |
| V3 Web Frontend Security | Aplicable | Existe una pantalla pública servida por el límite HTTP, con CSP, headers de seguridad, CORS y una matriz de navegadores soportados. |
| V4 API and Web Service | Parcialmente aplicable | Aplica a las dos operaciones HTTP contratadas, su Content-Type, sus métodos permitidos, el forwarding de proxy confiable y la delimitación de mensajes. No hay GraphQL, WebSocket ni RPC. |
| V5 File Handling | Parcialmente aplicable | No hay uploads: la foundation no recibe ni almacena archivos de un usuario, de modo que 5.2.1 y 5.2.2 no aplican. **Sí** aplica la parte de servicio y resolución de rutas: `web-static` sirve los archivos del directorio estático detrás de Envoy (5.3.1), con defensa tanto en el borde como en NGINX frente a path traversal y destinos construidos con datos no confiables (5.3.2). Excluir el capítulo entero por la ausencia de uploads habría dejado sin evaluar esos dos requisitos L1. |
| V6 Authentication | No aplicable | No existe Identity: ni login, ni credenciales de producto, ni registro, ni recuperación, ni MFA. Una spec de Identity deberá aplicarlo con su propio threat model. |
| V7 Session Management | No aplicable | No hay sesiones, cookies de sesión ni estado de usuario; el procesamiento es stateless y el frontend usa `credentials: omit`. |
| V8 Authorization | Parcialmente aplicable | No existe autorización de producto `resource.action` ni acceso por objeto, de modo que 8.2.2 (IDOR/BOLA) no aplica. **Sí** aplica la autorización **operacional**: existe documentación de reglas de acceso por superficie (8.1.1, tabla **Autorización** de `security.md`), restricción de acceso a nivel de función sobre `/ready`, OTLP, catálogo y carga (8.2.1) y aplicación de esas reglas en una capa confiable —borde o plataforma— que el consumidor no puede manipular (8.3.1). Clasificar el capítulo como no aplicable habría dejado sin evaluar tres requisitos L1 que esta foundation sí ejerce. |
| V9 Self-contained Tokens | No aplicable | No se emiten, validan ni consumen tokens autocontenidos. El request ID es un identificador de correlación sin valor de autorización. |
| V10 OAuth and OIDC | No aplicable | No hay OAuth, OIDC, proveedor de identidad ni delegación de autorización. |
| V11 Cryptography | Parcialmente aplicable | Aplica a la generación de identificadores de correlación no adivinables y al inventario de certificados y material TLS operacional. La foundation no implementa algoritmos propios, cifrado de datos de dominio, hashing de credenciales ni un ciclo de vida de claves de aplicación. |
| V12 Secure Communication | Aplicable | Hay TLS externo para la pantalla y la API, y cifrado del tráfico proxy-backend y operacional fuera del host. |
| V13 Configuration | Aplicable | Toda la configuración es externa y validada, con secretos operacionales, allowlists de destinos, inventario de comunicaciones y superficies que no deben exponerse. |
| V14 Data Protection | Parcialmente aplicable | No hay datos de negocio ni personales que clasificar. Aplica a la clasificación y protección de configuración, logs, evidencia operacional y al almacenamiento del navegador, que no guarda nada sensible. |
| V15 Secure Coding and Architecture | Aplicable | Hay dependencias de terceros, SBOM, plazos de remediación, funcionalidad costosa con defensas de disponibilidad y separación de lo que llega al artefacto productivo. |
| V16 Security Logging and Error Handling | Aplicable | Hay logging estructurado con inventario, eventos obligatorios de seguridad, protección del canal, correlación y manejo seguro de errores. |
| V17 WebRTC | No aplicable | No hay WebRTC, medios en tiempo real, señalización ni conexiones peer-to-peer en ninguna superficie de SPEC-001. |

Las disposiciones «parcialmente aplicable» acotan el alcance, no lo relajan:
dentro de la parte aplicable rigen íntegramente los requisitos del nivel
seleccionado.

### Cobertura individual de **todos** los requisitos L1 de V1 a V17

ASVS 5.0.0 contiene **345 requisitos**, de los cuales **70 son de nivel L1**.
Esta sección los recorre **todos**, uno por uno, incluidos los de los capítulos
dispuestos como no aplicables, porque una disposición de capítulo no sustituye
la evaluación de cada requisito. Conteo verificado contra el texto oficial del
tag `v5.0.0`: V1 8, V2 4, V3 8, V4 2, V5 4, V6 13, V7 6, V8 4, V9 4, V10 5,
V11 3, V12 3, V13 1, V14 2, V15 3, **V16 0 y V17 0**.

> **V16 no contiene ningún requisito L1 en ASVS 5.0.0.** Es un capítulo
> *aplicable* cuyos requisitos son todos L2 o L3. Los controles de logging de
> esta foundation se seleccionan por tanto deliberadamente del nivel L2, no por
> obligación del baseline L1. Lo mismo ocurre con V17, que además no aplica.

Resultado de la evaluación: **23 aplican**, **3 aplican parcialmente** y
**44 no aplican**, con razón concreta por requisito.

#### V1 Encoding and Sanitization — 8 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 1.2.1 | L1 | Aplica | Se emiten respuestas JSON y campos de cabecera HTTP (`X-Request-ID`) cuya estructura no puede alterarse por contenido no confiable | `SEC-001-003`, `SEC-001-006`, `SEC-001-007` | `TEST-001-012`, `SEC-TEST-001-005` |
| 1.2.2 | L1 | Parcial | No se construyen URLs con datos no confiables: la consulta de health usa una ruta fija same-origin. Sí aplica la mitad de protocolos seguros, impuesta por CSP (`base-uri 'none'`, sin `javascript:` ni `data:`) | `SEC-001-026`, `SEC-001-034` | `SEC-TEST-001-013`, `SEC-TEST-001-026` |
| 1.2.3 | L1 | Aplica | Los cuerpos JSON y los registros de log se serializan, nunca se concatenan | `SEC-001-007` | `TEST-001-017`, `SEC-TEST-001-005` |
| 1.2.4 | L1 | No aplica | No existe base de datos ni lenguaje de consulta: `data-model.md` 1.0.0 declara la no aplicabilidad | `SEC-001-032` | `SEC-TEST-001-022` |
| 1.2.5 | L1 | No aplica | La aplicación no invoca el sistema operativo ni expande shell; la configuración no se evalúa como código | `SEC-001-001` | `SEC-TEST-001-002` |
| 1.3.1 | L1 | No aplica | No hay editor WYSIWYG ni entrada HTML no confiable: la pantalla usa texto React | `SEC-001-034` | `SEC-TEST-001-026` |
| 1.3.2 | L1 | Aplica | Se prohíbe `eval` y la ejecución dinámica; CSP sin `unsafe-eval` ni `unsafe-inline` | `SEC-001-034` | `SEC-TEST-001-026` |
| 1.5.1 | L1 | No aplica | No se procesa XML en ninguna superficie: la API es exclusivamente JSON | `SEC-001-028` | `TEST-001-030` |

#### V2 Validation and Business Logic — 4 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 2.1.1 | L1 | Aplica | El contrato documenta `IncomingRequestId` y `ValidRequestId`, y el inventario documenta tipo, rango y restricción de cada campo de configuración | `SEC-001-001`, `SEC-001-006` | `TEST-001-008`, `SEC-TEST-001-002` |
| 2.2.1 | L1 | Aplica | Validación positiva: patrón y longitud del candidato de request ID, enumeraciones y rangos de configuración, relaciones de presupuestos | `SEC-001-001`, `SEC-001-006`, `SEC-001-016` | `TEST-001-038`, `SEC-TEST-001-004` |
| 2.2.2 | L1 | Aplica | Toda validación decisiva ocurre en el backend y en el borde; el frontend nunca es control de seguridad | `SEC-001-001`, `SEC-001-029` | `TEST-001-016`, `SEC-TEST-001-002` |
| 2.3.1 | L1 | No aplica | No existe flujo de negocio con pasos secuenciales: las dos operaciones son lecturas idempotentes e independientes | `SEC-001-032` | `SEC-TEST-001-022` |

#### V3 Web Frontend Security — 8 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 3.2.1 | L1 | Aplica | Las respuestas de API se sirven con `Content-Type` JSON, `nosniff`, `frame-ancestors 'none'` y `X-Frame-Options: DENY`, de modo que no se rendericen en un contexto incorrecto | `SEC-001-026` | `SEC-TEST-001-013` |
| 3.2.2 | L1 | Aplica | El contenido textual se renderiza con texto React, sin HTML crudo ni `dangerouslySetInnerHTML` | `SEC-001-034` | `SEC-TEST-001-026` |
| 3.3.1 | L1 | No aplica | La foundation no emite ninguna cookie: no hay sesión y el frontend usa `credentials: omit` | `SEC-001-032` | `SEC-TEST-001-022` |
| 3.4.1 | L1 | Aplica | HSTS con `max-age=31536000` en el límite TLS productivo, que satisface el año exigido por L1 | `SEC-001-026` | `SEC-TEST-001-013` |
| 3.4.2 | L1 | Aplica | ACAO es valor fijo del límite con allowlist vacía por defecto; nunca se refleja un `Origin` arbitrario | `SEC-001-026` | `SEC-TEST-001-013` |
| 3.5.1 | L1 | No aplica | No hay funcionalidad sensible que cambie estado ni autoridad ambiental —sin cookies, sin credenciales—, de modo que no existe superficie CSRF que defender | `SEC-001-026`, `SEC-001-032` | `SEC-TEST-001-013` |
| 3.5.2 | L1 | No aplica | Misma razón: no se depende del preflight para proteger funcionalidad sensible, porque no existe tal funcionalidad | `SEC-001-026` | `SEC-TEST-001-013` |
| 3.5.3 | L1 | No aplica | Las dos operaciones son GET de solo lectura sin autoridad ambiental; el acceso a `/ready` se restringe en el límite de red, no por método ni por cookie | `SEC-001-029` | `SEC-TEST-001-007` |

#### V4 API and Web Service — 2 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 4.1.1 | L1 | Aplica | Toda respuesta con cuerpo declara `application/json` con charset correcto | `SEC-001-028` | `TEST-001-030` |
| 4.4.1 | L1 | No aplica | No existen WebSockets en ninguna superficie de SPEC-001 | `SEC-001-028`, `SEC-001-032` | `TEST-001-030` |

#### V5 File Handling — 4 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 5.2.1 | L1 | No aplica | No se aceptan archivos. El presupuesto equivalente de tamaño de solicitud existe y se verifica, pero pertenece a las defensas de disponibilidad de V15, no a este requisito | `SEC-001-015` | `SEC-TEST-001-009` |
| 5.2.2 | L1 | No aplica | No se acepta ningún archivo cuyo tipo o extensión hubiera que validar; las fuentes e iconos se incorporan en build desde origen revisado e inventariado | `SEC-001-021`, `SEC-001-034` | `SEC-TEST-001-029` |
| 5.3.1 | L1 | Parcial | No hay archivos subidos, pero `web-static` **sí sirve el directorio estático** detrás de Envoy: debe demostrarse que ninguno se ejecuta como código de servidor —sin CGI, FastCGI ni SSI— y que autoindex está deshabilitado | `SEC-001-025`, `SEC-001-033` | `SEC-TEST-001-023` |
| 5.3.2 | L1 | **Aplica** | Envoy enruta y `web-static` resuelve solicitudes a archivos del directorio estático: path traversal, segmentos codificados y normalizaciones ambiguas se rechazan en ambas capas. Además, los destinos de OTLP y de carga se construyen desde allowlists validadas antes de conectar, nunca desde datos de la solicitud, que es la parte SSRF del requisito | `SEC-001-017`, `SEC-001-029`, `SEC-001-034` | `SEC-TEST-001-012`, `SEC-TEST-001-023`, `TEST-001-030` |

#### V6 Authentication — 13 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 6.1.1, 6.2.1, 6.2.2, 6.2.3, 6.2.4, 6.2.5, 6.2.6, 6.2.7, 6.2.8, 6.3.1, 6.4.1, 6.4.2 | L1 | No aplica | Los doce regulan el ciclo de vida de credenciales de usuario —política, cambio, composición, enmascarado, secretos iniciales y preguntas de recuperación—. SPEC-001 no tiene Identity, login, registro ni credenciales de producto, de modo que no existe el objeto que regulan. Una spec de Identity deberá aplicarlos con su propio threat model | `SEC-001-032` | `SEC-TEST-001-022` |
| 6.3.2 | L1 | No aplica | No existen cuentas de aplicación por defecto que deshabilitar, porque no existen cuentas. La exigencia análoga sobre el usuario del contenedor —numérico y no root— se cubre por separado | `SEC-001-025`, `SEC-001-032` | `SEC-TEST-001-016` |

#### V7 Session Management — 6 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 7.2.1, 7.2.2, 7.2.4, 7.4.1, 7.4.2 | L1 | No aplica | No hay sesiones, tokens de sesión, autenticación ni terminación de sesión: el procesamiento es stateless y no se conserva estado de usuario entre solicitudes | `SEC-001-032` | `SEC-TEST-001-022` |
| 7.2.3 | L1 | No aplica | No existen tokens de referencia de sesión. La construcción que exige —CSPRNG y 128 bits— se adopta igualmente para el request ID, pero bajo 11.5.1, porque el request ID es un identificador de correlación sin valor de autorización | `SEC-001-006` | `TEST-001-015` |

#### V8 Authorization — 4 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 8.1.1 | L1 | **Aplica** | Sí existe documentación de reglas de autorización, aunque sea operacional y no de producto: la tabla **Autorización** de `security.md` y la matriz de acceso operacional de este plan definen recurso, acceso autorizado, scope y comportamiento por defecto para pantalla, health, readiness, métricas, catálogo, carga y evidencia | `SEC-001-029`, tabla **Autorización** de `security.md` | `TEST-001-010`, `SEC-TEST-001-007` |
| 8.2.1 | L1 | **Aplica** | El acceso a nivel de función está restringido a consumidores con permiso explícito: `/ready`, OTLP, catálogo y carga son superficies deny by default alcanzables solo desde el contexto operacional autorizado | `SEC-001-013`, `SEC-001-029`, `SEC-001-033` | `SEC-TEST-001-007`, `SEC-TEST-001-012`, `SEC-TEST-001-023` |
| 8.2.2 | L1 | No aplica | No hay objetos de datos ni identificadores de recurso: no existe superficie para IDOR ni BOLA | `SEC-001-032` | `SEC-TEST-001-022` |
| 8.3.1 | L1 | **Aplica** | La decisión se aplica en una capa confiable —borde o plataforma—, nunca en el navegador; el frontend jamás recibe ruta ni credencial de readiness y la allowlist se evalúa sobre el peer validado, no sobre cabeceras que el cliente controla | `SEC-001-029` | Batería de conformidad de `/ready`, `SEC-TEST-001-007` |

#### V9 Self-contained Tokens — 4 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 9.1.1, 9.1.2, 9.1.3, 9.2.1 | L1 | No aplica | No se emiten, validan ni consumen tokens autocontenidos, firmas, algoritmos ni claims de validez. El request ID no es un token: no transporta autorización y el candidato recibido **nunca se acepta como contenido de confianza**, se reemplaza | `SEC-001-006` | `SEC-TEST-001-004` |

#### V10 OAuth and OIDC — 5 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 10.4.1, 10.4.2, 10.4.3, 10.4.4, 10.4.5 | L1 | No aplica | No hay servidor de autorización, cliente OAuth, redirect URI, authorization code ni refresh token en ninguna superficie de SPEC-001 | `SEC-001-032` | `SEC-TEST-001-022` |

#### V11 Cryptography — 3 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 11.3.1 | L1 | No aplica | La aplicación no cifra datos: no elige modo de bloque ni esquema de padding. La negociación TLS se rige por V12 y su perfil | `SEC-001-013` | `SEC-TEST-001-007` |
| 11.3.2 | L1 | No aplica | Misma razón: no hay cifrado de aplicación cuyos algoritmos aprobar; las suites TLS se fijan en el perfil de V12 | `SEC-001-013` | `SEC-TEST-001-007` |
| 11.4.1 | L1 | Parcial | No hay hashing criptográfico de aplicación ni credenciales que derivar. Sí aplica a las funciones hash usadas para **integridad de artefactos**: hashes de baseline, diff, SBOM y digests OCI deben usar funciones aprobadas y no MD5 | `SEC-001-021`, `SEC-001-023` | `SEC-TEST-001-019`, `SEC-TEST-001-029` |

#### V12 Secure Communication — 3 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 12.1.1 | L1 | Aplica | TLS 1.2 mínimo y 1.3 preferido en el límite productivo | `SEC-001-013`, `SEC-001-030` | `SEC-TEST-001-002` |
| 12.2.1 | L1 | Aplica | Todo el tráfico cliente–servicio usa TLS, sin fallback inseguro ni redirección transparente de API | `SEC-001-013`, `SEC-001-030` | `SEC-TEST-001-002`, `SEC-TEST-001-007` |
| 12.2.2 | L1 | Aplica | Certificados públicamente confiables y validados para la exposición externa | `SEC-001-013` | `SEC-TEST-001-007` |

#### V13 Configuration — 1 requisito L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 13.4.1 | L1 | Aplica | La imagen de entrega no incluye metadatos de control de versiones ni el directorio `.git`, y el límite no los sirve | `SEC-001-025`, `SEC-001-033` | `SEC-TEST-001-023`, `TEST-001-029` |

#### V14 Data Protection — 2 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 14.2.1 | L1 | Aplica | No se envían datos sensibles en URL ni query: las dos operaciones no aceptan parámetros y los logs prohíben la URL cruda | `SEC-001-002`, `SEC-001-008` | `SEC-TEST-001-003`, `SEC-TEST-001-006` |
| 14.3.1 | L1 | No aplica | No hay datos autenticados ni sesión que limpiar del cliente. La decisión de **no persistir el idioma** refuerza que el navegador no conserva estado alguno entre recargas | `SEC-001-027`, decisión de no persistencia de idioma | `SEC-TEST-001-021` |

#### V15 Secure Coding and Architecture — 3 requisitos L1

| ID | Nivel | Aplicabilidad | Razón concreta | Control o decisión de SPEC-001 | Verificación prevista |
|---|---|---|---|---|---|
| 15.1.1 | L1 | Aplica | Los plazos de remediación por criticidad están documentados: crítico/alto antes de promover con triage en 24 h, medio 7/30 días, bajo 90 días | `SEC-001-021`, `SEC-001-022` | `SEC-TEST-001-014` |
| 15.2.1 | L1 | Aplica | El gate bloquea la entrega cuando un componente incumple esos plazos, sin aceptación implícita | `SEC-001-022` | `SEC-TEST-001-014`, `SEC-TEST-001-029` |
| 15.3.1 | L1 | Aplica | Las respuestas devuelven el subconjunto mínimo: health solo `status`, readiness solo `status`, errores solo `code`, `message` y `request_id`, todos con `additionalProperties: false` | `SEC-001-003`, `SEC-001-004`, `SEC-001-005` | `TEST-001-009`, `TEST-001-010`, `TEST-001-030` |

#### V16 y V17 — 0 requisitos L1

No existe ningún requisito L1 que evaluar en estos dos capítulos. V16 es
aplicable y sus controles seleccionados son L2, recogidos en el mapeo de la
sección siguiente. V17 no aplica y tampoco aporta requisitos L1.

**Conclusión de la cobertura L1.** Los 70 requisitos L1 de ASVS 5.0.0 quedan
evaluados individualmente, con razón concreta y, cuando aplican, control y
verificación previstos. Esta cobertura **no afirma conformidad ni
certificación**: demostrarla exige implementación y evidencia ejecutada, que
todavía no existen.

| IDs ASVS 5.0.0 y nivel | Aplicabilidad concreta / decisión | SEC y pruebas previstas |
|---|---|---|
| 1.2.1, 1.2.3 (L1) | Codificación contextual de salida y JSON seguro, no interpolación de contenido hostil | SEC-001-007, SEC-001-034; SEC-TEST-001-005, SEC-TEST-001-026 |
| 1.3.2 (L1), 1.3.4 (L2) | Sin eval ni ejecución dinámica; SVG revisado sin contenido activo | SEC-001-034; SEC-TEST-001-026 |
| 1.3.6 (L2) | Destinos de carga/OTLP validados y salida allowlisted, sin redirects | SEC-001-017, SEC-001-034; SEC-TEST-001-012, SEC-TEST-001-026 |
| 2.1.1, 2.2.1, 2.2.2 (L1), 2.2.3 (L2) | Contratos de entrada documentados, servidor valida candidato de ID/config y relaciones de presupuestos | SEC-001-001, SEC-001-006, SEC-001-016; SEC-TEST-001-002, SEC-TEST-001-004, TEST-001-038 |
| 3.2.2 (L1) | DOM con métodos/texto seguros, no HTML inyectado | SEC-001-034; SEC-TEST-001-026 |
| 3.4.1, 3.4.2 (L1) | HSTS de un año en TLS productivo y allowlist CORS | SEC-001-026; SEC-TEST-001-013 |
| 3.4.3, 3.4.4, 3.4.5, 3.4.6 (L2) | CSP, nosniff, referrer y frame-ancestors efectivos | SEC-001-026, SEC-001-034; SEC-TEST-001-013, SEC-TEST-001-026 |
| 3.7.1 (L2) | Tecnologías frontend soportadas y matriz de browsers verificada | SEC-001-021, SEC-001-036; TEST-001-028, TEST-001-048 |
| 4.1.1 (L1), 4.1.3 (L2), 4.1.4 (L3) | Content-Type correcto, forwarding solo de proxy confiable, métodos allowlisted | SEC-001-026, SEC-001-028, SEC-001-029; SEC-TEST-001-013, TEST-001-030 |
| 4.2.1 (L2) | Framing coherente entre borde/ASGI, duplicados ambiguos rechazados | SEC-001-015, SEC-001-026; SEC-TEST-001-009, SEC-TEST-001-013 |
| 11.1.2 (L2) | Inventario de certificados y material TLS operacional en uso, sin claves de aplicación ni algoritmos propios | SEC-001-002, SEC-001-021; SEC-TEST-001-003, SEC-TEST-001-029 |
| 11.5.1 (L2) | Request ID generado con CSPRNG: 32 caracteres hexadecimales aleatorios, 128 bits de entropía, nunca derivado del valor recibido | SEC-001-006; TEST-001-015, SEC-TEST-001-004 |
| 12.1.1, 12.2.1, 12.2.2 (L1), 12.3.2 (L2) | TLS externo y validación de certificados, sin fallback inseguro | SEC-001-001, SEC-001-013, SEC-001-030; SEC-TEST-001-002, SEC-TEST-001-007 |
| 13.1.1 (L2), 13.1.2, 13.1.3 (L3) | Inventario de comunicaciones, concurrencia, timeouts y liberación documentados | SEC-001-015, SEC-001-016; TEST-001-038, TEST-001-039 |
| 13.2.4, 13.2.5 (L2), 13.2.6 (L3) | Salida denegada por defecto y respeto de límites de conexiones | SEC-001-016, SEC-001-034; SEC-TEST-001-009, SEC-TEST-001-026 |
| 13.3.2 (L2) | Solo componentes/procesos autorizados acceden a secretos operacionales | SEC-001-002, SEC-001-020, SEC-001-024; SEC-TEST-001-003, SEC-TEST-001-015 |
| 13.4.1 (L1), 13.4.2, 13.4.3, 13.4.4, 13.4.5 (L2), 13.4.6 (L3) | Sin archivos del repositorio, debug, listados, TRACE, documentación expuesta ni versiones detalladas | SEC-001-004, SEC-001-025, SEC-001-028, SEC-001-033; TEST-001-009, SEC-TEST-001-023 |
| 14.1.1, 14.1.2 (L2) | Clasificar configuración, logs y evidencia; protección/retención operacional, no datos de dominio | SEC-001-002, SEC-001-008, SEC-001-018, SEC-001-019; SEC-TEST-001-003, SEC-TEST-001-017, SEC-TEST-001-024 |
| 14.2.1 (L1), 14.2.3 (L2), 14.3.3 (L2) | Sin secretos en URL, tracking externo o almacenamiento inseguro del navegador | SEC-001-002, SEC-001-027, SEC-001-034; SEC-TEST-001-021, SEC-TEST-001-026 |
| 15.1.1, 15.2.1 (L1), 15.1.2 (L2) | Plazos de remediación, dependencias confiables e inventario/SBOM | SEC-001-021, SEC-001-022; SEC-TEST-001-014 |
| 15.1.3, 15.2.2 (L2) | Recursos costosos inventariados y defensas DoS verificadas | SEC-001-015, SEC-001-016; SEC-TEST-001-009, TEST-001-038 |
| 15.2.3 (L2), 15.2.4, 15.2.5 (L3) | Sin funciones de prueba en producto; procedencia transitiva y aislamiento de ejecución riesgosa | SEC-001-021, SEC-001-024, SEC-001-033; SEC-TEST-001-014, SEC-TEST-001-023, SEC-TEST-001-025 |
| 15.3.1 (L1), 15.3.4, 15.3.5 (L2) | Salida mínima, forwarding confiable y tipos estrictos | SEC-001-003, SEC-001-004, SEC-001-005, SEC-001-001; TEST-001-009, TEST-001-010, SEC-TEST-001-002 |
| 16.1.1, 16.2.1, 16.2.2, 16.2.3, 16.2.4, 16.2.5 (L2) | Inventario, contexto suficiente sin identidades de producto, UTC, destinos, correlación y redacción | SEC-001-007, SEC-001-008, SEC-001-009, SEC-001-010; TEST-001-017, TEST-001-040 |
| 16.3.2, 16.3.3, 16.3.4 (L2) | Eventos de acceso operacional y seguridad; excepciones/fallos del canal | SEC-001-010, SEC-001-029; SEC-TEST-001-011, SEC-TEST-001-012 |
| 16.4.1, 16.4.2, 16.4.3 (L2) | Logs resistentes a inyección, protegidos y enviados al plano separado de análisis | SEC-001-007, SEC-001-010, SEC-001-024; SEC-TEST-001-005, SEC-TEST-001-011, SEC-TEST-001-015 |
| 16.5.1, 16.5.2, 16.5.3 (L2), 16.5.4 (L3) | Errores seguros, dependencia fallida, fallo cerrado y handler de último recurso | SEC-001-003, SEC-001-005, SEC-001-015; TEST-001-013, SEC-TEST-001-008, SEC-TEST-001-009 |

Textos oficiales consultados por capítulo:
[V1](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x10-V1-Encoding-and-Sanitization.md),
[V2](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x11-V2-Validation-and-Business-Logic.md),
[V3](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x12-V3-Web-Frontend-Security.md),
[V4](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x13-V4-API-and-Web-Service.md),
[V11](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x20-V11-Cryptography.md),
[V12](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x21-V12-Secure-Communication.md),
[V13](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x22-V13-Configuration.md),
[V14](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x23-V14-Data-Protection.md),
[V15](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x24-V15-Secure-Coding-and-Architecture.md),
[V16](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x25-V16-Security-Logging-and-Error-Handling.md).
Para la cobertura individual L1 se consultaron además los siete capítulos
restantes, de modo que los diecisiete quedan verificados contra el tag `v5.0.0`:
[V5](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x14-V5-File-Handling.md),
[V6](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x15-V6-Authentication.md),
[V7](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x16-V7-Session-Management.md),
[V8](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x17-V8-Authorization.md),
[V9](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x18-V9-Self-contained-Tokens.md),
[V10](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x19-V10-OAuth-and-OIDC.md),
[V17](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x26-V17-WebRTC.md).

Este mapeo de controles seleccionados contiene **72 requisitos ASVS 5.0.0
distintos**, sin repeticiones, distribuidos en 27 filas y diez capítulos:
**18 de nivel L1, 46 de nivel L2 y 8 de nivel L3**. Cada ID y cada nivel se
verificaron individualmente contra el texto oficial del capítulo correspondiente
en el tag `v5.0.0`, y ninguno resultó inexistente ni con nivel distinto del
declarado.

**Relación entre las dos tablas, para que ningún conteo se lea mal.** Son
recuentos de cosas distintas y no deben sumarse:

| Tabla | Qué recuenta | Total |
|---|---|---|
| Cobertura individual L1 | **Todos** los requisitos L1 de V1 a V17, aplicables o no | **70** evaluados: 23 aplican, 3 parcial, 44 no aplican |
| Mapeo seleccionado | Requisitos con control y prueba concretos asignados, de cualquier nivel | **72**: 18 L1, 46 L2, 8 L3 |

Los 18 L1 del mapeo seleccionado son un subconjunto de los 26 L1 que la
cobertura individual clasifica como aplicables o parciales; los 8 L1 aplicables
restantes quedan cubiertos por controles que la tabla de cobertura nombra
individualmente, sin fila propia en el mapeo. Los capítulos sin IDs en el mapeo
seleccionado son V5, V6, V7, V8, V9, V10 y V17; **V5 y V8 ya no son «no
aplicables»**, sino parcialmente aplicables con requisitos L1 que sí aplican
—5.3.1, 5.3.2, 8.1.1, 8.2.1 y 8.3.1—, recogidos en la cobertura individual.

Identity, contraseñas/credenciales de producto, autenticación, sesiones, OAuth,
tokens de sesión y tenancy están fuera de alcance; no se inventan controles
implementados para ellos. Sí aplican secretos operacionales, permisos de CI y
aislamiento de red. Uploads, GraphQL, WebSocket, SQL y negocio no existen aquí.
ASVS no certifica por sí solo workflows CI, accesibilidad ni regresión visual:
esos gates se fundamentan en Constitución, AC y SEC, sin fabricar equivalencias.

**La aplicabilidad requisito a requisito está cerrada.** La sección *Cobertura
individual de todos los requisitos L1 de V1 a V17* recorre los 70 requisitos L1
del estándar —no solo los de los capítulos aplicables— y asigna a cada uno su
nivel oficial, su aplicabilidad, una razón concreta y, cuando aplica, el control
de SPEC-001 y la verificación prevista. Por eso deja de existir la pregunta
abierta que reservaba ese checklist para más adelante.

Lo que **sigue pendiente** no es la evaluación, sino su demostración: ningún
requisito puede darse por cumplido hasta que exista implementación y evidencia
ejecutada. Esta sección documenta aplicabilidad y diseño, **no conformidad**, y
en ningún caso afirma certificación ASVS.

## Migraciones

**No aplica:** ninguna migración ni schema de DB. No generar herramientas,
archivos vacíos de migración, bootstrap de datos o políticas RLS. Reapertura
obligatoria según data-model si cambia esta condición. Versiones de tokens,
contrato o artefactos no son migraciones de datos de aplicación.

## Rollout y rollback

0. Requisito previo de etapa SDD: conservar las aprobaciones vigentes de
   `security.md` y `contracts/openapi.yaml` 1.1.0. `ADR-0003` fue aceptado y
   aprobado independientemente por el Product Owner el `2026-10-01`; después,
   este plan `1.0.1` fue aprobado mediante un acto independiente. En ese
   momento, SPEC-001 pasó de `Design Approved` a `Plan Approved` y `tasks.md`
   1.0.2 permanecía en `Draft`, con aprobación `Pendiente`. Posteriormente,
   el Product Owner aprobó `tasks.md` 1.0.2 mediante otro acto independiente
   el `2026-10-01`; SPEC-001 está ahora en `Ready for Implementation`, que
   representa únicamente disponibilidad documental y no autoriza ejecutar
   tareas. `TASK-001-001` no fue reanudada y ninguna tarea fue completada.
   Reanudar `TASK-001-001` y avanzar SPEC-001 a `In Progress` requieren una
   autorización posterior e independiente. Ninguna otra tarea puede iniciarse
   automáticamente por efecto de esas aprobaciones sin autorización conforme
   al gobierno vigente; la implementación continúa pausada.
1. Local: checkout limpio, configuración validada, redes/límites inspeccionados,
   arranque y tests; comprobar separación health público/ready operacional, las
   fases y cotas de timeout con su deadline total, y la taxonomía cerrada de
   resultados no satisfactorios.
2. CI: mismos scripts, gates completos verificados por el job `GC` de
   completitud **y por la protección de rama externa**, builds limpios, SBOM y
   evidencia privada; browsers reales y revisiones manuales necesarias antes de
   cerrar aceptación.
3. Entorno no productivo: promover digest verificado, validar TLS/acceso/recursos,
   supervisión de logs/OTLP, shutdown, dos réplicas y baseline autorizado.

No autoriza despliegue productivo automático. Readiness retira tráfico en inicio,
cierre o fallo obligatorio; health solo comprueba vida. Pruebas de rollout no
demuestran disponibilidad ni journeys de negocio inexistentes.
Rollback: último artefacto verificado por digest y configuración compatible,
incluyendo frontend/tokens coherentes; preservar evidencia del fallo y revisión.
En primera instalación sin artefacto previo, detener exposición, no promover
uno sin verificar. Cambios incompatibles requieren forward fix o nueva aprobación,
no alteración silenciosa de contrato/diseño. Sin migración o rollback de datos.

## Riesgos

| Riesgo | Mitigación | Señal | Criterio de escalamiento |
|---|---|---|---|
| Complejidad excesiva | Solo componentes con consumidor y gates trazados; sin dominios ficticios | Herramientas/abstracciones sin REQ/SEC | Detener incorporación y revisar alcance con PO |
| Divergencia contrato / implementación / borde | Comparación semántica del esquema en memoria más estados realmente observables tras el límite HTTP, sin autoactualizar el contrato | Estado, cuerpo o header observable fuera del contrato en cualquiera de las dos rutas, incluido un rechazo del borde no documentado | Bloquear G4 y revisar la implementación o solicitar un cambio aprobado del contrato |
| Flakiness visual multiplataforma | Baseline por contexto exacto, fuentes/tiempo estables, revisión real | Fallos intermitentes sin cambio de producto | Investigar antes de subir tolerancia o promover |
| Cardinalidad creciente | Catálogos finitos, presupuesto y prueba de series | Atributo nuevo o más de 10000 series | Bloquear export/config y corregir instrumentación |
| Pérdida o tormenta de logs | Colas acotadas, conciliación, canal obligatorio separado y supervisor | Pendientes crecientes/fallo explícito o recepción incierta | Retirar readiness, parar promoción/carga y activar operador |
| Evidencia falsa de rendimiento | Perturbación de sensores, dos ejecuciones y datos válidos | CPU/RSS constantes, gaps o resultados inconsistentes | Invalidar informe; no inferir capacidad |
| Exposición de ready/catálogo/evidencia/`web-static` | Deny by default, redes separadas y prueba exterior | Ruta/puerto/artefacto accesible indebidamente, o acceso directo a `web-static` | Bloquear promoción y retirar exposición |
| Fallback SPA captura `/ready` | Guardia pública explícita antes del catch-all, prueba exterior de ausencia de ruta y prueba directa de que NGINX no crea una operación de API | `GET /ready` público devuelve 200, llega al origen de estáticos o se comporta como readiness | Bloquear G4/G8 y corregir rutas antes de exponer |
| Supply chain | Pins, revisión, sandbox, SBOM y escaneo | SHA/digest desconocido o vulnerabilidad bloqueante | Detener entrega y triage, sin excepción implícita |
| Sobreajuste temprano de límites | Valores explícitos, presupuestos y escenarios separados | Rechazos normales o memoria saturada en baseline | Recalibrar con evidencia; no eliminar límites |
| Dependencia accidental de herramientas | Scripts portables, formatos abiertos y contratos normativos | Solo funciona mediante proveedor/servicio particular | Revisar adaptador sin cambiar requisitos |
| Browser real no disponible | Matriz manual bloqueante complementa Playwright | Safari/iOS/versión anterior sin evidencia | No cerrar aceptación; obtener entorno autorizado |
| Pérdida total del proceso/canal | Supervisión y conciliación externa; sin prometer durabilidad en RAM | SIGKILL, OOM o hueco de recepción | Registrar ventana incierta y bloquear rollout hasta resolver |
| Gate obligatorio que no se ejecuta | Manifiesto versionado `tooling/ci/required-gates.yaml` con etapa por gate, job `GC` de completitud dentro del workflow y protección de rama externa verificada por `scripts/ci/verify-required-checks` | Gate ausente, saltado, cancelado, deshabilitado o condicionado; conjunto reportado distinto del declarado; checks requeridos divergentes del manifiesto o sin `GC` | Bloquear la integración; nunca tratar `skipped`, `cancelled` o ausente como satisfactorio |
| Artefacto normativo pendiente de reaprobación | Estado explícito en `spec.md`, plan y tareas; G0 verifica aprobaciones vigentes antes que cualquier otro gate | `plan.md` 1.0.1 o `tasks.md` 1.0.2 siguen en Draft, o una aprobación protegida deja de estar vigente, al intentar implementar | Detener la etapa SDD y solicitar la aprobación explícita correspondiente |
| Confusión entre rechazo deliberado y defecto | Taxonomía cerrada 429/413/403/408/502/503/504/500, con 500 reservado a excepción interna no controlada y sin categoría residual de anomalía; métricas con `reason` y `outcome` enumerados | Saturación, admisión fallida, vencimiento de recepción, fallo upstream, vencimiento de upstream o timeout de readiness reportados como 500; reaparición de una categoría «sin causa clasificada» | Corregir la representación antes de promover; no ajustar el contrato para aceptar la conflación |
| Vencimiento de upstream mal representado | 504 `GATEWAY_TIMEOUT` contratado en ambas operaciones, emitido por el borde al vencer el plazo total de upstream o la ociosidad; 502 para el fallo anterior a encabezados; 429 reservado a frecuencia/admisión y 500 a excepción interna | Un vencimiento reportado como 429, 500, 502 o 503; un fallo de conexión reportado como 504; un cierre de conexión contabilizado como 504 | Bloquear G4; corregir la representación antes de promover, sin reutilizar el código de otra condición |
| Estado nativo del adaptador escapando al contrato | Traducción de los estados nativos del adaptador a los códigos contratados mediante su configuración de respuestas locales, **seleccionada solo por el indicador de resultado**, nunca por umbrales de duración | Un 503, 404 o 408 con cuerpo ajeno al contrato observable sobre una ruta contratada; una regla de traducción que dependa del tiempo transcurrido | Bloquear G4; corregir la configuración del borde antes de promover |
| Guarda entre operaciones confundida con límite total | `edge.upstream.total_deadline` de 3 s como único total del intento, con la ociosidad degradada a guarda interna; ordenamiento 2 s < 3 s y precedencia encabezados ≤ recepción total validados antes del arranque | Un upstream que gotea y extiende el intento más allá del plazo total, una configuración con el plazo de encabezados por encima del de recepción total, o una evidencia que afirme una cota desde el primer byte del cliente | Bloquear la configuración; recalcular la composición de plazos antes de aceptar cualquier cambio de valores |
| `/ready` alcanzable desde la entrada pública en preview/staging | Listener operacional enlazado solo a la red privada, `/ready` ausente del mapa público, allowlist inventariada evaluada sobre el peer validado y validación previa al bind | `public_exposure` distinto de false, `/ready` en rutas públicas, allowlist vacía o con origen no inventariado, o acceso concedido por una cabecera de origen | Impedir el despliegue; el validador falla cerrado antes de aceptar tráfico |

Riesgos aceptados: **Ninguno**. Responsable, expiración y plan de eliminación de
riesgos aceptados: no aplicables. Riesgos anteriores son trabajo de mitigación,
no aceptación de riesgo alto/crítico ni permiso para omitir controles.

## Decisiones descartadas

- PostgreSQL, Redis, persistencia técnica ficticia y pools para justificar métricas.
- Microservicios, dominios o colas de negocio anticipados; copiar módulos legacy.
- Material UI, Ant Design, Carbon React u otro sistema visual completo.
- Paleta Tailwind como sustituto de tokens, valores visuales provisionales,
  CDN de fuentes, recursos externos, overlays y navegación de negocio.
- Publicar readiness, documentación automática, métricas HTTP o Storybook.
- Sesión/cookie/API key de producto para resolver autorización operacional.
- Logs INFO por request exitoso, descarte silencioso de seguridad y etiquetas
  métricas con identificadores; usar cero para simular métricas N/A.
- Igualdad de píxeles entre plataformas, aceptar baselines automáticamente o
  usar screenshots como sustituto de funcionalidad, teclado o seguridad.
- Exponer secretos a PR, ejecutar artefactos no confiables desde jobs privilegiados,
  referencias flotantes o promoción sin procedencia y revisión.
- Interpretar health/baseline como capacidad de negocio o prometer una tasa de RPS.
- Representar saturación, admisión fallida, vencimiento de upstream o timeout
  de readiness como `500 INTERNAL_ERROR`; conservar una categoría de «anomalía
  sin causa clasificada» que permitiera reintroducir esa conflación; contratar
  un código `SERVICE_OVERLOADED` que SPEC-001 no necesita.
- Devolver 429 a una solicitud ya admitida que agotó su plazo: 429 queda
  reservado a límites de frecuencia y admisión, y un vencimiento no es un
  rechazo deliberado.
- Contabilizar como 504 un cierre de conexión en el que no se emitió ningún
  estado HTTP, o presentarlo como código observado en cualquier evidencia.
- Enumerar alternativas de plataforma para `/ready` en preview/staging en lugar
  de decidir el mecanismo; confiar en cabeceras de origen para conceder acceso
  operacional.
- Devolver 413 por un cuerpo inesperado dentro del presupuesto, o por exceso de
  URI o de cabeceras, que no son rechazos de tamaño de cuerpo.
- Presentar una guarda entre operaciones como límite total de la solicitud, o
  sumar su valor al del timeout de conexión como si fuera el peor caso del
  borde.
- Seleccionar entre 502 y 504 mediante un umbral de duración sobre el mismo
  indicador de resultado: se probó y clasifica mal en silencio en cuanto el
  plazo de conexión baja del umbral.
- Prometer una retención de la respuesta upstream —de 1 KiB o de cualquier otro
  tamaño— para mantener abierta la ventana de sustitución por 504: el adaptador
  no ofrece un límite por respuesta con el que acotarla de forma verificable.
- Afirmar una cota de tiempo medida desde el primer byte del cliente o hasta la
  entrega al navegador: la configuración del borde no impone ninguna de las dos.
- Tratar el 404 de la entrada pública sobre `/ready` como una respuesta de la
  operación de readiness, que solo existe en la entrada operacional.
- Persistir la preferencia de idioma en `localStorage`, cookies, servidor o
  cualquier preferencia de usuario o tenant todavía inexistente.
- Ampliar el mapeo de controles de una prueba negativa ya aprobada para
  aparentar cobertura completa en lugar de corregir la brecha en `security.md`.
- Invocar autorizaciones del Product Owner que no consten registradas en el
  repositorio como fundamento de una decisión del plan.

## Preguntas abiertas

### A0. Decisiones específicas ya autorizadas por el Product Owner

Estas dos **sí** cuentan con autorización expresa del Product Owner como
decisiones concretas, y por eso no figuran ni como propuesta pendiente ni como
pregunta abierta:

1. **Nivel ASVS.** OWASP ASVS 5.0.0 **L1 como baseline** de SPEC-001, con
   controles L2 y L3 seleccionados donde el riesgo de esta foundation lo exige.
2. **Ausencia de persistencia de idioma en SPEC-001.** La selección manual de
   idioma no se persiste: vive solo en el estado de la página y una recarga
   vuelve a resolver navegador y fallback.

**Alcance exacto de aquella autorización.** Cubrió esas dos decisiones de
contenido y nada más; por sí sola no aprobó el plan, `security.md` ni el
contrato. `security.md` y OpenAPI 1.1.0 fueron aprobados posteriormente el
`2026-09-25`; este plan reabierto fue aprobado independientemente el
`2026-10-01`. La autorización de
aquellas dos decisiones no tenía una fecha ni referencia documental de
aprobación propia que pudiera inventarse retrospectivamente.

### A. Decisiones históricas del borrador 1.0.0, aprobadas el 2026-09-25

Esta lista conserva el razonamiento del borrador original. Sus decisiones
quedaron aprobadas con `plan.md` 1.0.0. La nueva topología de `web-static` fue
aprobada como decisión el `2026-09-30`; este documento revisado fue aprobado
independientemente el `2026-10-01`.

1. Exponer públicamente solo `GET /health` con su respuesta mínima, sustentado
   en `SEC-001-004`, `SEC-001-029` y `x-public-exposure` del contrato.
2. La taxonomía cerrada de resultados no satisfactorios: 429 **solo** por
   frecuencia o admisión, 413 por exceder el presupuesto del cuerpo, 403 por
   acceso operacional denegado, **408 `REQUEST_TIMEOUT` por vencimiento de la
   recepción de una solicitud ya encaminada**, 503 `{status: not_ready}`
   **solo** por dependencia obligatoria de readiness, **502
   `UPSTREAM_UNAVAILABLE` por los fallos upstream detectados por el borde antes
   de recibir encabezados válidos del upstream y antes de comprometer
   encabezados de respuesta downstream (UF, UC, UR)**,
   **504 `GATEWAY_TIMEOUT` solo por vencimiento del plazo upstream total (UT) o
   de la ociosidad del flujo antes de comprometer encabezados (SI)** y 500
   **solo** para excepción interna no controlada; más el
   cierre de conexión sin estado HTTP y la interrupción posterior a los
   encabezados, que no se representan con ningún código.
3. Contratar 504, 502 y 408 en ambas operaciones, que sustituye la declaración
   anterior de que SPEC-001 no documentaría 504. Requirió la reaprobación del
   contrato, registrada el `2026-09-25`. Incluye **aceptar expresamente** que
   agrupar UF, UC y UR bajo 502 pierde esa distinción en el código público y la conserva solo en
   diagnóstico operacional, y que **UC y UR no fueron observados en el POC**.
4. El plazo upstream total del borde de 3 s contado desde que la solicitud
   downstream está completamente recibida, con la ociosidad degradada a guarda
   interna, el deadline interno de aplicación de 2 s desde la admisión
   **contenido** en ese plazo, y `request_headers_timeout` 1 s **contenido** en
   `request_timeout` 2 s, fases que **preceden** al inicio de `route.timeout`;
   sin prometer ninguna cota desde el primer byte del cliente, ninguna garantía
   end-to-end de menos de 4 s desde el navegador ni ninguna sobre la velocidad
   de entrega al cliente.
5. El techo inicial de buffers de 16 MiB por proceso y `P × 16 MiB` agregado.
6. El mecanismo de acceso a `/ready` en preview/staging: listener operacional
   enlazado solo a la red privada, sin ruta pública, con allowlist inventariada
   deny by default, validación previa al bind y batería de once casos que cubre
   contextos permitidos, denegados y con origen falsificado.
7. El conjunto obligatorio de gates —`G0`–`G10` y `GC`, con etapa por gate— y
   la protección de rama como control de integración externo a los jobs.
8. La base tecnológica concreta (FastAPI, React/TypeScript/Vite, Tailwind sobre
   variables del diseño, Storybook, Playwright, i18next, OpenTelemetry, k6,
   **Envoy** como adaptador del límite HTTP), habilitada porque `spec.md` y
   `ui-design.md` declaran expresamente que esas elecciones no se fijan en la
   spec. La versión y el digest ejercitados en el POC son evidencia de
   viabilidad del adaptador, **no** la versión de entrega, que sigue pendiente
   de resolver como el resto de la matriz.

### B. Preguntas materiales todavía abiertas

1. **Aprobación de tareas y reanudación.** Las aprobaciones de `security.md` y
   `contracts/openapi.yaml` 1.1.0 permanecen vigentes. `ADR-0003` fue aceptado
   y aprobado independientemente el `2026-10-01`, antes de la aprobación de
   `plan.md` 1.0.1. Al aprobarse este plan, `tasks.md` 1.0.2-draft requería
   aprobación independiente y SPEC-001 estaba en `Plan Approved`.
   Posteriormente, el Product Owner aprobó `tasks.md` 1.0.2 el `2026-10-01` y
   SPEC-001 volvió a `Ready for Implementation`, solo como disponibilidad
   documental. `TASK-001-001` no fue reanudada, ninguna tarea fue completada y
   la implementación sigue pausada; avanzar a `In Progress` y reanudar
   `TASK-001-001` requieren autorización posterior e independiente.
2. **Registro normativo del par de contraste del wordmark.** El cálculo está
   hecho y su comprobación fijada: `text.inverse` sobre `color.navigation` da
   `16.6928:1`, muy por encima del umbral, y `TEST-001-046` lo medirá sobre el
   render real. Lo que queda **no es una duda técnica** sino documental: ese par
   no figura en la tabla de contrastes de `ui-design.md` 1.0.0, y añadirlo
   corresponde a ese documento y a su reaprobación. Este plan no puede
   modificarlo y no lo da por registrado.

**B.2 y B.3 anteriores quedan cerradas por esta corrección.** La
materialización del segmento operacional de `/ready` en preview/staging pasó de
lista de alternativas a **decisión adoptada** —listener enlazado solo a la red
privada, sin ruta pública, con allowlist inventariada deny by default—, con
campos obligatorios, validación previa al bind y once casos de prueba. La
representación del vencimiento de plazo pasó de propuesta pendiente a
**decisión adoptada**: se retiró el 429 y se contrató **504 `GATEWAY_TIMEOUT`**
en ambas operaciones, con las relaciones de contención y precedencia entre
plazos fijadas en la sección de plazos. Este plan **no afirma** que esas
relaciones garanticen la emisión ni la entrega completa de una respuesta antes
del timeout de 4 s del navegador: **no existe tal garantía end-to-end**.
Ninguna de las dos sigue bloqueando la preparación del plan; el bloqueo
vigente para reanudar `TASK-001-001` es obtener una autorización posterior e
independiente y registrar expresamente la transición de SPEC-001 desde
`Ready for Implementation` a `In Progress`. La aprobación de `tasks.md` 1.0.2
ya ocurrió y no constituye un bloqueo vigente.

### C. Pendientes verificables de implementación

No son decisiones flotantes autorizadas: resolver y registrar versiones
exactas, hashes y digests compatibles; provisionar contextos reales de
browsers; demostrar límites, retención y canal operacional; crear
`contracts/observability/log-event.schema.json`,
`tooling/ci/required-gates.yaml` y `scripts/ci/verify-required-checks`;
ejecutar la batería de conformidad de `/ready` en el entorno elegido; producir
evidencia y validar los presupuestos iniciales. Fallar cualquiera bloquea el gate correspondiente. Si resolverlo
exige cambiar comportamiento, seguridad, alcance o diseño aprobado, detener y
solicitar cambio y aprobación.

**Pendientes que el POC del borde dejó expresamente abiertos.** El POC observó
403, 408, 413, 429, 502 y 504 a través del borde, la conservación exacta y la
sustitución íntegra del identificador, y el paso sin alteración del 503. **No
ejercitó**, y por tanto siguen siendo casos previstos que requieren
verificación posterior:

1. El **200 de `/ready`**: el upstream sintético siempre respondió no listo.
2. El **500 `INTERNAL_ERROR`**: no se provocó ninguna excepción interna.
3. Las causas **UC** —terminación de la conexión upstream antes de
   encabezados— y **UR** —reset upstream antes de encabezados—, que 502 agrupa
   junto a UF: solo se ejercitó **UF**, en sus dos formas de conexión rechazada
   y timeout de conexión. UC y UR son **comportamiento contratado, no
   observado en el POC**, y `TEST-001-030` es su verificación pendiente.
4. La adecuación del **generador del identificador sustituto** del borde, cuya
   documentación no lo caracteriza como criptográfico.
5. Las respuestas del borde sobre la **entrada operacional** distintas de 403 y
   del paso del 503: 408, 413, 429, 502 y 504 se ejercitaron en la entrada
   pública y deben repetirse en la operacional.

Ninguna observación del POC es evidencia de conformidad de SPEC-001, que no
tiene implementación, y ninguna sustituye a `TEST-001-030` ni a
`TEST-001-039`.

## Control de aprobación

- El borrador `1.0.0-draft` se preparó el `2026-09-21` y permaneció sin
  aprobación mientras `security.md` y `contracts/openapi.yaml` requerían
  reaprobación y SPEC-001 no había recuperado `Design Approved`.
- El `2026-09-25`, tras registrarse esas aprobaciones y restaurarse
  `Design Approved`, el Product Owner aprobó explícitamente este plan como
  versión `1.0.0`, incluidas sus decisiones arquitectónicas, operacionales, de
  seguridad, observabilidad, pruebas, límites y riesgos documentados.
- El `2026-09-30`, durante `TASK-001-001`, se confirmó que el filtro
  `file_server` de Envoy 1.39.1 está [declarado oficialmente incompleto y no
  apto para producción](https://www.envoyproxy.io/docs/envoy/v1.39.1/api-v3/extensions/filters/http/file_server/v3/file_server.proto).
  El Product Owner aprobó separar el origen estático en `web-static` NGINX
  interno, mantener Envoy como único borde y fijar las familias OCI detalladas
  en este borrador. Se reabre el plan como `1.0.1-draft`; la implementación
  queda pausada y esta revisión requiere aprobación independiente. La decisión
  no aprueba la versión ni los digests de NGINX, que resolverá `TASK-001-001`.
  Se crea `ADR-0003` como borrador obligatorio para esta decisión estructural;
  su aprobación independiente precede a la del plan. Se corrigen las referencias
  vigentes al contrato OpenAPI 1.1.0, aprobado el `2026-09-25`, y se precisan
  en las tareas reabiertas el cierre declarativo del borde, la prueba negativa
  interna de métodos, los logs de `web-static`, los cuatro servicios y el inicio
  parcial de `TASK-001-001`. Nada de esto constituye implementación.
- El `2026-10-01`, después de la aceptación y aprobación independiente de
  `ADR-0003`, el Product Owner aprobó explícitamente `plan.md` 1.0.1,
  incluidas sus decisiones arquitectónicas y la topología aceptada en el ADR,
  sus decisiones operacionales, de seguridad, observabilidad y pruebas, sus
  límites, presupuestos de recursos, riesgos, rollout y rollback. Esta
  aprobación cierra la reapertura arquitectónica del plan y permite que
  SPEC-001 pase de `Design Approved` a `Plan Approved`. `tasks.md` 1.0.2
  continúa en `Draft` con aprobación pendiente; ninguna tarea se reanudó o
  completó por este acto y la implementación permanece pausada.
- El `2026-10-01`, tras la aprobación independiente de `tasks.md` 1.0.2, se
  sincronizaron las referencias administrativas posteriores: las tareas están
  aprobadas y SPEC-001 está en `Ready for Implementation` solo como
  disponibilidad documental. Esta errata no cambia decisiones técnicas ni la
  versión `1.0.1` o la aprobación vigente de este plan; la implementación
  continúa pausada.

### Aprobación histórica de `plan.md` 1.0.0

- **Estado:** `Aprobado`
- **Aprobado por:** `Product Owner`
- **Fecha:** `2026-09-25`
- **Comentario o referencia:** Aprobación explícita del Product Owner del
  `2026-09-25` del plan de SPEC-001 Platform Foundation, incluidas las
  decisiones arquitectónicas, operacionales, de seguridad, observabilidad,
  pruebas, límites y riesgos documentados.

**Prerrequisitos de aquella aprobación:** `security.md` fue reaprobado,
`contracts/openapi.yaml` 1.1.0 fue aprobado y SPEC-001 volvió a
`Design Approved` antes de la aprobación independiente de este plan. Las
menciones anteriores al estado de borrador y a aprobaciones pendientes
documentan su preparación y no sustituyen el estado vigente registrado aquí.

Aquella aprobación no autorizaba por sí sola crear `tasks.md` ni implementar
código; las autorizaciones posteriores constan en el historial de SPEC-001.

## Aprobación del plan

- **Estado:** `Aprobado`
- **Aprobado por:** `Product Owner`
- **Fecha:** `2026-10-01`
- **Comentario o referencia:** Aprobación explícita del Product Owner del
  `2026-10-01` de `plan.md` 1.0.1, posterior a la aprobación independiente de
  `ADR-0003`, incluidas las decisiones arquitectónicas, la topología aceptada
  en ese ADR, las decisiones operacionales, de seguridad, observabilidad y
  pruebas, los límites, presupuestos de recursos, riesgos, rollout y rollback.
  Esta aprobación documental no autoriza implementar ni aprueba `tasks.md` 1.0.2.
