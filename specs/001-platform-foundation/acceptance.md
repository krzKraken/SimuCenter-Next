# Aceptación de Platform Foundation

Referencia: `SPEC-001`

## Criterios de aceptación

### AC-001-001 — Backend ejecutable

- **Relacionado con:** `REQ-001-001`
- **Given:** un checkout limpio, los prerrequisitos documentados instalados y
  una configuración válida;
- **When:** se ejecuta el procedimiento documentado para iniciar el backend;
- **Then:** el proceso inicia sin autenticación, tenancy ni módulos de negocio
  y quedan disponibles únicamente las capacidades de plataforma aprobadas en
  `SPEC-001`.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-001`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-002 — Frontend ejecutable y alcance de la pantalla inicial

- **Relacionado con:** `REQ-001-002`
- **Given:** un checkout limpio, una configuración válida y el backend
  disponible;
- **When:** se inicia el frontend y se abre la pantalla inicial;
- **Then:** se muestran el nombre o marca de SimuCenter, un mensaje localizado
  y el selector español/inglés, y no se muestran login, navegación de negocio,
  dashboard ni datos simulados.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-002`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-003 — Estado del frontend sin backend

- **Relacionado con:** `REQ-001-002`
- **Given:** el frontend está iniciado y el backend no está disponible;
- **When:** se abre la pantalla inicial o se pierde la conexión con el backend;
- **Then:** la interfaz muestra un estado comprensible en el idioma activo, no
  expone detalles internos de red y mantiene disponible el selector de idioma.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-003`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-004 — Contenido mínimo de la documentación

- **Relacionado con:** `REQ-001-015`
- **Given:** la documentación de Platform Foundation;
- **When:** se revisa contra los requisitos aprobados;
- **Then:** documenta prerrequisitos, configuración externa, arranque de
  backend y frontend, health, readiness, pruebas, controles equivalentes a CI
  y resolución de problemas frecuentes, sin incluir secretos reales.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-004`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-005 — Arranque reproducible desde checkout limpio

- **Relacionado con:** `REQ-001-003`, `REQ-001-015`, `NFR-001-001`,
  `NFR-001-010`
- **Given:** un checkout limpio en un entorno compatible que solo cuenta con
  los prerrequisitos documentados;
- **When:** una persona sigue de principio a fin la documentación de arranque y
  verificación;
- **Then:** backend y frontend quedan operativos y las verificaciones
  documentadas finalizan satisfactoriamente, sin editar código fuente ni usar
  pasos manuales no documentados.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-005`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-006 — Configuración externa válida

- **Relacionado con:** `REQ-001-004`
- **Given:** todos los valores obligatorios se proporcionan mediante las
  fuentes externas documentadas y tienen formato válido;
- **When:** se inicia cada componente;
- **Then:** la configuración es aceptada sin modificar el código fuente y
  ningún secreto operativo está incorporado al repositorio.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-006`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-007 — Configuración obligatoria ausente

- **Relacionado con:** `REQ-001-004`, `NFR-001-006`
- **Given:** falta al menos un valor de configuración declarado obligatorio;
- **When:** se intenta iniciar el componente afectado;
- **Then:** el componente falla cerrado, no se declara listo y emite un
  diagnóstico que identifica la configuración ausente sin revelar valores
  sensibles.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-007`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-008 — Configuración obligatoria inválida

- **Relacionado con:** `REQ-001-004`, `NFR-001-006`
- **Given:** un valor obligatorio tiene un formato inválido según el contrato
  de configuración documentado;
- **When:** se intenta iniciar el componente afectado;
- **Then:** el componente falla cerrado, no se declara listo y emite un
  diagnóstico útil sin reproducir el valor sensible recibido.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-008`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-009 — Health confirma vida del proceso

- **Relacionado con:** `REQ-001-005`
- **Given:** el proceso backend está iniciado;
- **When:** se ejecuta la comprobación de health definida en el contrato;
- **Then:** la respuesta indica de forma inequívoca que el proceso está vivo y
  usa el contrato HTTP documentado sin exponer configuración ni detalles
  internos.
- **Tipo de prueba esperada:** `API`
- **Prueba relacionada:** `TEST-001-009`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-010 — Readiness satisfactoria

- **Relacionado con:** `REQ-001-006`
- **Given:** la aplicación y todas las dependencias declaradas obligatorias
  están disponibles;
- **When:** se ejecuta la comprobación de readiness definida en el contrato;
- **Then:** la respuesta indica que el backend está listo para recibir tráfico
  sin exponer configuración ni detalles internos de las dependencias.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-010`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-011 — Health y readiness ante una dependencia no disponible

- **Relacionado con:** `REQ-001-005`, `REQ-001-006`
- **Given:** el proceso backend sigue vivo y una dependencia declarada
  obligatoria para atender tráfico no está disponible;
- **When:** se ejecutan las comprobaciones de health y readiness;
- **Then:** health continúa indicando que el proceso está vivo y readiness
  devuelve un resultado no satisfactorio sin exponer detalles sensibles.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-011`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-012 — Contrato para errores esperados

