# Seguridad de Platform Foundation

Referencia: `SPEC-001`

## Alcance y postura de seguridad

`[Seguro]` Este diseño cubre únicamente la foundation aprobada. `SPEC-001` no
incorpora autenticación, sesiones, usuarios, roles, organizaciones, tenants,
tablas tenant ni datos de negocio. Tampoco debe procesar datos reales de
pacientes o estudiantes, credenciales de producción ni información tenant.
Toda prueba, demostración y baseline usa exclusivamente datos sintéticos.

El sistema visual incorporado a `SPEC-001` amplía este diseño a
`ui-design.md`, tokens, componentes compartidos, catálogo ejecutable, recursos
visuales y evidencia de regresión visual. El catálogo es una superficie de
desarrollo y verificación distinta de la pantalla inicial pública; su
existencia no autoriza incluirlo en artefactos productivos ni publicarlo.

La ausencia de Identity no convierte las superficies operativas en públicas:
cuando un control requiera restricción, se aplica `deny by default` mediante el
límite de red o de plataforma que se defina en `plan.md`. La selección de
herramientas, proveedores, topología, valores operativos de límites y mecanismos
concretos queda fuera de este documento.

## Activos

- **Secretos y configuración operativa:** su exposición o manipulación puede
  comprometer componentes, dependencias, CI o entornos; una configuración
  inválida también puede producir un arranque inseguro.
- **Integridad del código, repositorio, CI y artefactos:** una alteración puede
  introducir comportamiento malicioso o publicar un artefacto distinto del
  código revisado.
- **Disponibilidad de backend, frontend y dependencias:** el agotamiento de
  procesos, conexiones, memoria, CPU o dependencias puede impedir prestar la
  foundation y falsear readiness.
- **Logs, métricas y diagnósticos:** pueden revelar información interna,
  permitir inyección, generar costos o perder evidencia operacional y de
  seguridad.
- **Reportes de rendimiento y evidencia de pruebas:** pueden filtrar
  identificadores, configuración o detalles internos, o sustentar conclusiones
  inválidas si las mediciones están ausentes o manipuladas.
- **Request ID y correlación:** su integridad permite investigar incidentes; un
  valor controlado por un cliente puede contaminar logs o agotar cardinalidad.
- **Contrato OpenAPI versionado:** su divergencia puede ocultar superficies,
  debilitar validaciones o publicar ejemplos y detalles sensibles.
- **Imágenes y artefactos de contenedor:** una imagen manipulada, vulnerable,
  privilegiada o con secretos incorporados compromete ejecución y supply chain.
- **Datos sintéticos de pruebas:** deben conservar su carácter artificial y no
  convertirse en un canal para copiar datos reales o credenciales productivas.
- **Pantalla inicial y contenido frontend:** contenido o headers inseguros
  pueden habilitar ejecución no prevista, filtración de diagnóstico o acceso
  desde orígenes no autorizados.
- **`ui-design.md`, su versión y aprobación:** su integridad conserva la
  dirección visual aprobada y permite verificar que el catálogo y la
  implementación corresponden a la misma decisión de producto.
- **Tokens y contratos de componentes compartidos:** una alteración o
  divergencia puede introducir estados, interacciones o excepciones no
  aprobadas y debilitar controles funcionales, accesibles o de seguridad.
- **Catálogo ejecutable, ejemplos, configuración y addons:** ejecutan contenido
  de desarrollo y CI que puede incorporar código, dependencias, destinos de red
  o información no confiables.
- **Snapshots, baselines, diffs y registros de revisión visual:** pueden
  contener información sensible, ser sustituidos o promoverse sin revisión, o
  presentar como satisfactoria una revisión que no cubre comportamiento ni
  seguridad.
- **Fuentes, iconos, SVG y otros recursos visuales y su procedencia:** pueden
  incorporar contenido activo, dependencias comprometidas, tracking o destinos
  externos capaces de filtrar información.

## Datos sensibles y datos permitidos

- **Prohibidos en cualquier entorno o artefacto de `SPEC-001`:** datos reales
  de pacientes o estudiantes; `user_id`, `tenant_id` u otros identificadores
  reales; contraseñas, tokens, cookies de sesión, claves, credenciales de
  producción; cuerpos sensibles; parámetros SQL; stack traces o configuración
  interna expuesta públicamente.
- **Sensibles operativos:** secretos y valores de configuración confidenciales,
  detalles internos de dependencias, hostnames internos, reportes crudos,
  diagnósticos, evidencia de CI y metadatos internos de ejecución o revisión.
  Solo pueden estar en el canal operacional autorizado que corresponda y nunca
  en respuestas públicas, frontend, ejemplos, contenido visual, snapshots,
  diffs, imágenes o repositorio.
- **Permitidos para pruebas:** valores inequívocamente sintéticos, no derivados
  de producción y sin posibilidad razonable de confundirse con una identidad,
  tenant o credencial real.

## Trust boundaries

- **Navegador ↔ frontend:** cruza contenido y configuración pública. Requiere
  contenido construido desde fuentes revisadas, headers defensivos, ausencia de
  secretos y diagnóstico depurado.
- **Frontend ↔ backend:** cruza solicitudes, respuestas, CORS y request ID. El
  backend valida toda entrada, aplica el contrato público y no confía en el
  frontend como control de autorización.
- **Backend ↔ dependencias:** cruza disponibilidad, timeouts, pools, errores y
  configuración sensible. Requiere límites, fallo seguro, sanitización y
  métricas útiles sin parámetros ni credenciales.
- **Aplicación ↔ plataforma de observabilidad:** cruza logs, métricas,
  diagnósticos y correlación. Requiere campos permitidos, redacción, dimensiones
  acotadas, control de volumen y acceso operacional restringido.
- **CI ↔ repositorio, dependencias, secretos y registros de artefactos:** cruza
  código potencialmente no confiable, credenciales y material publicable.
  Requiere mínimo privilegio, separación de contextos confiables/no confiables,
  referencias inmutables, revisión y gates bloqueantes.
- **Operador ↔ health, readiness, métricas y ejecución de baselines:** cruza
  superficies operativas capaces de revelar estado interno o consumir recursos.
  Requiere acceso explícito por recurso, respuesta mínima y trazabilidad de la
  ejecución.
- **Generador de carga ↔ entorno de pruebas:** cruza tráfico deliberadamente
  intensivo. Requiere autorización operacional, destino permitido verificado y
  aislamiento de producción por defecto.
- **Código, ejemplos y addons ↔ ejecución local y CI:** cruza contenido
  revisado o no confiable con procesos, navegador, filesystem, red y
  credenciales potenciales. Requiere ejecución delimitada, mínimo privilegio y
  separación entre contextos confiables y no confiables.
- **Navegador ↔ catálogo ejecutable:** cruza componentes, ejemplos, HTML, SVG,
  fuentes, iconos y configuración de desarrollo. El catálogo no hereda el
  carácter público de la pantalla inicial y su acceso queda delimitado.
- **Generación visual ↔ almacenamiento, publicación y revisión:** cruza
  snapshots, baselines, diffs, metadatos y decisiones de aprobación. Requiere
  datos sintéticos, integridad, procedencia y acceso restringido.
- **Pull requests no confiables ↔ promoción o aprobación de baselines:** cruza
  resultados producidos por código no confiable con procesos capaces de
  escribir, publicar o aprobar evidencia. La promoción solo ocurre desde un
  contexto confiable y revisado.