- **Relacionado con:** `REQ-001-007`, `NFR-001-006`
- **Given:** una operación HTTP produce un error esperado documentado;
- **When:** el backend devuelve la respuesta de error;
- **Then:** la respuesta contiene el código estable, el mensaje seguro y el
  request ID definidos por el contrato, y no contiene stack trace, secretos ni
  detalles internos de dependencias.
- **Tipo de prueba esperada:** `API`
- **Prueba relacionada:** `TEST-001-012`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-013 — Contrato para errores no controlados

- **Relacionado con:** `REQ-001-007`, `NFR-001-006`
- **Given:** una solicitud provoca de forma controlada una excepción interna no
  manejada por el caso de uso;
- **When:** el backend construye la respuesta pública;
- **Then:** responde con el contrato seguro de error y su request ID, mientras
  oculta stack traces, nombres internos, configuración y datos sensibles.
- **Tipo de prueba esperada:** `SECURITY`
- **Prueba relacionada:** `TEST-001-013`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-014 — Request ID válido recibido

- **Relacionado con:** `REQ-001-008`, `NFR-001-005`
- **Given:** una solicitud contiene un request ID válido según el contrato
  documentado;
- **When:** el backend procesa la solicitud;
- **Then:** la respuesta devuelve el mismo ID y todos los logs estructurados
  del backend asociados a esa solicitud contienen ese valor.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-014`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-015 — Request ID ausente

- **Relacionado con:** `REQ-001-008`, `NFR-001-005`
- **Given:** una solicitud no contiene request ID;
- **When:** el backend procesa la solicitud;
- **Then:** genera un ID válido, lo devuelve en la respuesta y usa el mismo
  valor en todos los logs estructurados asociados a la solicitud.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-015`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-016 — Request ID inválido

- **Relacionado con:** `REQ-001-008`, `NFR-001-005`
- **Given:** una solicitud contiene un request ID que incumple el contrato de
  validez documentado;
- **When:** el backend procesa la solicitud;
- **Then:** descarta el valor recibido, genera un ID válido y usa ese nuevo
  valor tanto en la respuesta como en todos los logs estructurados asociados.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-016`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-017 — Esquema de logs estructurados del backend

- **Relacionado con:** `REQ-001-009`, `NFR-001-005`, `NFR-001-007`
- **Given:** el backend procesa solicitudes que producen eventos en los niveles
  documentados;
- **When:** se capturan sus registros;
- **Then:** cada registro puede validarse contra el esquema documentado,
  contiene timestamp, nivel, evento y componente, e incluye request ID cuando
  pertenece al ciclo de una solicitud.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-017`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-018 — Exclusión de datos sensibles en errores y logs del backend

- **Relacionado con:** `REQ-001-009`, `NFR-001-006`
- **Given:** entradas de prueba contienen contraseñas, tokens, secretos,
  credenciales y cuerpos sensibles sintéticos;
- **When:** el backend produce respuestas de error y logs durante su manejo;
- **Then:** ninguno de esos valores ni stack traces aparece en las respuestas o
  registros capturados.
- **Tipo de prueba esperada:** `SECURITY`
- **Prueba relacionada:** `TEST-001-018`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-019 — Diagnóstico controlado del frontend

- **Relacionado con:** `REQ-001-009`, `NFR-001-006`, `NFR-001-012`
- **Given:** una build de producción del frontend y errores o eventos
  representativos con datos sensibles sintéticos;
- **When:** se ejercita el mecanismo de diagnóstico documentado;
- **Then:** el diagnóstico conserva solo datos permitidos, no expone los datos
  sensibles, no produce emisiones de consola fuera del mecanismo documentado y
  funciona sin proveedor ni envío remoto de telemetría.
- **Tipo de prueba esperada:** `SECURITY`
- **Prueba relacionada:** `TEST-001-019`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-020 — Controles obligatorios de CI

- **Relacionado con:** `REQ-001-010`, `NFR-001-002`
- **Given:** una propuesta de cambio;
- **When:** se ejecuta su pipeline de CI;
- **Then:** se ejecutan los controles aplicables de formato o lint, tipos,
  pruebas automatizadas, build, detección de secretos y validación del contrato
  API, y la integración queda bloqueada si cualquiera falla o no se ejecuta.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-020`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-021 — Base de pruebas automatizadas local y en CI

- **Relacionado con:** `REQ-001-011`
- **Given:** el mismo revisionado de código y la configuración de prueba
  documentada;
- **When:** se ejecuta la suite localmente y en CI;
- **Then:** ambos entornos ejecutan las verificaciones aplicables de smoke,
  health, readiness, errores, request ID, localización, accesibilidad y
  compatibilidad, y reportan por separado pruebas pasadas, fallidas y no
  ejecutadas.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-021`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-022 — Selección automática de español

- **Relacionado con:** `REQ-001-012`, `NFR-001-003`
- **Given:** no existen preferencias de usuario ni tenant, el navegador
  solicita español o una variante regional de español y están disponibles los
  catálogos de la interfaz inicial;
- **When:** se valida la cobertura de catálogos y se abre por primera vez la
  pantalla inicial;
- **Then:** el 100 % de las cadenas estáticas visibles procede de los catálogos
  y tiene entradas en español e inglés, todos los textos visibles aparecen en
  español y no se muestran claves internas de traducción.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-022`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-023 — Selección automática de inglés

- **Relacionado con:** `REQ-001-012`, `NFR-001-003`
- **Given:** no existen preferencias de usuario ni tenant y el navegador
  solicita inglés o una variante regional de inglés;
- **When:** se abre por primera vez la pantalla inicial;
- **Then:** todos los textos visibles, incluidos los estados de error, aparecen
  en inglés y no se muestran claves internas de traducción.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-023`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-024 — Fallback a inglés

- **Relacionado con:** `REQ-001-012`, `NFR-001-003`
- **Given:** el idioma del navegador es no soportado o indeterminado, o se
  simula de forma aislada una traducción no disponible;
- **When:** se muestra la pantalla inicial o un estado de error;
- **Then:** se usa el texto inglés de fallback y no se muestran claves internas
  ni contenido vacío al usuario.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-024`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-025 — Selector español/inglés

- **Relacionado con:** `REQ-001-002`, `REQ-001-012`, `NFR-001-003`
- **Given:** la pantalla inicial está visible en cualquiera de los dos idiomas
  soportados;
- **When:** la persona selecciona el otro idioma mediante el selector;
- **Then:** todos los textos visibles y el estado de disponibilidad del backend
  cambian al idioma elegido sin mostrar claves internas ni contenido de negocio.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-025`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-026 — Accesibilidad automatizada

- **Relacionado con:** `REQ-001-013`, `NFR-001-004`
- **Given:** la pantalla inicial en español e inglés, tanto con backend
  disponible como no disponible;
- **When:** se ejecuta la revisión automatizada de los criterios aplicables de
  WCAG 2.2 AA;
- **Then:** la revisión no reporta violaciones en ninguno de los estados e
  idiomas evaluados.
- **Tipo de prueba esperada:** `ACCESSIBILITY`
- **Prueba relacionada:** `TEST-001-026`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-027 — Revisión manual de teclado y foco

- **Relacionado con:** `REQ-001-002`, `REQ-001-013`, `NFR-001-004`
- **Given:** la pantalla inicial en cada estado e idioma cubierto por
  `AC-001-026`;
- **When:** una persona completa la revisión manual usando solo teclado;
- **Then:** alcanza y opera todos los controles interactivos, cada control tiene
  nombre accesible, el foco permanece visible, avanza en el orden documentado y
  no queda atrapado en ningún elemento.
- **Tipo de prueba esperada:** `ACCESSIBILITY`
- **Prueba relacionada:** `TEST-001-027`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-028 — Matriz de navegadores soportados

- **Relacionado con:** `REQ-001-011`, `NFR-001-011`
- **Given:** la versión estable actual y anterior de Chrome, Edge, Firefox y
  Safari, y la versión estable actual de Safari en iOS y Chrome en Android al
  momento de la verificación;
- **When:** se ejecutan en cada navegador las verificaciones de carga,
  localización, selector de idioma, indisponibilidad del backend y navegación
  por teclado aplicable;
- **Then:** todas las verificaciones pasan y la evidencia registra navegador,
  versión, plataforma y fecha de ejecución; Internet Explorer y navegadores
  obsoletos no forman parte de la matriz.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-028`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-029 — Construcción y ejecución de contenedores

- **Relacionado con:** `REQ-001-014`, `NFR-001-008`
- **Given:** un checkout limpio y un runtime compatible con estándares de
  contenedores;
- **When:** se construyen y ejecutan los artefactos documentados de backend y
  frontend en local y en CI;
- **Then:** ambos artefactos se construyen, inician y permiten verificar la
  pantalla inicial, health y readiness sin requerir una API propietaria del
  proveedor de despliegue.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-029`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-030 — Contrato OpenAPI contra implementación

- **Relacionado con:** `REQ-001-010`, `REQ-001-016`
- **Given:** el contrato OpenAPI versionado y la implementación backend de la
  misma revisión;
- **When:** se ejecuta la validación automática de contrato y su control de
  diferencias;
- **Then:** el contrato declara su versión, todas las operaciones HTTP
  implementadas en esta foundation están documentadas, las operaciones
  documentadas existen, los errores cumplen el contrato aprobado y cualquier
  diferencia no aprobada hace fallar el control.