- **Catálogo y componentes ↔ recursos y destinos externos:** cruza fuentes,
  iconos, SVG, otros recursos visuales y conexiones salientes. Los recursos y
  destinos se deniegan por defecto salvo autorización documentada.
- **`ui-design.md` ↔ catálogo ↔ implementación:** cruza la decisión visual
  aprobada, su documentación ejecutable y el código resultante. Requiere
  concordancia versionada, verificaciones mecánicas cuando sean posibles y
  revisión estructural para equivalencias conceptuales o de interacción.

## Amenazas

| ID | Activo | Amenaza | Impacto | Probabilidad | Mitigación o referencia |
|---|---|---|---|---|---|
| `THREAT-001-001` | Secretos y configuración | Un secreto se incorpora o aparece en configuración, error, frontend, log, métrica, CI, reporte, ejemplo, contenido renderizado, snapshot, diff, metadatos, imagen o artefacto. | Crítico | Media | `SEC-001-001`, `SEC-001-002`, `SEC-001-003`, `SEC-001-008`, `SEC-001-019`, `SEC-001-025`, `SEC-001-033`, `SEC-001-034` |
| `THREAT-001-002` | Errores, health y readiness | Una respuesta revela stack traces, nombres de dependencias, credenciales, hostnames, configuración o detalles internos. | Alto | Media | `SEC-001-003`, `SEC-001-004`, `SEC-001-005` |
| `THREAT-001-003` | Métricas | El endpoint o canal de métricas queda públicamente accesible y permite reconocimiento o abuso. | Alto | Media | `SEC-001-013`, `SEC-001-029` |
| `THREAT-001-004` | Request ID y logs | Un request ID demasiado largo, mal formado o con caracteres de control produce log injection, confusión de correlación o consumo excesivo. | Alto | Alta | `SEC-001-006`, `SEC-001-007` |
| `THREAT-001-005` | Métricas | URL cruda o identificadores no acotados crean cardinalidad ilimitada y agotan el backend de métricas. | Alto | Alta | `SEC-001-011`, `SEC-001-012` |
| `THREAT-001-006` | Logs y disponibilidad | Una tormenta de requests, logs o errores degrada aplicación, almacenamiento u observabilidad. | Alto | Alta | `SEC-001-009`, `SEC-001-015` |
| `THREAT-001-007` | Evidencia de seguridad y auditoría | Sampling, fallos de transporte o saturación descartan silenciosamente eventos obligatorios. | Alto | Media | `SEC-001-010` |
| `THREAT-001-008` | Backend y dependencias | Abuso de endpoints, concurrencia o solicitudes lentas agota CPU, memoria, workers, conexiones o pools. | Alto | Alta | `SEC-001-015`, `SEC-001-016` |
| `THREAT-001-009` | Entorno de pruebas y producción | Una persona o automatización no autorizada inicia carga contra producción o un destino no aprobado. | Crítico | Media | `SEC-001-017`, `SEC-001-029` |
| `THREAT-001-010` | Reportes de rendimiento | Un reporte contiene identificadores, datos sensibles, URLs crudas, parámetros o detalles internos innecesarios. | Alto | Media | `SEC-001-018`, `SEC-001-019` |
| `THREAT-001-011` | Supply chain | Dependencias, acciones de CI, herramientas de build, imágenes, dependencias UI, addons, HTML/SVG, fuentes, iconos o recursos visuales comprometidos ejecutan código malicioso o alteran el catálogo y sus componentes. | Crítico | Media | `SEC-001-021`, `SEC-001-022`, `SEC-001-023`, `SEC-001-024`, `SEC-001-034` |
| `THREAT-001-012` | CI y secretos | Un pull request o código no confiable obtiene secretos, permisos de escritura o credenciales de publicación, o logra que un proceso privilegiado publique indirectamente sus resultados o promueva sus baselines. | Crítico | Media | `SEC-001-020`, `SEC-001-023`, `SEC-001-024`, `SEC-001-033`, `SEC-001-035` |
| `THREAT-001-013` | Contenedores | Un contenedor ejecuta con usuario privilegiado, capacidades innecesarias o secretos incorporados. | Alto | Media | `SEC-001-025` |
| `THREAT-001-014` | Frontend, catálogo y API | CORS permisivo, headers inseguros, addons o contenido HTML/SVG, fuentes, iconos y recursos del catálogo no controlados habilitan acceso, transmisión o ejecución no prevista. | Alto | Media | `SEC-001-026`, `SEC-001-034` |
| `THREAT-001-015` | Diagnóstico frontend | Errores de red, cuerpos, tokens, URLs o detalles internos se filtran por consola o telemetría. | Alto | Media | `SEC-001-027` |
| `THREAT-001-016` | OpenAPI | El contrato diverge de la implementación, omite operaciones o documenta secretos, datos reales o detalles sensibles. | Alto | Media | `SEC-001-028` |
| `THREAT-001-017` | Métricas y capacidad | CPU, memoria, pools u otras métricas ausentes, constantes, inválidas o manipuladas ocultan saturación. | Alto | Media | `SEC-001-014`, `SEC-001-016` |
| `THREAT-001-018` | Configuración | Valores manipulados o defaults inseguros habilitan debug, exposición operativa, límites no acotados o destinos productivos peligrosos. | Alto | Media | `SEC-001-001`, `SEC-001-030` |
| `THREAT-001-019` | Código, artefactos y evidencia visual | Un artefacto, snapshot, diff o baseline no corresponde a la revisión y ejecución aprobadas, puede sustituirse sin detección o se promueve sin aprobación. | Crítico | Media | `SEC-001-023`, `SEC-001-024`, `SEC-001-035` |
| `THREAT-001-020` | Datos de prueba y evidencia visual | Se copian datos reales, identificadores, credenciales productivas o detalles sensibles a pruebas, ejemplos, contenido renderizado, snapshots, baselines, diffs o sus metadatos. | Crítico | Baja | `SEC-001-018`, `SEC-001-019`, `SEC-001-031`, `SEC-001-033`, `SEC-001-034` |
| `THREAT-001-021` | Disponibilidad y dependencias | Una dependencia lenta, caída o con respuesta hostil mantiene recursos ocupados y genera cascadas de fallos. | Alto | Alta | `SEC-001-005`, `SEC-001-015`, `SEC-001-016` |
| `THREAT-001-022` | Superficies operativas | Health, readiness, métricas o baselines se usan con un nivel de acceso distinto del autorizado. | Alto | Media | `SEC-001-004`, `SEC-001-005`, `SEC-001-013`, `SEC-001-017`, `SEC-001-029` |
| `THREAT-001-023` | Alcance de la foundation | Se introduce accidentalmente autenticación, sesión, usuario, tenant o dato de negocio sin actualizar el diseño. | Alto | Baja | `SEC-001-032`; control de cambio de `spec.md` y este documento |
| `THREAT-001-024` | Identity y sesiones futuras | Suplantación, robo de sesión o autorización incorrecta. No existe superficie de Identity en `SPEC-001`. | No aplicable en esta spec | No aplicable | Fuera de alcance; requiere una spec y threat model propios antes de implementarse. `SEC-001-032` evita su incorporación implícita. |
| `THREAT-001-025` | Datos tenant futuros | Acceso o asociación cross-tenant. `SPEC-001` no crea ni procesa recursos tenant. | No aplicable en esta spec | No aplicable | Fuera de alcance; ADR-0002 y una spec/diseño de datos posteriores regirán su implementación. `SEC-001-032` evita su incorporación implícita. |
| `THREAT-001-026` | Catálogo ejecutable | El catálogo queda expuesto, publicado o ejecutado desde un contexto no autorizado, se incorpora al artefacto productivo o accede implícitamente a credenciales y recursos del host. | Alto | Media | `SEC-001-029`, `SEC-001-030`, `SEC-001-033` |
| `THREAT-001-027` | Recursos visuales y privacidad | Fuentes, iconos, SVG, addons u otros recursos externos realizan tracking, transmiten información o acceden a destinos no autorizados. | Alto | Media | `SEC-001-021`, `SEC-001-022`, `SEC-001-034` |
| `THREAT-001-028` | Evidencia visual | Una captura coincidente, un diff aceptado o una actualización de baseline oculta o desactiva un fallo funcional, de accesibilidad o de seguridad. | Alto | Media | `SEC-001-023`, `SEC-001-035` |
| `THREAT-001-029` | Integridad del sistema visual | `ui-design.md`, el catálogo, los componentes implementados o una excepción de reutilización divergen sin detección o aprobación. | Alto | Media | `SEC-001-036` |