- **Tipo de prueba esperada:** `API`
- **Prueba relacionada:** `TEST-001-030`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-031 — Detección de secretos

- **Relacionado con:** `REQ-001-010`, `NFR-001-009`
- **Given:** el repositorio sin secretos reales y una entrada sintética
  controlada que coincide con la política de detección;
- **When:** se ejecuta el control de secretos;
- **Then:** el repositorio limpio supera el control, la entrada sintética es
  detectada y una detección no resuelta bloquea la entrega.
- **Tipo de prueba esperada:** `SECURITY`
- **Prueba relacionada:** `TEST-001-031`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-032 — Vulnerabilidades bloqueantes

- **Relacionado con:** `REQ-001-010`, `NFR-001-009`
- **Given:** los artefactos y dependencias del proyecto y un resultado de
  análisis que identifica una vulnerabilidad crítica o alta explotable;
- **When:** se evalúa el gate de seguridad de la entrega;
- **Then:** la entrega queda bloqueada mientras el hallazgo siga explotable y
  no existe una excepción explícita conforme a la Constitución; sin hallazgos
  bloqueantes, el control finaliza satisfactoriamente.
- **Tipo de prueba esperada:** `SECURITY`
- **Prueba relacionada:** `TEST-001-032`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-033 — Solicitudes sucesivas entre réplicas

- **Relacionado con:** `REQ-001-017`, `NFR-001-013`
- **Given:** al menos dos réplicas equivalentes de un componente, sin afinidad
  obligatoria, y un conjunto de solicitudes ordinarias cuyo resultado esperado
  está documentado;
- **When:** las solicitudes sucesivas se dirigen alternadamente a réplicas
  diferentes y una caché local se inicia vacía o se descarta entre solicitudes;
- **Then:** cada solicitud produce el resultado correcto sin depender de
  memoria mutable de una réplica anterior y cualquier estado compartido usado
  procede del mecanismo externo documentado.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-033`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-034 — Métricas HTTP y de recursos válidas

- **Relacionado con:** `REQ-001-018`
- **Given:** una ejecución controlada con solicitudes satisfactorias y fallidas
  y con variaciones observables de carga y consumo de recursos, que incluye una
  operación con persistencia cuando la foundation disponga de ella;
- **When:** se capturan las métricas operativas durante toda la ejecución;
- **Then:** se reportan cantidad de requests, duración, errores, throughput,
  CPU y memoria con unidades y valores válidos, y la verificación falla si una
  métrica requerida está ausente, permanece constante pese a la variación
  ejercida o contiene valores inválidos. Cuando exista persistencia, también se
  reportan por request la cantidad de operaciones o consultas, su duración
  acumulada y su duración máxima individual, sin parámetros SQL ni datos
  sensibles. Si `SPEC-001` no incorpora persistencia, la evidencia registra que
  esta parte no aplica todavía y que será obligatoria desde la primera spec que
  la introduzca.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-034`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-035 — Métricas de pools y espera

- **Relacionado con:** `REQ-001-018`
- **Given:** el inventario de dependencias declara cuáles usan un pool y sus
  límites configurados;
- **When:** una carga controlada utiliza y alcanza la capacidad de cada pool
  declarado y se observan sus métricas;
- **Then:** las métricas muestran capacidad, uso, saturación y tiempo de espera
  con valores válidos; si el inventario no contiene dependencias con pool, la
  evidencia registra explícitamente que el criterio no aplica y por qué.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-035`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-036 — Dimensiones acotadas de métricas

- **Relacionado con:** `REQ-001-021`, `NFR-001-014`
- **Given:** solicitudes con distintos request ID, usuarios o tenants
  sintéticos, parámetros y valores variables en sus URL;
- **When:** se inspeccionan las series y dimensiones emitidas para las métricas
  HTTP;
- **Then:** las rutas aparecen como plantillas y ninguna dimensión contiene
  `request_id`, `user_id`, `tenant_id` o una URL cruda; una emisión con
  cualquiera de esos valores hace fallar la verificación.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-036`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-037 — Baseline reproducible de rendimiento

- **Relacionado con:** `REQ-001-019`, `NFR-001-015`
- **Given:** una revisión de código identificada y un entorno de medición
  preparado según instrucciones versionadas;
- **When:** se ejecuta dos veces el procedimiento de baseline con los mismos
  parámetros documentados;
- **Then:** cada ejecución produce un reporte que registra revisión, entorno y
  recursos, duración, concurrencia, think time, mezcla de operaciones,
  throughput, p50, p95, p99, errores, CPU, memoria y métricas aplicables de
  pools y dependencias, y ninguna conclusión usa una métrica requerida ausente,
  constante o inválida. Cuando la mezcla incluya persistencia, el reporte
  registra por request la cantidad de operaciones o consultas, su duración
  acumulada y su duración máxima individual, sin parámetros SQL ni datos
  sensibles. Si no existe persistencia en `SPEC-001`, el reporte registra que
  esta parte no aplica todavía y que será obligatoria desde la primera spec que
  la introduzca.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-037`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-038 — Presupuesto agregado al variar procesos y réplicas

- **Relacionado con:** `REQ-001-020`, `NFR-001-016`
- **Given:** límites configurados por proceso o réplica y un presupuesto total
  documentado para cada recurso o conexión compartida aplicable;
- **When:** se evalúan configuraciones con distintas cantidades de workers y
  réplicas;
- **Then:** el cálculo reproducible muestra el consumo agregado esperado para
  cada configuración y rechaza cualquier combinación que exceda el presupuesto
  total o multiplique conexiones o recursos sin un límite explícito.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-038`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-039 — Comportamiento ante saturación

- **Relacionado con:** `REQ-001-020`, `NFR-001-016`
- **Given:** una dependencia o recurso con límite y timeout configurables y un
  comportamiento de saturación documentado;
- **When:** una carga controlada mantiene el recurso en su límite y provoca
  espera y timeout;
- **Then:** la espera, el timeout, la respuesta de la aplicación y las métricas
  coinciden con lo documentado, no exceden los límites configurados y la
  aplicación no oculta la saturación como una operación satisfactoria.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-039`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-040 — Logging bajo alto volumen

- **Relacionado con:** `REQ-001-021`
- **Given:** una carga controlada con requests exitosos y una tormenta de
  errores con request ID cuando existe contexto, junto con una configuración
  documentada de niveles, rate limiting, sampling o agregación;
- **When:** el volumen activa la reducción, el rate limiting, el sampling o la
  agregación de eventos informativos exitosos y errores;
- **Then:** el sistema no depende de un log informativo por cada request
  exitoso, aplica la configuración documentada y ningún error se pierde
  silenciosamente. La evidencia permite conciliar el total de errores
  observados con las cantidades emitidas individualmente, agregadas o
  suprimidas, conserva muestras representativas con request ID y demuestra que
  los eventos obligatorios de seguridad o auditoría no se descartan
  silenciosamente conforme a la política aplicable.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-040`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-041 — Health no demuestra capacidad de negocio

- **Relacionado con:** `REQ-001-019`, `NFR-001-017`
- **Given:** una ejecución en la que health y readiness responden
  satisfactoriamente sin ejecutar journeys u operaciones de negocio;
- **When:** se genera o revisa el reporte de capacidad;
- **Then:** el resultado de health y readiness se identifica solo como
  comprobación operativa y no se presenta como throughput, latencia ni
  capacidad demostrada de journeys de negocio; la ausencia de una carga de
  negocio reproducible impide esa conclusión.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-041`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-042 — Diseño visual versionado y aprobado antes del plan

- **Relacionado con:** `REQ-001-028`
- **Given:** la revisión de requisitos aprobada y la evaluación de elegibilidad
  para redactar `plan.md`;
- **When:** se revisa el artefacto
  `specs/001-platform-foundation/ui-design.md` de la misma revisión;
- **Then:** el archivo existe, declara su versión, define la dirección visual
  inicial, valores concretos de tokens, tipografía, colores y usos semánticos,
  espaciado y layout, anatomía, variantes y estados de los componentes mínimos,
  comportamiento responsive, reglas de teclado, foco e interacción y la
  relación trazable entre diseño, catálogo y componentes, manteniendo
  independencia de frameworks, librerías y herramientas concretas; además
  registra aprobación explícita del Product Owner. Si falta el archivo, algún
  contenido mínimo, su versión o la aprobación, la spec no queda habilitada
  para redactar `plan.md`.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-042`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-043 — Inventario y contrato de design tokens

- **Relacionado con:** `REQ-001-022`, `REQ-001-028`, `NFR-001-020`
- **Given:** el sistema visual y `ui-design.md` versionados;
- **When:** se valida el inventario de tokens contra las categorías obligatorias
  de `SPEC-001`;
- **Then:** el inventario cubre colores semánticos, tipografía, espaciado,
  tamaños, bordes, radios, sombras, breakpoints y capas o elevación; para cada
  categoría declara valores concretos, semántica, aplicabilidad y versión, o
  justifica expresamente su no aplicabilidad, y ninguna definición depende de
  nombres o contratos propios de un framework o librería.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-043`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-044 — Componentes mínimos compartidos en la interfaz inicial

- **Relacionado con:** `REQ-001-023`, `NFR-001-020`
- **Given:** la base de componentes y la interfaz inicial construidas desde la
  misma revisión;
- **When:** se inspeccionan las dependencias visuales y se ejercita la interfaz
  inicial;
- **Then:** existen botón, selector de idioma, indicador de estado, mensaje o
  alerta y primitivas de layout como componentes compartidos; todos consumen
  los tokens compartidos y la interfaz inicial usa esos componentes cuando la
  capacidad correspondiente está presente.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-044`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-045 — Matriz explícita de estados y ejemplos