## Controles SEC-*

| ID | Control verificable | Trazabilidad |
|---|---|---|
| `SEC-001-001` | Cada componente recibe configuración operativa desde fuentes externas, declara campos obligatorios, tipo, rango y restricciones, rechaza valores ausentes, inválidos o inseguros antes de quedar listo y emite solo el nombre del campo y una causa sanitizada. La suite debe cubrir ausencia, formato inválido, exceso de longitud, caracteres maliciosos y valores inseguros para producción. | `REQ-001-004`; `AC-001-006`, `AC-001-007`, `AC-001-008` |
| `SEC-001-002` | Los secretos permanecen fuera del repositorio, historial nuevo, archivos de ejemplo con valores utilizables, imágenes, capas de build, frontend, OpenAPI, logs, métricas y reportes. Solo llegan al componente que los necesita por el mecanismo externo que defina el plan y no se reproducen al validar. | `REQ-001-004`, `REQ-001-010`, `REQ-001-014`; `NFR-001-006`, `NFR-001-009`; `AC-001-006`, `AC-001-018`, `AC-001-029`, `AC-001-031` |
| `SEC-001-003` | Las respuestas de error públicas usan únicamente código estable, mensaje seguro y request ID; nunca incluyen stack trace, excepción cruda, cuerpo sensible, secreto, configuración, hostname ni detalle interno de dependencias. Pruebas con marcadores sintéticos deben demostrar la sanitización de errores esperados y no controlados. | `REQ-001-007`; `NFR-001-006`; `AC-001-012`, `AC-001-013`, `AC-001-018` |
| `SEC-001-004` | Health solo expresa vida del proceso mediante una respuesta mínima y estable; no consulta ni enumera dependencias y no revela versión detallada, configuración, hostname, credenciales ni estado interno. Si el plan requiere exposición pública, esta es la única respuesta operativa que puede ser pública y conserva ese contenido mínimo. | `REQ-001-005`; `NFR-001-017`; `AC-001-009`, `AC-001-011`, `AC-001-041` |
| `SEC-001-005` | Readiness solo expresa listo/no listo y usa acceso operacional; puede comprobar dependencias obligatorias sin devolver nombres, direcciones, credenciales, hostnames, excepciones ni causa interna al cliente. Debe pasar a no listo cuando falle una dependencia obligatoria, mientras health conserva su semántica. | `REQ-001-006`; `AC-001-010`, `AC-001-011` |
| `SEC-001-006` | Un request ID recibido es aceptable solo si tiene entre 1 y 128 caracteres ASCII de los conjuntos alfanumérico, punto, guion y guion bajo (`[A-Za-z0-9._-]`). Cualquier valor ausente o que incumpla longitud o caracteres se descarta completo y se reemplaza por un ID generado por el backend; no se trunca ni normaliza el valor hostil. | `REQ-001-008`; `NFR-001-005`; `AC-001-014`, `AC-001-015`, `AC-001-016` |
| `SEC-001-007` | Los campos de log se serializan de forma estructurada y no por concatenación de texto controlado por el cliente. Se rechazan caracteres de control en request ID y se codifican o eliminan de otros campos no confiables según su contrato, de modo que una entrada no pueda crear registros, claves o líneas adicionales. | `REQ-001-008`, `REQ-001-009`; `NFR-001-005`, `NFR-001-007`; `AC-001-016`, `AC-001-017` |
| `SEC-001-008` | El esquema de logging aplica una lista permitida: timestamp, nivel, nombre estable de evento, componente, resultado, duración, plantilla de ruta, método, clase de estado y request ID validado cuando exista contexto. Prohíbe cuerpos, headers de credenciales, cookies, tokens, contraseñas, secretos, parámetros SQL, URLs crudas con query, datos personales, `user_id`, `tenant_id`, stack traces públicos y valores completos de configuración. Campos internos excepcionales requieren clasificación y acceso operacional. | `REQ-001-009`; `NFR-001-006`, `NFR-001-007`; `AC-001-017`, `AC-001-018` |
| `SEC-001-009` | El logging de alto volumen aplica reducción explícita y medible a eventos informativos exitosos y, ante tormentas, a errores no clasificados como seguridad/auditoría. Conserva por ventana contadores conciliables de eventos observados, emitidos individualmente, agregados y suprimidos, más muestras representativas con request ID cuando exista. Una prueba de tormenta debe demostrar que el volumen se reduce sin degradar el servicio ni ocultar el total. | `REQ-001-021`; `AC-001-040` |
| `SEC-001-010` | Los eventos clasificados como obligatorios de seguridad o auditoría no participan en el descarte silencioso ni en sampling que impida contabilizarlos. Todo evento se entrega, queda pendiente para reintento o produce una señal explícita y contabilizada de fallo; el plan debe definir clasificación, capacidad, degradación, reintento y respuesta operacional sin inventar eventos de dominios aún inexistentes. | `REQ-001-021`; `AC-001-040` |
| `SEC-001-011` | Las métricas HTTP usan plantillas de ruta de un catálogo finito y dimensiones con conjuntos documentados y acotados. Rutas desconocidas se agrupan en una categoría acotada; nunca se usa la URL cruda, query string o valor arbitrario como dimensión. La verificación inspecciona las series emitidas y falla ante crecimiento no previsto. | `REQ-001-018`, `REQ-001-021`; `NFR-001-014`; `AC-001-034`, `AC-001-036` |
| `SEC-001-012` | Ninguna dimensión métrica contiene request ID, `user_id`, `tenant_id`, identidad personal, secreto, hostname dinámico no acotado ni otro identificador único. Los request ID permanecen solo en logs permitidos y respuestas, no en métricas. | `REQ-001-018`; `NFR-001-014`; `AC-001-036` |
| `SEC-001-013` | El acceso a métricas es `deny by default` y queda limitado al plano interno u operador autorizado mediante el límite de red o plataforma que defina el plan. No se considera segura una ruta pública por ser poco conocida; una prueba desde el contexto no autorizado debe fallar. | `REQ-001-018`; `AC-001-034`, `AC-001-036` |
| `SEC-001-014` | Requests, duración, errores, throughput, CPU, memoria y, cuando apliquen, pools y dependencias tienen unidad, semántica y condiciones de validez documentadas. La evidencia se invalida si una métrica requerida falta, contiene valores imposibles/no finitos o permanece constante ante una variación controlada que debería cambiarla; CPU y memoria se ejercitan expresamente. | `REQ-001-018`, `REQ-001-019`; `NFR-001-015`; `AC-001-034`, `AC-001-035`, `AC-001-037` |
| `SEC-001-015` | Toda entrada y dependencia susceptible de consumir recursos queda sujeta a límites, timeouts y backpressure; ante saturación el sistema falla de forma acotada, no excede el presupuesto y no informa éxito falso. `plan.md` debe definir valores, nivel de aplicación y mecanismo para request size, concurrencia, pools, colas y dependencias aplicables, junto con pruebas de espera y timeout. | `REQ-001-020`; `NFR-001-016`; `AC-001-038`, `AC-001-039` |
| `SEC-001-016` | Cada pool o recurso compartido declara límite por proceso/réplica, timeout, presupuesto total y métricas de capacidad, uso, espera y saturación. La validación recalcula el total al variar workers/réplicas, rechaza configuraciones que lo exceden y demuestra que la saturación es observable. | `REQ-001-018`, `REQ-001-020`; `NFR-001-016`; `AC-001-035`, `AC-001-038`, `AC-001-039` |
| `SEC-001-017` | Baselines y pruebas de carga están denegados por defecto. Solo una ejecución operacional explícitamente autorizada puede iniciarlos, después de validar un destino de prueba permitido; la configuración inicial y los ejemplos no apuntan a producción. La evidencia registra autorizador, revisión, entorno, parámetros, inicio y resultado sin secretos. | `REQ-001-019`; `NFR-001-015`; `AC-001-037` |
| `SEC-001-018` | Los reportes de rendimiento se generan desde datos sintéticos y se sanitizan antes de conservarse o compartirse: admiten revisión, entorno no secreto, recursos agregados, escenario, estadísticas y métricas acotadas; excluyen IDs únicos, datos personales/tenant, credenciales, tokens, cuerpos, parámetros SQL, URLs crudas y stack traces. Una inspección automatizada con marcadores sintéticos debe fallar si alguno aparece. | `REQ-001-019`; `NFR-001-015`; `AC-001-037` |
| `SEC-001-019` | Pruebas, fixtures, documentación, demos, ejemplos, contenido renderizado y baselines usan exclusivamente datos sintéticos no derivados de producción. Un secreto sintético centinela debe ser detectable en respuesta, log, métrica, bundle frontend, imagen, snapshot, diff, metadatos y artefactos; su aparición hace fallar la verificación. Los controles automáticos se complementan con revisión documentada cuando el contenido de una imagen no pueda analizarse completamente. | `REQ-001-010`, `REQ-001-011`, `REQ-001-019`, `REQ-001-025`, `REQ-001-027`; `NFR-001-006`, `NFR-001-009`, `NFR-001-020`, `NFR-001-021`; `AC-001-018`, `AC-001-019`, `AC-001-031`, `AC-001-037`, `AC-001-049`, `AC-001-051` |
| `SEC-001-020` | CI separa contextos confiables y no confiables: cambios de pull requests o código no confiable no reciben secretos ni credenciales de escritura/publicación y no ejecutan pasos privilegiados con esos materiales. Un proceso privilegiado tampoco ejecuta ni publica indirectamente resultados producidos por contenido no confiable sin validación y revisión explícitas. Los permisos son mínimos, explícitos y verificables por job; un intento de acceso o promoción desde contexto no confiable debe fallar. | `REQ-001-010`, `REQ-001-025`, `REQ-001-027`; `NFR-001-002`, `NFR-001-009`, `NFR-001-020`, `NFR-001-021`; `AC-001-020`, `AC-001-031`, `AC-001-049`, `AC-001-051` |
| `SEC-001-021` | Dependencias, acciones de CI, imágenes, dependencias UI, addons, fuentes, iconos y otros recursos visuales se mantienen inventariados con su procedencia y uso, se fijan mediante versiones o referencias inmutables verificables cuando aplique, se revisan antes de actualizar y no usan referencias flotantes en el flujo de entrega. La política exacta de actualización y verificación se define en el plan, pero CI debe detectar una referencia mutable prohibida. | `REQ-001-010`, `REQ-001-014`, `REQ-001-022`, `REQ-001-023`, `REQ-001-025`; `NFR-001-002`, `NFR-001-008`, `NFR-001-009`, `NFR-001-020`; `AC-001-020`, `AC-001-029`, `AC-001-032`, `AC-001-043`, `AC-001-044`, `AC-001-049` |
| `SEC-001-022` | Cambios de dependencias, acciones, imágenes, dependencias UI, addons, fuentes, iconos y otros recursos visuales requieren revisión de procedencia, permisos, alcance, destinos externos, contenido activo y vulnerabilidades. Componentes no utilizados se excluyen; una dependencia, recurso o imagen de prueba con hallazgo crítico o alto explotable bloquea la entrega salvo excepción constitucional explícita. | `REQ-001-010`, `REQ-001-014`, `REQ-001-022`, `REQ-001-023`, `REQ-001-025`; `NFR-001-009`, `NFR-001-020`; `AC-001-032`, `AC-001-043`, `AC-001-044`, `AC-001-049` |
| `SEC-001-023` | Los artefactos de entrega y la evidencia visual se vinculan de forma verificable con la revisión y ejecución que los produjeron. Snapshots, diffs y baselines conservan integridad, resultado y aprobación antes de su uso, publicación o promoción. La sustitución, diferencia o promoción no explicada bloquea la entrega; el mecanismo concreto se decide en el plan. | `REQ-001-010`, `REQ-001-014`, `REQ-001-027`; `NFR-001-002`, `NFR-001-008`, `NFR-001-021`; `AC-001-020`, `AC-001-029`, `AC-001-051` |
| `SEC-001-024` | Repositorio, CI, registros de artefactos y almacenamiento de evidencia visual usan mínimo privilegio: lectura para verificaciones, y escritura, publicación o promoción solo para el contexto confiable, revisado y aprobado y el artefacto previsto. Acciones de terceros, pasos de build y procesos privilegiados no heredan credenciales que no necesitan ni ejecutan o publican indirectamente resultados no confiables. | `REQ-001-010`, `REQ-001-014`, `REQ-001-025`, `REQ-001-027`; `NFR-001-002`, `NFR-001-008`, `NFR-001-020`, `NFR-001-021`; `AC-001-020`, `AC-001-029`, `AC-001-049`, `AC-001-051` |
| `SEC-001-025` | Los contenedores de backend y frontend se ejecutan como usuario no privilegiado, sin modo privilegiado ni capacidades innecesarias, con una superficie mínima y sin secretos en imagen, capas, metadata o configuración frontend. La verificación inspecciona configuración e imagen y falla ante usuario root efectivo, privilegios indebidos o secretos centinela. | `REQ-001-014`; `NFR-001-008`, `NFR-001-009`; `AC-001-029`, `AC-001-031`, `AC-001-032` |
| `SEC-001-026` | CORS mantiene una lista explícita y acotada de orígenes, métodos y headers permitidos; no combina credenciales con origen comodín y rechaza orígenes no permitidos. Backend y frontend emiten una política documentada de headers y contenido seguro para su superficie; el conjunto exacto y sus valores se fijan en el plan y se prueban en build de producción. | `REQ-001-001`, `REQ-001-002`; `AC-001-001`, `AC-001-002`, `AC-001-003` |
| `SEC-001-027` | El diagnóstico frontend usa una lista permitida de nombre estable de evento, componente, severidad y estado técnico acotado. Excluye cuerpos y headers de requests/responses, tokens, cookies, secretos, identificadores, URLs con query, stack traces mostrados al usuario y detalles internos; producción no emite consola fuera del mecanismo documentado ni exige telemetría remota. | `REQ-001-009`; `NFR-001-006`, `NFR-001-012`; `AC-001-003`, `AC-001-019` |
| `SEC-001-028` | OpenAPI versiona todas y solo las operaciones HTTP implementadas por la foundation, declara el contrato seguro de errores y se valida automáticamente por diferencias. Descripciones, ejemplos y defaults no contienen secretos, credenciales, hostnames internos, datos reales ni valores que parezcan productivos; un marcador sintético o divergencia no aprobada falla CI. | `REQ-001-010`, `REQ-001-016`; `NFR-001-002`, `NFR-001-006`; `AC-001-020`, `AC-001-030` |
| `SEC-001-029` | La política de acceso operacional es explícita y `deny by default`: pantalla inicial pública de solo lectura; health para probes u operadores y, solo si el plan lo expone, respuesta pública mínima; readiness solo operacional; métricas solo plano interno u operador autorizado; baselines/carga solo con autorización operacional. El plan debe materializar cada límite de red/plataforma y las pruebas deben ejercer contextos permitidos y denegados. | `REQ-001-002`, `REQ-001-005`, `REQ-001-006`, `REQ-001-018`, `REQ-001-019`; `AC-001-002`, `AC-001-009`, `AC-001-010`, `AC-001-037` |
| `SEC-001-030` | La configuración destinada a producción rechaza modos de depuración, orígenes comodín incompatibles, exposición pública de métricas/readiness, destinos de carga y límites no acotados salvo una decisión operacional explícita permitida por este diseño. No existen defaults silenciosos que reduzcan seguridad; toda excepción requiere el proceso constitucional. | `REQ-001-004`, `REQ-001-018`, `REQ-001-020`; `AC-001-008`, `AC-001-036`, `AC-001-038` |
| `SEC-001-031` | Un control verificable impide usar datasets, dumps, fixtures o credenciales procedentes de producción en esta spec. La documentación identifica los datos como sintéticos y una revisión automatizada busca los marcadores prohibidos definidos en el plan antes de baselines y publicación de artefactos. | `REQ-001-011`, `REQ-001-019`; `AC-001-021`, `AC-001-037` |
| `SEC-001-032` | La implementación y las pruebas de alcance demuestran ausencia de login, sesiones, usuarios, roles, tenants, tablas/RLS tenant y datos de negocio. La aparición de cualquiera detiene el trabajo y exige actualizar y aprobar `spec.md`, este diseño y, para datos, el diseño correspondiente antes de continuar. | `REQ-001-001`, `REQ-001-002`; `AC-001-001`, `AC-001-002` |
| `SEC-001-033` | El catálogo queda excluido por defecto de los artefactos productivos. Su ejecución y acceso se delimitan a los contextos locales o de CI autorizados; una publicación separada requiere autorización explícita y un límite de acceso definido. El catálogo, sus ejemplos y addons operan sin credenciales productivas ni acceso implícito a secretos, filesystem, red u otros recursos del host. | `REQ-001-025`; `NFR-001-020`; `AC-001-049` |
| `SEC-001-034` | Los recursos y destinos externos del catálogo y los componentes se deniegan por defecto y solo se permiten con procedencia, necesidad y destino documentados. HTML, SVG, iconos, fuentes, ejemplos, addons u otro contenido activo no confiable se rechazan o neutralizan; la verificación demuestra que no ejecutan código ni transmiten información fuera del alcance autorizado. | `REQ-001-022`, `REQ-001-023`, `REQ-001-024`, `REQ-001-025`; `NFR-001-018`, `NFR-001-019`, `NFR-001-020`; `AC-001-043`, `AC-001-044`, `AC-001-045`, `AC-001-046`, `AC-001-047`, `AC-001-048`, `AC-001-049` |
| `SEC-001-035` | La comparación visual, un resultado coincidente y la actualización o promoción de baselines nunca sustituyen, omiten ni desactivan gates funcionales, de interacción, accesibilidad o seguridad. Todo cambio visual esperado requiere revisión explícita vinculada a la revisión de código, la ejecución y el diff correspondiente; un gate no visual fallido mantiene el resultado global no satisfactorio. | `REQ-001-027`; `NFR-001-018`, `NFR-001-021`; `AC-001-046`, `AC-001-051`, `AC-001-052` |
| `SEC-001-036` | La versión aprobada de `ui-design.md`, el catálogo y la implementación conservan concordancia trazable para tokens, componentes, variantes, estados, comportamiento responsive, interacción y excepciones de reutilización. Las divergencias detectables mecánicamente hacen fallar el control; las equivalencias conceptuales o de interacción requieren revisión estructural documentada. Una excepción solo se acepta con necesidad, justificación, alcance y criterio de eliminación o incorporación al sistema compartido. | `REQ-001-022`, `REQ-001-023`, `REQ-001-024`, `REQ-001-025`, `REQ-001-026`, `REQ-001-027`, `REQ-001-028`; `NFR-001-018`, `NFR-001-019`, `NFR-001-020`, `NFR-001-021`; `AC-001-042`, `AC-001-043`, `AC-001-044`, `AC-001-045`, `AC-001-046`, `AC-001-047`, `AC-001-048`, `AC-001-049`, `AC-001-050`, `AC-001-051`, `AC-001-052` |