- **Relacionado con:** `REQ-001-024`, `NFR-001-020`
- **Given:** todos los componentes mínimos y sus variantes documentadas;
- **When:** se valida su matriz de estados y se ejecutan los ejemplos del
  catálogo;
- **Then:** cada componente y variante marca expresamente como aplicable o no
  aplicable cada estado `default`, `hover`, `focus-visible`, `active`,
  `disabled`, `loading`, `success`, `warning` y `error`, y existe un ejemplo
  verificable para cada estado aplicable; una omisión implícita hace fallar la
  validación.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-045`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-046 — Accesibilidad de componentes, variantes y estados

- **Relacionado con:** `REQ-001-023`, `REQ-001-024`, `NFR-001-018`
- **Given:** cada componente interactivo en todas sus variantes y estados
  aplicables según `AC-001-045`;
- **When:** se ejecutan la revisión automatizada aplicable y una revisión manual
  dirigida mediante teclado;
- **Then:** cada caso puede operarse por teclado, conserva el orden de foco
  esperado y foco visible, expone semántica y nombre accesible correctos y
  cumple el contraste aplicable de WCAG 2.2 AA, sin que el resultado de una
  variante o estado se extrapole a los demás.
- **Tipo de prueba esperada:** `ACCESSIBILITY`
- **Prueba relacionada:** `TEST-001-046`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-047 — Comportamiento responsive en breakpoints

- **Relacionado con:** `REQ-001-022`, `REQ-001-023`, `REQ-001-024`,
  `REQ-001-028`, `NFR-001-019`
- **Given:** los breakpoints aprobados en `ui-design.md` y los componentes,
  variantes y estados aplicables;
- **When:** se ejercitan en cada breakpoint definido y en tamaños intermedios
  representativos;
- **Then:** no se pierde contenido, no existen solapamientos ni
  desbordamientos que impidan el uso, se conserva la operabilidad y el orden de
  foco no cambia de forma distinta a la documentada.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-047`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-048 — Consistencia del sistema visual entre navegadores

- **Relacionado con:** `REQ-001-023`, `REQ-001-024`, `NFR-001-019`
- **Given:** la matriz de navegadores ya definida por `NFR-001-011` y verificada
  por `AC-001-028`, junto con los escenarios de componentes, estados y
  breakpoints de `AC-001-045` a `AC-001-047`;
- **When:** se ejecuta en esa misma matriz una selección representativa y
  documentada de dichos escenarios;
- **Then:** los componentes conservan contenido, jerarquía, semántica,
  interacción, estados y comportamiento responsive equivalentes, y la
  evidencia complementa la de `AC-001-028` sin redefinir navegadores o
  versiones soportadas.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-048`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-049 — Catálogo ejecutable, completo y validado

- **Relacionado con:** `REQ-001-025`, `NFR-001-020`
- **Given:** un checkout limpio y la misma revisión del sistema visual y sus
  componentes;
- **When:** se inicia el catálogo mediante el procedimiento local documentado y
  se ejecuta su validación equivalente en CI;
- **Then:** el catálogo está disponible localmente, su validación pasa en CI,
  declara su versión y contiene todas las categorías de tokens, componentes
  mínimos, variantes, matrices de estados y ejemplos permitidos; todas sus
  referencias resuelven a contratos existentes y no contiene navegación,
  journeys ni datos de negocio.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-049`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-050 — Duplicación y excepciones del sistema compartido

- **Relacionado con:** `REQ-001-026`
- **Given:** el inventario de tokens, componentes, estilos y capacidades
  compartidas, una duplicación detectable mecánicamente, una duplicación
  conceptual o de interacción y una excepción candidata;
- **When:** se ejecuta el control automatizado de definiciones prohibidas,
  imports, tokens o primitivas paralelas y se realiza la revisión estructural
  documentada;
- **Then:** las duplicaciones detectables mecánicamente hacen fallar el control
  automatizado; las duplicaciones conceptuales o de interacción requieren una
  revisión estructural documentada; y una excepción solo se acepta si registra
  necesidad, justificación, alcance y criterio para eliminarla o incorporarla
  al sistema compartido.
- **Tipo de prueba esperada:** `INTEGRATION`
- **Prueba relacionada:** `TEST-001-050`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-051 — Regresión visual reproducible y revisada

- **Relacionado con:** `REQ-001-027`, `NFR-001-021`
- **Given:** baselines visuales versionados y vinculados a una revisión de
  código, con cobertura declarada de componentes, variantes, estados y
  breakpoints aplicables;
- **When:** se ejecuta dos veces el procedimiento reproducible y se introduce
  una diferencia visual controlada;