## Autorización

| Recurso o acción | Acceso autorizado | Scope/ownership | Comportamiento por defecto |
|---|---|---|---|
| Pantalla inicial | Lectura pública | No aplica; contenido estático de foundation | Solo lectura; ninguna mutación o dato de negocio |
| Health | Probes u operadores; puede exponerse públicamente solo si el plan lo requiere | Límite de red/plataforma; respuesta pública siempre mínima | Denegar cualquier detalle adicional |
| Readiness | Probes y operadores autorizados | Límite de red/plataforma operacional | Denegar fuera del plano operacional; sin detalles internos |
| Métricas | Plano interno u operador autorizado | Límite de red/plataforma operacional | Denegar por defecto |
| Ejecutar baseline o prueba de carga | Operador expresamente autorizado para el entorno y destino | Autorización operacional registrada; destino de prueba permitido | Denegar por defecto, especialmente producción |
| Ejecutar catálogo localmente o en CI | Equipo de desarrollo, revisores y jobs autorizados | Entorno local o de CI delimitado, sin credenciales productivas ni acceso implícito a recursos del host | Permitido solo en local o CI; excluido de artefactos productivos |
| Publicar el catálogo | Proceso y revisores expresamente autorizados | Publicación separada, revisión identificada y límite de acceso definido | Denegar por defecto |
| Consultar evidencia visual | Revisores o procesos autorizados | Snapshots, diffs y metadatos vinculados a revisión y ejecución | Denegar por defecto |
| Promover un baseline visual | Proceso autorizado desde contexto confiable y revisado | Revisión de código, ejecución, diff y aprobación explícita vinculados | Denegar desde pull requests o contextos no confiables |
| Artefactos y configuración de CI | Jobs y mantenedores con necesidad explícita | Mínimo privilegio por job, repositorio y registro | Sin secretos ni escritura en contexto no confiable |