- **Then:** las ejecuciones sin cambios producen resultados consistentes, la
  diferencia controlada es detectada, todo cambio esperado requiere revisión
  explícita y la evidencia conserva el vínculo con la revisión de código; una
  actualización del baseline sin esa revisión hace fallar el control.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-051`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

### AC-001-052 — La coincidencia visual no oculta otros fallos

- **Relacionado con:** `REQ-001-027`, `NFR-001-018`, `NFR-001-021`
- **Given:** un componente cuya captura coincide con el baseline y fallos
  controlados independientes de comportamiento funcional, operación por
  teclado y accesibilidad;
- **When:** se ejecutan conjuntamente la regresión visual y las verificaciones
  funcionales y de accesibilidad;
- **Then:** cada fallo no visual hace que la verificación global resulte no
  satisfactoria, aunque la comparación visual pase, y la evidencia informa por
  separado los resultados visuales, funcionales, de teclado y de accesibilidad.
- **Tipo de prueba esperada:** `E2E`
- **Prueba relacionada:** `TEST-001-052`
- **Estado de prueba:** `Pendiente de implementación`
- **Evidencia final:** `Pendiente`

## Matriz de trazabilidad

| Criterio AC-* | Requisito o NFR | Prueba | Estado | Evidencia |
|---|---|---|---|---|
| `AC-001-001` | `REQ-001-001` | `TEST-001-001` | Pendiente de implementación | Pendiente |
| `AC-001-002` | `REQ-001-002` | `TEST-001-002` | Pendiente de implementación | Pendiente |
| `AC-001-003` | `REQ-001-002` | `TEST-001-003` | Pendiente de implementación | Pendiente |
| `AC-001-004` | `REQ-001-015` | `TEST-001-004` | Pendiente de implementación | Pendiente |
| `AC-001-005` | `REQ-001-003`, `REQ-001-015`, `NFR-001-001`, `NFR-001-010` | `TEST-001-005` | Pendiente de implementación | Pendiente |
| `AC-001-006` | `REQ-001-004` | `TEST-001-006` | Pendiente de implementación | Pendiente |
| `AC-001-007` | `REQ-001-004`, `NFR-001-006` | `TEST-001-007` | Pendiente de implementación | Pendiente |
| `AC-001-008` | `REQ-001-004`, `NFR-001-006` | `TEST-001-008` | Pendiente de implementación | Pendiente |
| `AC-001-009` | `REQ-001-005` | `TEST-001-009` | Pendiente de implementación | Pendiente |
| `AC-001-010` | `REQ-001-006` | `TEST-001-010` | Pendiente de implementación | Pendiente |
| `AC-001-011` | `REQ-001-005`, `REQ-001-006` | `TEST-001-011` | Pendiente de implementación | Pendiente |
| `AC-001-012` | `REQ-001-007`, `NFR-001-006` | `TEST-001-012` | Pendiente de implementación | Pendiente |
| `AC-001-013` | `REQ-001-007`, `NFR-001-006` | `TEST-001-013` | Pendiente de implementación | Pendiente |
| `AC-001-014` | `REQ-001-008`, `NFR-001-005` | `TEST-001-014` | Pendiente de implementación | Pendiente |
| `AC-001-015` | `REQ-001-008`, `NFR-001-005` | `TEST-001-015` | Pendiente de implementación | Pendiente |
| `AC-001-016` | `REQ-001-008`, `NFR-001-005` | `TEST-001-016` | Pendiente de implementación | Pendiente |
| `AC-001-017` | `REQ-001-009`, `NFR-001-005`, `NFR-001-007` | `TEST-001-017` | Pendiente de implementación | Pendiente |
| `AC-001-018` | `REQ-001-009`, `NFR-001-006` | `TEST-001-018` | Pendiente de implementación | Pendiente |
| `AC-001-019` | `REQ-001-009`, `NFR-001-006`, `NFR-001-012` | `TEST-001-019` | Pendiente de implementación | Pendiente |
| `AC-001-020` | `REQ-001-010`, `NFR-001-002` | `TEST-001-020` | Pendiente de implementación | Pendiente |
| `AC-001-021` | `REQ-001-011` | `TEST-001-021` | Pendiente de implementación | Pendiente |
| `AC-001-022` | `REQ-001-012`, `NFR-001-003` | `TEST-001-022` | Pendiente de implementación | Pendiente |
| `AC-001-023` | `REQ-001-012`, `NFR-001-003` | `TEST-001-023` | Pendiente de implementación | Pendiente |
| `AC-001-024` | `REQ-001-012`, `NFR-001-003` | `TEST-001-024` | Pendiente de implementación | Pendiente |
| `AC-001-025` | `REQ-001-002`, `REQ-001-012`, `NFR-001-003` | `TEST-001-025` | Pendiente de implementación | Pendiente |
| `AC-001-026` | `REQ-001-013`, `NFR-001-004` | `TEST-001-026` | Pendiente de implementación | Pendiente |
| `AC-001-027` | `REQ-001-002`, `REQ-001-013`, `NFR-001-004` | `TEST-001-027` | Pendiente de implementación | Pendiente |
| `AC-001-028` | `REQ-001-011`, `NFR-001-011` | `TEST-001-028` | Pendiente de implementación | Pendiente |
| `AC-001-029` | `REQ-001-014`, `NFR-001-008` | `TEST-001-029` | Pendiente de implementación | Pendiente |
| `AC-001-030` | `REQ-001-010`, `REQ-001-016` | `TEST-001-030` | Pendiente de implementación | Pendiente |
| `AC-001-031` | `REQ-001-010`, `NFR-001-009` | `TEST-001-031` | Pendiente de implementación | Pendiente |
| `AC-001-032` | `REQ-001-010`, `NFR-001-009` | `TEST-001-032` | Pendiente de implementación | Pendiente |
| `AC-001-033` | `REQ-001-017`, `NFR-001-013` | `TEST-001-033` | Pendiente de implementación | Pendiente |
| `AC-001-034` | `REQ-001-018` | `TEST-001-034` | Pendiente de implementación | Pendiente |
| `AC-001-035` | `REQ-001-018` | `TEST-001-035` | Pendiente de implementación | Pendiente |
| `AC-001-036` | `REQ-001-021`, `NFR-001-014` | `TEST-001-036` | Pendiente de implementación | Pendiente |
| `AC-001-037` | `REQ-001-019`, `NFR-001-015` | `TEST-001-037` | Pendiente de implementación | Pendiente |
| `AC-001-038` | `REQ-001-020`, `NFR-001-016` | `TEST-001-038` | Pendiente de implementación | Pendiente |
| `AC-001-039` | `REQ-001-020`, `NFR-001-016` | `TEST-001-039` | Pendiente de implementación | Pendiente |
| `AC-001-040` | `REQ-001-021` | `TEST-001-040` | Pendiente de implementación | Pendiente |
| `AC-001-041` | `REQ-001-019`, `NFR-001-017` | `TEST-001-041` | Pendiente de implementación | Pendiente |
| `AC-001-042` | `REQ-001-028` | `TEST-001-042` | Pendiente de implementación | Pendiente |
| `AC-001-043` | `REQ-001-022`, `REQ-001-028`, `NFR-001-020` | `TEST-001-043` | Pendiente de implementación | Pendiente |
| `AC-001-044` | `REQ-001-023`, `NFR-001-020` | `TEST-001-044` | Pendiente de implementación | Pendiente |
| `AC-001-045` | `REQ-001-024`, `NFR-001-020` | `TEST-001-045` | Pendiente de implementación | Pendiente |
| `AC-001-046` | `REQ-001-023`, `REQ-001-024`, `NFR-001-018` | `TEST-001-046` | Pendiente de implementación | Pendiente |
| `AC-001-047` | `REQ-001-022`, `REQ-001-023`, `REQ-001-024`, `REQ-001-028`, `NFR-001-019` | `TEST-001-047` | Pendiente de implementación | Pendiente |
| `AC-001-048` | `REQ-001-023`, `REQ-001-024`, `NFR-001-019` | `TEST-001-048` | Pendiente de implementación | Pendiente |
| `AC-001-049` | `REQ-001-025`, `NFR-001-020` | `TEST-001-049` | Pendiente de implementación | Pendiente |
| `AC-001-050` | `REQ-001-026` | `TEST-001-050` | Pendiente de implementación | Pendiente |
| `AC-001-051` | `REQ-001-027`, `NFR-001-021` | `TEST-001-051` | Pendiente de implementación | Pendiente |
| `AC-001-052` | `REQ-001-027`, `NFR-001-018`, `NFR-001-021` | `TEST-001-052` | Pendiente de implementación | Pendiente |

## Control de cambios

- `AC-001-001` a `AC-001-041` fueron aprobados por Product Owner el
  `2026-09-19`.
- `acceptance.md` fue reabierto el `2026-09-20` para cubrir los requisitos del
  sistema de diseño incorporados a `SPEC-001`.
- Esta reapertura no constituye aprobación de `AC-001-042` a `AC-001-052` ni
  reaprobación del conjunto actualizado de criterios.
- El conjunto actualizado de criterios `AC-001-001` a `AC-001-052` fue
  reaprobado explícitamente por Product Owner el `2026-09-20` después de
  incorporar el sistema de diseño.

## Aprobación de criterios de aceptación

- **Estado:** `Aprobado`
- **Aprobado por:** `Product Owner`
- **Fecha:** `2026-09-20`
- **Comentario o referencia:** Reaprobación explícita de los criterios de
  aceptación de SPEC-001 después de incorporar el sistema de diseño.

La existencia de este archivo no constituye aprobación. Todos los criterios
`AC-*` deben ser verificables.