`[Seguro]` Esta spec no define permisos de producto `resource.action` porque no
existe Identity. La restricción operacional no puede quedar implícita: el
`plan.md` posterior deberá indicar el límite de red o plataforma que materializa
cada decisión de la tabla y cómo se verifica.

La autorización pública de lectura de la pantalla inicial no se extiende al
catálogo, su configuración, sus ejemplos ni la evidencia visual.

## Evidencia visual y catálogo

- **Contenido permitido:** tokens y contratos aprobados, componentes,
  variantes, estados, reglas y ejemplos mínimos de `SPEC-001`; valores y textos
  inequívocamente sintéticos; snapshots, diffs y metadatos técnicos mínimos
  necesarios para verificar la revisión.
- **Contenido prohibido:** secretos, tokens, credenciales, cookies, URLs o
  hostnames internos, configuración sensible, datos reales de pacientes o
  estudiantes, identificadores personales o tenant, cuerpos sensibles, datos
  de negocio, stack traces y metadatos no necesarios que revelen información
  del entorno o del host.
- **Acceso y publicación:** la ejecución local o en CI, el acceso a evidencia y
  la publicación separada del catálogo aplican `deny by default` conforme a
  `SEC-001-033`. El catálogo no forma parte de la pantalla inicial pública ni
  de los artefactos productivos por defecto.
- **Datos sintéticos:** catálogo, ejemplos, estados, snapshots, baselines y
  diffs usan exclusivamente datos sintéticos no derivados de producción. Las
  pruebas hostiles usan centinelas sintéticos y nunca credenciales reales.
- **Vínculo e integridad:** cada snapshot, diff y baseline identifica la
  revisión y ejecución que lo produjo, conserva su integridad y registra la
  aprobación exigida antes de publicación o promoción.
- **Snapshots, diffs y metadatos:** se sanitizan antes de conservarse o
  compartirse. Los controles automáticos se complementan con revisión
  documentada cuando el contenido visual no pueda analizarse completamente.
- **Recursos externos:** fuentes, iconos, SVG, addons y demás recursos o
  destinos externos están denegados por defecto; toda excepción documenta
  procedencia, necesidad, destino y revisión de contenido activo y transmisión.
- **Retención y publicación:** duración, ubicación, acceso, eliminación y
  mecanismo de publicación se definirán en `plan.md` o en la política
  operacional. Hasta entonces no se presume retención ni publicación pública.
- **Independencia de gates:** una comparación visual, un snapshot coincidente o
  una actualización de baseline no sustituye ni desactiva verificaciones
  funcionales, de interacción, accesibilidad o seguridad.

Snapshots, baselines y diffs visuales son evidencia de prueba; no se clasifican
como logs ni métricas y no heredan sus reglas de acceso o retención.

## Aislamiento tenant

- `tenant_id`: no aplica; `SPEC-001` no crea ni procesa recursos tenant.
- Política PostgreSQL RLS: no aplica en esta spec.
- Contexto tenant y transacción: no aplica en esta spec.
- Pruebas cross-tenant negativas: no aplican porque no existe superficie ni
  dato tenant que cruzar.
- Si durante diseño posterior o implementación aparece cualquier dato tenant,
  constituye un cambio de alcance. Se debe detener el trabajo y actualizar y
  aprobar `spec.md`, `security.md` y el diseño de datos antes de continuar; en
  ese momento aplican ADR-0002 y las pruebas cross-tenant correspondientes.

## Logging y métricas

### Logs permitidos

- timestamp, nivel, evento y componente;
- resultado, duración, método, plantilla de ruta y clase de estado;
- request ID ya validado cuando exista contexto;
- conteos agregados y motivo estable de reducción o fallo;
- detalles internos mínimos solo en el canal operacional autorizado y después
  de sanitización.

### Logs prohibidos

- contraseñas, tokens, cookies, secretos, credenciales y valores sensibles de
  configuración;
- cuerpos o headers sensibles, parámetros SQL y URLs crudas con query;
- datos reales de pacientes o estudiantes, datos tenant, `user_id`,
  `tenant_id` u otros identificadores personales;
- stack traces o detalles internos en canales públicos o frontend;
- entradas no confiables concatenadas sin codificación estructurada.

### Métricas permitidas

- contadores, histogramas y gauges necesarios para tráfico, duración, errores,
  throughput, CPU, memoria, pools y dependencias;
- método, plantilla de ruta de catálogo finito, clase de estado, componente y
  otras dimensiones enumeradas, finitas y documentadas;
- contadores de eventos de log observados, emitidos, agregados y suprimidos.

### Métricas prohibidas

- `request_id`, `user_id`, `tenant_id`, URL cruda, query string, datos
  personales, secretos, hostname dinámico no acotado o cualquier identificador
  único como dimensión;
- parámetros SQL, cuerpos o etiquetas procedentes directamente de entrada no
  confiable;
- series sin unidad o semántica verificable usadas como evidencia de capacidad.

### Volumen, pérdida y conservación

- Sampling, rate limiting o agregación se aplican de forma explícita y medible
  conforme a `SEC-001-009`; los contadores permiten conciliar la reducción.
- Seguridad y auditoría reciben el tratamiento especial de `SEC-001-010` y no
  se pierden silenciosamente. Esta foundation no inventa eventos de auditoría
  propios de dominios futuros.
- La retención, acceso detallado, capacidad, alertas, responsables y runbooks
  son decisiones pendientes del plan o de la política operacional. Hasta que
  se definan, no se presume retención ni acceso público y se mantiene mínimo
  privilegio.

## Pruebas negativas de seguridad

| ID | Prueba negativa y resultado esperado | Controles | Trazabilidad |
|---|---|---|---|
| `SEC-TEST-001-001` | Omitir configuración obligatoria: el componente falla cerrado, no queda listo y no revela valores. | `SEC-001-001` | `REQ-001-004`, `NFR-001-006`; `AC-001-007` |
| `SEC-TEST-001-002` | Proveer configuración con formato, longitud, caracteres o valor de producción inseguros: se rechaza antes de readiness y el diagnóstico se sanitiza. | `SEC-001-001`, `SEC-001-030` | `REQ-001-004`; `AC-001-008` |
| `SEC-TEST-001-003` | Introducir un secreto sintético en entrada/configuración y buscarlo en respuesta, logs, métricas, bundle frontend, OpenAPI, imagen y artefactos: cualquier aparición falla la prueba. | `SEC-001-002`, `SEC-001-003`, `SEC-001-008`, `SEC-001-019`, `SEC-001-025`, `SEC-001-028` | `NFR-001-006`, `NFR-001-009`; `AC-001-018`, `AC-001-019`, `AC-001-029`, `AC-001-030`, `AC-001-031` |
| `SEC-TEST-001-004` | Enviar request ID de 129 o más caracteres: se descarta íntegro, se genera otro válido y no aparece el original en logs ni respuesta. | `SEC-001-006`, `SEC-001-007` | `REQ-001-008`; `AC-001-016` |
| `SEC-TEST-001-005` | Enviar request ID con CR, LF, tabulador, Unicode fuera del contrato o intento de inyección: no crea líneas/campos y se reemplaza por un ID válido. | `SEC-001-006`, `SEC-001-007` | `REQ-001-008`, `REQ-001-009`; `AC-001-016`, `AC-001-017` |
| `SEC-TEST-001-006` | Intentar emitir URL cruda, `user_id`, `tenant_id` o `request_id` como dimensión: la validación rechaza la serie. | `SEC-001-011`, `SEC-001-012` | `NFR-001-014`; `AC-001-036` |
| `SEC-TEST-001-007` | Consultar métricas desde un contexto no autorizado: el acceso se deniega sin entregar series ni metadata. | `SEC-001-013`, `SEC-001-029` | `REQ-001-018`; `AC-001-034` |
| `SEC-TEST-001-008` | Derribar una dependencia obligatoria: readiness es no satisfactoria y no enumera la dependencia; health continúa indicando vida. | `SEC-001-004`, `SEC-001-005` | `REQ-001-005`, `REQ-001-006`; `AC-001-011` |
| `SEC-TEST-001-009` | Saturar un pool hasta espera y timeout: se respetan límite y timeout, aparece saturación en métricas y no se reporta éxito falso. | `SEC-001-015`, `SEC-001-016` | `REQ-001-018`, `REQ-001-020`; `AC-001-035`, `AC-001-039` |
| `SEC-TEST-001-010` | Generar tormenta de logs y errores: el volumen se reduce según política, el servicio conserva límites y los conteos observado/emitido/agregado/suprimido son conciliables. | `SEC-001-009`, `SEC-001-015` | `REQ-001-021`; `AC-001-040` |
| `SEC-TEST-001-011` | Inyectar eventos obligatorios de seguridad/auditoría durante saturación o fallo de salida: quedan entregados o pendientes, o existe señal y contador explícitos; nunca desaparecen sin evidencia. | `SEC-001-010` | `REQ-001-021`; `AC-001-040` |
| `SEC-TEST-001-012` | Intentar iniciar una prueba de carga sin autorización o contra destino no permitido/producción: no comienza y registra el rechazo sin secretos. | `SEC-001-017`, `SEC-001-029` | `REQ-001-019`; `AC-001-037` |
| `SEC-TEST-001-013` | Enviar preflight/request desde origen, método o header no permitido y probar origen comodín con credenciales: se rechaza la combinación insegura. | `SEC-001-026` | `REQ-001-001`, `REQ-001-002`; `AC-001-001`, `AC-001-002` |
| `SEC-TEST-001-014` | Evaluar una dependencia, imagen, dependencia UI, addon, fuente, icono o recurso visual con vulnerabilidad crítica o alta explotable: el gate bloquea la entrega sin aceptación implícita. | `SEC-001-022` | `REQ-001-023`, `REQ-001-025`; `NFR-001-009`; `AC-001-032`, `AC-001-044`, `AC-001-049` |
| `SEC-TEST-001-015` | Ejecutar CI desde un pull request/contexto no confiable e intentar leer secretos, escribir en repositorio, publicar artefactos o evidencia visual, o invocar indirectamente un proceso privilegiado de publicación o promoción: todos los intentos se deniegan. | `SEC-001-020`, `SEC-001-024` | `REQ-001-025`, `REQ-001-027`; `NFR-001-002`, `NFR-001-009`, `NFR-001-020`, `NFR-001-021`; `AC-001-020`, `AC-001-031`, `AC-001-049`, `AC-001-051` |
| `SEC-TEST-001-016` | Inspeccionar o ejecutar un contenedor configurado como root, privilegiado o con capacidades innecesarias: la verificación falla y el artefacto no es entregable. | `SEC-001-025` | `NFR-001-008`, `NFR-001-009`; `AC-001-029`, `AC-001-032` |
| `SEC-TEST-001-017` | Sembrar nombres, IDs, credenciales o marcadores con apariencia de datos reales en un reporte de rendimiento: la sanitización o gate de datos sintéticos bloquea su publicación. | `SEC-001-018`, `SEC-001-019`, `SEC-001-031` | `REQ-001-019`, `NFR-001-015`; `AC-001-037` |
| `SEC-TEST-001-018` | Producir métricas de CPU o memoria constantes, ausentes, no finitas o con unidad inválida bajo carga variable: se invalida la conclusión del baseline. | `SEC-001-014` | `REQ-001-018`, `NFR-001-015`; `AC-001-034`, `AC-001-037` |
| `SEC-TEST-001-019` | Alterar o sustituir un artefacto, snapshot, diff o baseline respecto de la revisión y ejecución declaradas: la comprobación de integridad y vínculo falla y bloquea su uso. | `SEC-001-023` | `REQ-001-010`, `REQ-001-014`, `REQ-001-027`; `NFR-001-021`; `AC-001-020`, `AC-001-029`, `AC-001-051` |
| `SEC-TEST-001-020` | Añadir a OpenAPI una operación divergente o ejemplo con secreto/dato real sintético: el diff o gate de contenido falla. | `SEC-001-028` | `REQ-001-016`; `AC-001-030` |
| `SEC-TEST-001-021` | Emitir diagnóstico frontend con cuerpo, token, URL con query o detalle interno sintético en build de producción: el control falla y ninguna consola no autorizada lo reproduce. | `SEC-001-027` | `NFR-001-006`, `NFR-001-012`; `AC-001-019` |
| `SEC-TEST-001-022` | Introducir una ruta de login, sesión, usuario, tenant, tabla/RLS tenant o dato de negocio: la prueba de alcance falla y obliga al control de cambios antes de continuar. | `SEC-001-032` | `REQ-001-001`, `REQ-001-002`; `AC-001-001`, `AC-001-002` |
| `SEC-TEST-001-023` | Intentar acceder al catálogo desde un contexto no autorizado o incluirlo accidentalmente en un artefacto productivo: el acceso se deniega y la verificación del artefacto falla antes de entrega. | `SEC-001-029`, `SEC-001-030`, `SEC-001-033` | `REQ-001-025`; `NFR-001-020`; `AC-001-049` |
| `SEC-TEST-001-024` | Sembrar centinelas sintéticos de secretos, URLs internas, identificadores y configuración sensible en ejemplos, contenido renderizado, snapshots, diffs y metadatos: el control automático detecta los soportes analizables y bloquea su conservación o publicación; la evidencia incluye revisión documentada completa de las imágenes que no puedan analizarse totalmente, y cualquier centinela encontrado bloquea el resultado. | `SEC-001-019`, `SEC-001-033` | `REQ-001-025`, `REQ-001-027`; `NFR-001-020`, `NFR-001-021`; `AC-001-049`, `AC-001-051` |
| `SEC-TEST-001-025` | Ejecutar únicamente en un sandbox sintético, sin secretos ni credenciales reales, un ejemplo o addon hostil que intenta leer, escribir, acceder a red o publicar fuera de su alcance: todos los intentos se deniegan y ningún proceso privilegiado promueve el resultado. | `SEC-001-020`, `SEC-001-024`, `SEC-001-033` | `REQ-001-025`; `NFR-001-020`; `AC-001-049` |
| `SEC-TEST-001-026` | Introducir HTML o SVG activo, o un recurso externo no autorizado, que intenta ejecutar código o transmitir información: el contenido se rechaza o neutraliza, no se produce ejecución ni transmisión y el control falla. | `SEC-001-034` | `REQ-001-022`, `REQ-001-023`, `REQ-001-024`, `REQ-001-025`; `NFR-001-018`, `NFR-001-019`, `NFR-001-020`; `AC-001-043`, `AC-001-044`, `AC-001-045`, `AC-001-046`, `AC-001-047`, `AC-001-048`, `AC-001-049` |
| `SEC-TEST-001-027` | Mantener una captura coincidente con el baseline mientras existe un fallo de seguridad, o intentar actualizar, publicar o promover un baseline sin revisión vinculada: el resultado global falla y la actualización o promoción se deniega. | `SEC-001-023`, `SEC-001-035` | `REQ-001-027`; `NFR-001-018`, `NFR-001-021`; `AC-001-046`, `AC-001-051`, `AC-001-052` |
| `SEC-TEST-001-028` | Introducir una divergencia respecto de `ui-design.md` aprobado o una excepción de reutilización sin necesidad, justificación, alcance o criterio de eliminación/incorporación: la comprobación mecánica aplicable o la revisión estructural documentada rechaza el cambio. | `SEC-001-036` | `REQ-001-022`, `REQ-001-023`, `REQ-001-024`, `REQ-001-025`, `REQ-001-026`, `REQ-001-027`, `REQ-001-028`; `NFR-001-018`, `NFR-001-019`, `NFR-001-020`, `NFR-001-021`; `AC-001-042`, `AC-001-043`, `AC-001-044`, `AC-001-045`, `AC-001-046`, `AC-001-047`, `AC-001-048`, `AC-001-049`, `AC-001-050`, `AC-001-051`, `AC-001-052` |
| `SEC-TEST-001-029` | Presentar una dependencia, acción de CI, imagen, dependencia UI, addon, fuente, icono u otro recurso visual que (a) no figure en el inventario, (b) declare una procedencia ausente o no verificable, (c) use una versión o referencia flotante donde el flujo de entrega exige fijación inmutable verificable, o (d) se haya actualizado sin la revisión requerida: cada caso hace fallar el gate correspondiente y bloquea la entrega. No se admite aceptación implícita ni excepción por tratarse de una herramienta de desarrollo, prueba o catálogo. | `SEC-001-021` | `REQ-001-010`, `REQ-001-014`, `REQ-001-022`, `REQ-001-023`, `REQ-001-025`; `NFR-001-002`, `NFR-001-008`, `NFR-001-009`, `NFR-001-020`; `AC-001-020`, `AC-001-029`, `AC-001-032`, `AC-001-043`, `AC-001-044`, `AC-001-049` |

`SEC-TEST-001-029` se incorporó el `2026-09-21` para cerrar la única brecha de
cobertura detectada: antes de esta versión, `SEC-001-021` era el único control
sin prueba negativa asociada. `SEC-TEST-001-014` conserva su alcance original de
vulnerabilidad crítica o alta explotable sobre `SEC-001-022`, y
`SEC-TEST-001-019` conserva el suyo de integridad y vínculo de artefactos sobre
`SEC-001-023`. Ninguna prueba existente amplía su mapeo de controles.

## Referencias ASVS

- **Baseline declarado por la Constitución:** OWASP ASVS 5.0.
- `[No determinado]` El repositorio no establece todavía una correspondencia
  exacta y revisada entre los controles de esta spec y requisitos numerados de
  ASVS 5.0. Para no inventar IDs, el plan de seguridad deberá seleccionar y
  revisar el nivel aplicable y producir el mapeo exacto contra la publicación
  oficial antes de implementación.

## Decisiones materiales pendientes para el plan

- Mecanismo de límite de red/plataforma para readiness, métricas y ejecución de
  baselines, incluida la respuesta mínima de health si se expone públicamente.
- Valores y mecanismos concretos de límites, timeouts, backpressure, pools,
  presupuestos agregados y protección ante abuso.
- Política operacional de retención, acceso, capacidad, alertas, responsables,
  runbooks, reintento y degradación de logs y eventos de seguridad/auditoría.
- Mecanismos de referencias inmutables, vínculo e integridad de artefactos,
  análisis de supply chain y gates, sin reducir los resultados exigidos aquí.
- Política concreta de headers, contenido seguro y orígenes CORS permitidos para
  los entornos aprobados.
- Límites de ejecución y acceso del catálogo, almacenamiento de evidencia
  visual y mecanismos de publicación o promoción desde contextos confiables.
- Política de retención, acceso, eliminación y publicación de snapshots,
  baselines, diffs y sus metadatos, sin tratarlos como logs o métricas.
- Inventario, procedencia, aislamiento y política de destinos externos para
  dependencias UI, addons, fuentes, iconos, SVG y otros recursos visuales.
- Clasificación de eventos obligatorios de seguridad de la foundation y mapeo
  exacto y revisado contra OWASP ASVS 5.0.

Ninguna decisión pendiente autoriza acceso público por defecto, uso de datos
reales, pérdida silenciosa ni relajación de un gate constitucional.

## Riesgos aceptados

- Ninguno.
- Responsable: no aplica.
- Fecha de expiración: no aplica.
- Plan de eliminación: no aplica.

No existe aceptación explícita del Product Owner para riesgos de esta spec. Un
riesgo alto o crítico no puede considerarse aceptado por defecto.

## Control de cambios

- **Aprobación original del diseño de seguridad:** estado `Aprobado`, aprobado
  por `Product Owner` el `2026-09-20`, con la referencia
  `Aprobación explícita del diseño de seguridad de SPEC-001 durante la revisión SDD.`
- `security.md` fue reabierto el `2026-09-20` por el impacto del sistema de
  diseño incorporado a `SPEC-001`.
- Esta modificación requiere reaprobación explícita del diseño de seguridad y
  no constituye por sí misma esa reaprobación.
- El diseño de seguridad actualizado fue reaprobado explícitamente por Product
  Owner el `2026-09-20` después de incorporar el sistema de diseño.
- `security.md` fue reabierto el `2026-09-21` por una brecha de cobertura
  detectada durante la revisión independiente del borrador de `plan.md`: la
  matriz de pruebas negativas no cubría `SEC-001-021`, de modo que el control
  de inventario, procedencia, fijación inmutable y revisión de actualizaciones
  carecía de prueba negativa. El borrador de `plan.md` había compensado esa
  brecha añadiendo `SEC-001-021` a `SEC-TEST-001-014` y `SEC-TEST-001-019`, lo
  que alteraba este documento aprobado sin control de cambios; esa alteración
  se revirtió y la brecha se corrige aquí, en su fuente normativa.
- La corrección del `2026-09-21` se limita a incorporar `SEC-TEST-001-029` y a
  registrar este control de cambios. No modifica activos, datos sensibles,
  trust boundaries, amenazas `THREAT-*`, controles `SEC-*`, la tabla de
  autorización, las reglas de logging y métricas, las decisiones materiales
  pendientes ni los riesgos aceptados.
- Esta modificación requiere reaprobación explícita del diseño de seguridad y
  no constituye por sí misma esa reaprobación. La autorización para editar este
  documento tampoco equivale a aprobar su contenido resultante.
- El diseño de seguridad reabierto el `2026-09-21` fue reaprobado explícitamente
  por `Product Owner` el `2026-09-25`. La reaprobación comprende la incorporación
  de `SEC-TEST-001-029`, dedicada a verificar negativamente `SEC-001-021`.

## Aprobación de diseño de seguridad

- **Estado:** `Aprobado`
- **Aprobado por:** `Product Owner`
- **Fecha:** `2026-09-25`
- **Comentario o referencia:** Aprobación explícita del Product Owner del
  `2026-09-25` del diseño de seguridad de SPEC-001, incluida la incorporación
  de `SEC-TEST-001-029` para `SEC-001-021`.

La versión reabierta el `2026-09-21` quedó reaprobada el `2026-09-25`. La
aprobación previa del `2026-09-20` corresponde a la versión anterior, que no
incluía `SEC-TEST-001-029`, y permanece registrada en el control de cambios.

La existencia de este archivo no constituye aprobación. Ningún riesgo de
severidad alta o crítica puede aceptarse implícitamente. Todo riesgo aceptado
requiere responsable, fecha de expiración, justificación y aprobación explícita.
