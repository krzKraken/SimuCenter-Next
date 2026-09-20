# Platform Foundation

## Metadata

- **ID:** `SPEC-001`
- **Nombre:** `Platform Foundation`
- **Estado:** `Design Approved`
- **Responsable:** `Product Owner`
- **Fecha:** `2026-09-19`
- **Última actualización:** `2026-09-20`

## Problema

SimuCenter Next necesita una base ejecutable y verificable antes de incorporar
tenancy, identity o capacidades de negocio. Sin esa base, cada dominio podría
resolver de forma distinta el arranque, la configuración, los errores, la
observabilidad, las pruebas, la internacionalización, la accesibilidad, el
sistema visual, los componentes y las interacciones, repitiendo el acoplamiento
y la deuda observados en el sistema legacy.

La foundation debe demostrar que backend y frontend pueden desarrollarse,
ejecutarse y verificarse de manera reproducible, sin anticipar funcionalidades
ni decisiones técnicas que corresponden a specs, ADR o planes posteriores.

## Objetivo

Definir los cimientos verificables sobre los que se implementarán
posteriormente tenancy, identity y los dominios de negocio, con contratos
básicos de ejecución, configuración, operación, calidad, localización,
accesibilidad y portabilidad, además de una base visual compartida y versionada
que permita construir interfaces futuras de forma consistente sin acoplar la
identidad del producto a una implementación técnica concreta.

## Alcance incluido

- Base ejecutable del backend y frontend, con una pantalla inicial mínima que
  incluya la identidad de SimuCenter.
- Entorno local reproducible.
- Configuración externa sin secretos en el repositorio.
- Health y readiness con semánticas diferenciadas.
- Formato consistente de errores de API.
- Request ID, logging estructurado del backend y diagnóstico controlado del
  frontend.
- Procesamiento stateless compatible con múltiples réplicas.
- Métricas operativas de tráfico, latencia, errores, recursos y dependencias.
- Baseline reproducible de rendimiento y presupuestos agregados de recursos.
- Observabilidad configurable para escenarios de alto volumen.
- CI con controles básicos de calidad y seguridad.
- Base de pruebas automatizadas.
- Internacionalización en español e inglés desde la primera interfaz visible.
- Selector de idioma español/inglés y reglas de selección y fallback definidas.
- Baseline inicial de accesibilidad.
- Sistema visual versionado basado en design tokens semánticos.
- Dirección visual inicial de la aplicación y valores concretos de su primera
  versión definidos en un `ui-design.md` versionado y aprobado explícitamente
  por Product Owner antes de redactar `plan.md`.
- Base compartida de componentes, estados e interacciones para la interfaz
  inicial.
- Catálogo ejecutable y versionado del sistema visual y estrategia verificable
  de regresión visual.
- Portabilidad mediante estándares abiertos y contenedores.
- Documentación mínima para levantar y verificar el proyecto.
- Contrato OpenAPI versionado para las operaciones HTTP incluidas en esta spec.

## Actores

- **Product Owner:** aprueba el alcance y los requisitos de la foundation.
- **Equipo de desarrollo:** levanta, cambia y prueba el proyecto sobre una base
  reproducible.
- **Revisor o mantenedor:** verifica automáticamente la calidad y el contrato
  de los cambios.
- **Operador de plataforma:** distingue si el proceso está vivo y si está listo
  para recibir tráfico, y correlaciona solicitudes mediante request ID.
- **Persona usuaria futura:** recibe desde el inicio una interfaz localizable y
  accesible, aunque esta spec no incorpore flujos de negocio.

## User stories

- Como integrante del equipo de desarrollo, quiero levantar backend y frontend
  desde un checkout limpio siguiendo documentación mantenida para poder empezar
  a trabajar sin reconstruir conocimiento implícito.
- Como revisor, quiero que cada cambio ejecute controles automáticos
  reproducibles para detectar defectos básicos antes de integrarlo.
- Como operador, quiero consultar health y readiness por separado para
  distinguir un proceso vivo de uno preparado para recibir tráfico.
- Como operador o desarrollador, quiero correlacionar respuestas y logs con un
  request ID para investigar fallos sin exponer información sensible.
- Como operador, quiero métricas válidas y un baseline reproducible para
  distinguir capacidad observada de estimaciones y detectar saturación.
- Como responsable de plataforma, quiero presupuestos agregados de recursos y
  procesamiento stateless para escalar réplicas sin multiplicar dependencias
  de forma descontrolada.
- Como persona usuaria, quiero que la primera interfaz sea utilizable en
  español e inglés, permita elegir el idioma y sea operable mediante teclado
  para que la foundation no genere deuda de localización o accesibilidad.
- Como equipo de producto y desarrollo, quiero una base visual y componentes
  compartidos, documentados y verificables para que las interfaces futuras no
  creen estilos, estados o interacciones incompatibles entre sí.
- Como equipo responsable de un dominio futuro, quiero contratos comunes de
  errores, configuración y observabilidad para incorporar capacidades sin
  redefinir la plataforma.

## Requisitos funcionales

- **REQ-001-001:** El repositorio debe proporcionar una aplicación backend
  ejecutable que arranque sin depender de autenticación, tenancy ni módulos de
  negocio y exponga únicamente las capacidades de plataforma incluidas en esta
  spec.
- **REQ-001-002:** El repositorio debe proporcionar una aplicación frontend
  ejecutable que muestre una pantalla mínima con nombre o marca de SimuCenter,
  un mensaje localizado, selector español/inglés y un estado comprensible y
  localizado cuando el backend no esté disponible. Debe ser navegable mediante
  teclado y mostrar foco visible. No debe incluir login, navegación de negocio,
  dashboard ni datos simulados.
- **REQ-001-003:** Debe existir un procedimiento documentado para preparar y
  levantar el entorno local completo desde un checkout limpio, sin editar el
  código fuente ni depender de conocimiento no documentado.
- **REQ-001-004:** La configuración operativa debe recibirse desde fuentes
  externas al código. El sistema debe identificar configuración obligatoria,
  validar valores al arrancar y fallar de forma segura con un mensaje útil que
  no revele secretos.
- **REQ-001-005:** El backend debe ofrecer una comprobación de health que
  indique si el proceso está vivo sin confundir ese resultado con la
  disponibilidad de sus dependencias.
- **REQ-001-006:** El backend debe ofrecer una comprobación de readiness que
  solo resulte satisfactoria cuando la aplicación y las dependencias declaradas
  como obligatorias estén listas para atender tráfico.
- **REQ-001-007:** Todas las respuestas de error de la API deben usar un
  contrato consistente que incluya un código estable, un mensaje seguro para
  el cliente y el request ID, sin devolver stack traces ni detalles internos de
  dependencias.
- **REQ-001-008:** Cada solicitud al backend debe recibir un request ID válido,
  generado cuando no exista uno aceptable, y devolverlo al cliente. El mismo ID
  debe estar disponible para correlacionar los logs producidos por la solicitud.
- **REQ-001-009:** El backend debe emitir logs estructurados con un esquema
  documentado y niveles consistentes. El frontend debe contar con diagnóstico
  controlado de errores y eventos, sin datos sensibles y evitando logs
  innecesarios de consola en producción. Esta spec no exige un proveedor ni el
  envío remoto de telemetría.
- **REQ-001-010:** Cada propuesta de cambio debe activar CI con controles
  básicos aplicables de formato o lint, tipos, pruebas automatizadas, build,
  detección de secretos y validación del contrato API.
- **REQ-001-011:** Debe existir una base de pruebas automatizadas para backend y
  frontend que pueda ejecutarse localmente y en CI, e incluya verificaciones de
  arranque o smoke, health, readiness, errores, request ID, localización y
  accesibilidad, además de compatibilidad con navegadores cuando corresponda.
- **REQ-001-012:** Toda cadena visible de la interfaz inicial debe proceder del
  sistema de internacionalización y tener traducción en español e inglés. Los
  errores visibles para usuarios deben seguir la misma regla. El orden futuro
  de preferencia debe ser usuario, tenant, navegador y fallback. Mientras no
  existan usuario ni tenant, si el navegador solicita español o inglés debe
  usarse ese idioma; para un idioma no soportado o indeterminado debe usarse
  inglés como fallback. La pantalla inicial debe permitir seleccionar
  explícitamente español o inglés.
- **REQ-001-013:** La interfaz inicial debe establecer el baseline de
  accesibilidad del producto: estructura semántica, navegación por teclado,
  foco visible y controles con nombre accesible, conforme a los criterios
  aplicables de WCAG 2.2 AA.
- **REQ-001-014:** Backend y frontend deben poder construirse y ejecutarse en
  contenedores basados en estándares, sin requerir servicios propietarios para
  el flujo local definido por esta spec.
- **REQ-001-015:** Debe existir documentación mantenida que describa
  prerrequisitos, configuración externa, arranque, verificación de health y
  readiness, ejecución de pruebas, controles equivalentes a CI y resolución de
  problemas frecuentes, sin incluir secretos reales.
- **REQ-001-016:** Las operaciones HTTP expuestas por esta foundation deben
  formar parte de un contrato OpenAPI versionado, consistente con el contrato
  de errores y validado automáticamente contra la implementación.
- **REQ-001-017:** El procesamiento de solicitudes ordinarias debe ser
  stateless: su corrección no puede depender de memoria mutable de una réplica.
  El estado que deba compartirse entre réplicas debe externalizarse mediante un
  mecanismo definido posteriormente. Las cachés locales solo pueden actuar
  como optimizaciones prescindibles cuya pérdida no altere la corrección.
- **REQ-001-018:** La foundation debe producir métricas operativas que permitan
  observar cantidad de requests, duración, errores, throughput, CPU y memoria,
  además de saturación y tiempo de espera de pools o dependencias cuando
  existan. Cuando una operación use persistencia, las métricas deben incluir la
  cantidad de operaciones o consultas de persistencia por request, su duración
  acumulada y su duración máxima individual, sin registrar parámetros SQL ni
  datos sensibles. La verificación debe detectar métricas ausentes, constantes
  o inválidas en lugar de aceptarlas como evidencia.
- **REQ-001-019:** Debe poder ejecutarse un baseline reproducible de rendimiento
  cuyo reporte identifique la revisión de código, entorno y recursos, duración,
  concurrencia, think time, mezcla de operaciones, throughput, p50, p95, p99,
  errores, CPU, memoria y las métricas aplicables de pools y dependencias. Si la
  mezcla incluye operaciones con persistencia, el reporte debe incluir por
  request la cantidad de operaciones o consultas, su duración acumulada y su
  duración máxima individual, sin parámetros SQL ni datos sensibles.
- **REQ-001-020:** Los pools y límites de recursos deben ser configurables y
  contar con un presupuesto documentado por proceso o réplica y un presupuesto
  total esperado. La configuración debe impedir que aumentar workers o réplicas
  multiplique conexiones o recursos sin control, y debe documentar timeouts y
  comportamiento ante saturación.
- **REQ-001-021:** La observabilidad de alto volumen no debe depender de emitir
  un log informativo por cada request exitoso. Los niveles, el sampling o la
  agregación deben ser configurables; los logs de errores emitidos deben
  conservar su request ID cuando exista contexto; y ningún error puede perderse
  silenciosamente. Ante una tormenta de errores puede aplicarse rate limiting,
  agregación o sampling documentado. Si se reduce el detalle individual, deben
  conservarse contadores del total observado y de las cantidades emitidas,
  agregadas o suprimidas, además de muestras representativas correlacionables.
  Los eventos obligatorios de seguridad o auditoría no pueden descartarse
  silenciosamente; su política exacta se definirá en `security.md` o specs
  posteriores. Las métricas HTTP deben usar plantillas de rutas y dimensiones
  acotadas.
- **REQ-001-022:** Debe existir un sistema visual versionado basado en design
  tokens semánticos que defina, como mínimo, colores semánticos, tipografía,
  espaciado, tamaños, bordes, radios, sombras, breakpoints y capas o elevación
  cuando apliquen. `SPEC-001` define las categorías, semántica, obligaciones de
  cobertura, versionado y aplicabilidad de los tokens; los valores concretos
  aprobados para la versión inicial del producto deben definirse en
  `specs/001-platform-foundation/ui-design.md`. Ni las categorías ni sus valores
  pueden acoplarse a un framework, librería o tecnología de presentación.
- **REQ-001-023:** Debe existir una base de componentes compartidos utilizada
  por la interfaz inicial que incluya, como mínimo, botón, selector de idioma,
  indicador de estado, mensaje o alerta y primitivas de layout. Cada componente
  debe consumir el sistema de tokens y exponer solo las variantes y usos
  documentados.
- **REQ-001-024:** Cada componente compartido debe declarar cuáles de los
  estados `default`, `hover`, `focus-visible`, `active`, `disabled`, `loading`,
  `success`, `warning` y `error` le aplican, y presentar esos estados de forma
  homogénea y verificable. La no aplicabilidad de un estado debe quedar
  documentada en lugar de resolverse mediante una omisión implícita.
- **REQ-001-025:** Debe existir un catálogo o documentación ejecutable y
  versionada que permita inspeccionar los tokens, componentes, variantes,
  estados, reglas de uso y ejemplos permitidos incluidos en esta spec. El
  catálogo debe poder verificarse localmente y en CI sin convertirse en una
  aplicación o navegación de negocio.
- **REQ-001-026:** Una capacidad visual o de interacción ya cubierta por el
  sistema compartido debe reutilizar su token, primitiva o componente. Se
  prohíben componentes o estilos duplicados para esa capacidad salvo una
  excepción documentada que identifique la necesidad, justificación, alcance y
  criterio para eliminarla o incorporarla al sistema compartido.
- **REQ-001-027:** Debe existir una estrategia automatizable y reproducible de
  regresión visual para los componentes, variantes y estados cubiertos por esta
  spec. Sus diferencias deben revisarse explícitamente; una captura o
  comparación visual complementa y no sustituye pruebas funcionales, de
  interacción ni de accesibilidad.
- **REQ-001-028:** Antes de redactar `plan.md` debe existir
  `specs/001-platform-foundation/ui-design.md` como artefacto versionado y contar
  con aprobación explícita del Product Owner. Debe definir la dirección visual
  inicial aprobada del producto; los valores concretos de los tokens; la
  tipografía; los colores y sus usos semánticos; el espaciado y layout; la
  anatomía, variantes y estados de los componentes mínimos; el comportamiento
  responsive; las reglas de teclado, foco e interacción; y la relación
  trazable entre el diseño, el catálogo ejecutable y los componentes
  implementados. El artefacto debe conservar la independencia respecto de
  frameworks, librerías y herramientas concretas.

## Requisitos no funcionales

- **NFR-001-001:** En un entorno compatible limpio, una persona debe poder
  seguir la documentación de principio a fin y obtener backend y frontend
  operativos, además de una verificación satisfactoria de la foundation, sin
  pasos manuales no documentados.
- **NFR-001-002:** Los controles obligatorios de CI deben ejecutarse para cada
  pull request y bloquear su integración cuando cualquiera falle o no se
  ejecute.
- **NFR-001-003:** El 100 % de las cadenas estáticas visibles de la interfaz
  inicial debe estar definido en catálogos y contar con entradas en español e
  inglés; la verificación debe detectar claves ausentes.
- **NFR-001-004:** La interfaz inicial debe cumplir los criterios aplicables de
  WCAG 2.2 AA, no presentar violaciones en la revisión automatizada configurada
  y superar una revisión manual dirigida de teclado y foco.
- **NFR-001-005:** Todas las respuestas HTTP emitidas por el backend deben
  incluir un request ID y todos sus logs estructurados asociados al ciclo de
  una solicitud deben conservar ese mismo valor.
- **NFR-001-006:** Las verificaciones de errores y logging deben demostrar que
  las respuestas y logs no contienen stack traces, contraseñas, tokens,
  secretos, credenciales ni cuerpos sensibles.
- **NFR-001-007:** Cada registro estructurado del backend debe poder validarse
  contra el esquema documentado e incluir, como mínimo, timestamp, nivel,
  evento y componente; debe incluir request ID cuando exista contexto de
  solicitud.
- **NFR-001-008:** Los artefactos de contenedor de backend y frontend deben
  poder construirse y ejecutarse tanto en el entorno local documentado como en
  CI sin depender de una API propietaria del proveedor de despliegue.
- **NFR-001-009:** El repositorio debe superar los controles configurados de
  secretos y dependencias. Una vulnerabilidad crítica o alta explotable bloquea
  la entrega conforme a la Constitución.
- **NFR-001-010:** La documentación de arranque y verificación debe validarse
  ejecutándola desde un checkout limpio antes de considerar implementada esta
  spec.
- **NFR-001-011:** La compatibilidad del frontend debe verificarse en Chrome,
  Edge, Firefox y Safari para la versión estable actual y la inmediatamente
  anterior, y en Safari para iOS y Chrome para Android para la versión estable
  actual. Internet Explorer y navegadores obsoletos quedan fuera de soporte.
  La evidencia debe proceder de verificaciones ejecutadas, no solo de una
  declaración documental.
- **NFR-001-012:** El diagnóstico del frontend debe excluir datos sensibles,
  evitar logs innecesarios de consola en producción y funcionar sin requerir
  un proveedor ni envío remoto de telemetría.
- **NFR-001-013:** Backend y frontend deben ser compatibles con escalamiento
  horizontal, de modo que dirigir solicitudes sucesivas a réplicas diferentes
  no cambie el resultado correcto de una operación ordinaria por depender de
  memoria mutable local.
- **NFR-001-014:** Las métricas no deben usar dimensiones de alta cardinalidad,
  incluidos `request_id`, `user_id`, `tenant_id` o la URL cruda; las dimensiones
  y plantillas de rutas permitidas deben ser finitas y estar documentadas.
- **NFR-001-015:** La evidencia del baseline debe poder reproducirse a partir
  de las instrucciones y parámetros registrados, y cualquier métrica requerida
  ausente, constante o inválida debe invalidar la conclusión correspondiente.
- **NFR-001-016:** La verificación de pools y recursos debe calcular y contrastar
  el presupuesto agregado esperado al variar la cantidad de workers y réplicas,
  además de comprobar el comportamiento documentado ante saturación.
- **NFR-001-017:** Una comprobación satisfactoria de health o readiness no debe
  presentarse como demostración de capacidad de journeys u operaciones de
  negocio.
- **NFR-001-018:** Todos los componentes interactivos incluidos deben cumplir
  los criterios aplicables de WCAG 2.2 AA y superar verificaciones de operación
  mediante teclado, orden de foco, foco visible, semántica, nombre accesible y
  contraste en cada variante y estado aplicable.
- **NFR-001-019:** Los componentes, primitivas de layout y estados incluidos
  deben conservar contenido, jerarquía, operación y ausencia de desbordamientos
  que impidan el uso en los breakpoints documentados, y mostrar comportamiento
  consistente en toda la matriz de navegadores aprobada por `NFR-001-011`.
- **NFR-001-020:** Una validación reproducible debe comprobar que el catálogo
  contiene todas las categorías mínimas de tokens, todos los componentes
  mínimos, sus variantes, la aplicabilidad de los estados y al menos un ejemplo
  permitido por caso documentado, y debe fallar ante referencias inexistentes,
  cobertura incompleta o versiones incompatibles.
- **NFR-001-021:** La regresión visual debe cubrir, en los breakpoints
  documentados, cada componente y cada variante o estado aplicable incluidos en
  esta spec; debe detectar diferencias no aprobadas y conservar evidencia
  vinculada a la revisión, sin considerarse evidencia suficiente de
  comportamiento funcional o conformidad de accesibilidad.

No se fija en esta spec un único SLO numérico global de latencia ni objetivos de
disponibilidad, RPO, RTO o versiones de runtimes, lenguajes, frameworks o
herramientas. Esos valores requieren contexto operacional o decisiones
posteriores. La matriz de navegadores soportados sí queda definida en
`NFR-001-011`. Tampoco se seleccionan aquí framework UI, librería de
componentes, herramienta de catálogo ni herramienta de regresión visual. La
dirección visual y los valores concretos de la versión inicial se definen y
aprueban en `ui-design.md`; esta spec no exige una identidad corporativa o guía
de marketing completa.

## Objetivos evolutivos de capacidad

Esta sección expresa dirección futura y no constituye aceptación de
`SPEC-001`.

- El primer objetivo evolutivo es soportar 500 usuarios activos con
  aproximadamente una acción cada cinco segundos, equivalente a unas 100
  requests por segundo en promedio bajo ese patrón.
- Los escalones posteriores son 1000, 2500 y 5000 usuarios activos.
- El objetivo de 5000 usuarios equivale aproximadamente a 1000 requests por
  segundo en promedio bajo el mismo patrón.
- Los bursts, la mezcla real de journeys, el volumen de datos y los presupuestos
  de latencia se definirán en una spec posterior de capacidad y rendimiento.
- Los reportes y trabajos pesados tendrán presupuestos separados o ejecución
  diferida cuando corresponda.
- Cada journey crítico respaldado por persistencia deberá definir en una spec
  posterior un presupuesto verificable de cantidad y tiempo de consultas,
  usando datos y volumen representativos en lugar de un número global
  arbitrario.
- `SPEC-001` crea la instrumentación y la capacidad de medición; no demuestra
  ninguno de estos niveles de capacidad.

## Reglas de negocio

- La foundation no crea identidades, sesiones, organizaciones, tenants ni datos
  de negocio.
- Health confirma vida del proceso; readiness confirma capacidad para recibir
  tráfico con sus dependencias obligatorias disponibles.
- La ausencia o invalidez de configuración obligatoria produce un fallo
  cerrado y diagnosticable sin revelar valores sensibles.
- Los errores públicos usan códigos estables y mensajes seguros; los detalles
  internos solo pueden aparecer en observabilidad autorizada y depurada.
- Toda cadena visible se gestiona mediante internacionalización desde su
  incorporación.
- La identidad visual del producto se expresa mediante contratos semánticos y
  versionados; no depende de que una tecnología, framework o librería concreta
  actúe como fuente de verdad.
- `ui-design.md` es la fuente aprobada para la dirección visual inicial y los
  valores concretos de tokens de la aplicación. Su aprobación no equivale a un
  rediseño integral de marca ni a una guía de identidad corporativa.
- La interfaz inicial reutiliza los tokens y componentes compartidos. Una
  variante, estado o estilo nuevo requiere incorporarse al contrato compartido
  o documentar y justificar una excepción conforme a `REQ-001-026`.
- Los estados solo se omiten cuando su no aplicabilidad está declarada. Un
  nombre distinto no convierte en nueva una capacidad visual o de interacción
  equivalente ya cubierta.
- El catálogo ejecutable documenta el contrato permitido, pero no sustituye la
  verificación de la interfaz integrada.
- La regresión visual detecta cambios de presentación; no sustituye pruebas
  funcionales, de teclado, semántica, contraste ni accesibilidad.
- La resolución futura del idioma sigue el orden usuario, tenant, navegador y
  fallback. En esta foundation, el navegador determina español o inglés cuando
  solicita uno de esos idiomas; cualquier valor no soportado o indeterminado
  usa inglés.
- La pantalla inicial se limita a identidad de SimuCenter, mensaje localizado,
  selector de idioma, estado de indisponibilidad del backend y comportamiento
  accesible. No representa un flujo de negocio.
- El backend produce logs estructurados. El frontend limita su observabilidad a
  diagnóstico controlado sin datos sensibles ni telemetría remota obligatoria.
- La corrección de una solicitud ordinaria no depende de memoria mutable de una
  réplica; una caché local puede perderse sin modificar el resultado correcto.
- Los límites de pools y recursos se evalúan de forma agregada para todos los
  procesos y réplicas previstos, no solo de manera aislada por proceso.
- Las métricas usan plantillas de rutas y dimensiones acotadas. Los IDs de
  request, usuario o tenant y las URL crudas no se admiten como dimensiones.
- Una respuesta satisfactoria de health o readiness no demuestra capacidad de
  operaciones de negocio.
- El logging de alto volumen puede reducir, limitar, muestrear o agregar eventos
  informativos exitosos y errores conforme a una política documentada. Ningún
  error puede perderse silenciosamente: si se reduce su detalle individual, se
  conservan contadores del total y de las cantidades emitidas, agregadas o
  suprimidas, junto con muestras representativas que incluyan request ID cuando
  exista contexto.
- Los eventos obligatorios de seguridad o auditoría no pueden descartarse
  silenciosamente. Su clasificación, conservación y tratamiento exactos se
  definirán en `security.md` o en las specs que los introduzcan.
- Las comprobaciones del frontend facilitan la experiencia, pero la autoridad
  de cualquier capacidad futura residirá en el backend.
- La estructura debe respetar ADR-0001: monolito modular y dependencias
  `API → application → domain`, con infrastructure implementando puertos.
- ADR-0002 rige cualquier persistencia tenant futura, pero esta spec no crea
  tablas tenant, contexto tenant ni políticas RLS.
- La evidencia automatizada y reproducible prevalece sobre una declaración
  manual de funcionamiento.

## Casos límite

- Si falta configuración obligatoria o su formato es inválido, el componente
  afectado no debe declararse listo y debe ofrecer un diagnóstico sin incluir
  el valor sensible.
- Si una dependencia obligatoria deja de estar disponible, readiness debe
  reflejarlo sin convertir automáticamente la comprobación de vida del proceso
  en un fallo.
- Si el cliente no envía un request ID o envía uno inválido, el backend debe
  generar uno válido y mantener la correlación de la solicitud.
- Si ocurre una excepción no controlada, el cliente debe recibir el contrato de
  error seguro y los logs autorizados deben conservar el request ID.
- Si se solicita una localización no soportada o falta una traducción, la
  interfaz debe usar inglés como fallback sin mostrar claves internas.
- Si el navegador solicita español o inglés, incluidas sus variantes
  regionales, la interfaz debe usar el idioma soportado correspondiente salvo
  que la persona seleccione explícitamente otro idioma disponible.
- Si el backend no está disponible, la interfaz inicial debe presentar un
  estado comprensible y localizado sin mostrar detalles internos de red.
- Si el frontend detecta un error o evento relevante, el diagnóstico debe
  evitar datos sensibles y no debe depender de un servicio remoto para ser útil
  dentro de esta foundation.
- Si un control obligatorio de CI no puede ejecutarse, el resultado debe
  considerarse no satisfactorio, no equivalente a una prueba pasada.
- Si solicitudes sucesivas llegan a réplicas diferentes, su corrección no debe
  depender de estado mutable conservado por una réplica anterior.
- Si una métrica requerida está ausente, permanece constante ante cambios de
  carga o contiene valores inválidos, no puede usarse para concluir que el
  recurso medido no está saturado.
- Si una dependencia con pool alcanza su límite, el timeout y la respuesta ante
  saturación deben coincidir con el comportamiento documentado y quedar
  reflejados en métricas.
- Si cambia la cantidad de workers o réplicas, debe recalcularse el consumo
  agregado esperado y respetarse el presupuesto documentado.
- Si una tormenta de errores activa rate limiting, agregación o sampling, los
  errores no deben perderse silenciosamente: los logs emitidos conservan
  request ID cuando exista contexto, y la observabilidad mantiene contadores
  del total observado y de las cantidades emitidas, agregadas o suprimidas,
  además de muestras representativas correlacionables.
- Si un evento está clasificado como obligatorio de seguridad o auditoría, no
  puede descartarse silenciosamente aunque exista saturación; su tratamiento
  debe seguir la política definida en `security.md` o en la spec aplicable.
- Si una métrica HTTP recibe una URL cruda o un identificador de request,
  usuario o tenant como dimensión, la verificación debe rechazar esa emisión.
- Si health o readiness resulta satisfactorio, ese resultado no puede
  publicarse como evidencia de capacidad de journeys de negocio.
- Si una categoría de token mínima no aplica a la interfaz inicial, el catálogo
  debe registrar la justificación y no puede omitirla silenciosamente.
- Si un estado no aplica a un componente, su matriz debe marcarlo expresamente;
  si aplica, debe existir un ejemplo ejecutable y verificable.
- Si el viewport coincide con un breakpoint documentado o se sitúa entre dos,
  los componentes no deben perder contenido, orden de foco ni operabilidad por
  desbordamiento o solapamiento.
- Si aparece una necesidad ya cubierta por el sistema compartido, debe
  reutilizarse la capacidad existente; si existe una diferencia material, la
  excepción debe documentarse antes de crear una alternativa.
- Si una comparación visual cambia, la evidencia no puede actualizarse o
  aceptarse silenciosamente; debe distinguir un cambio esperado de una
  regresión y conservar la revisión correspondiente.
- Si una captura visual coincide pero falla una prueba funcional o de
  accesibilidad, el resultado global sigue siendo no satisfactorio.
- Si `specs/001-platform-foundation/ui-design.md` no existe o no cuenta con
  aprobación explícita del Product Owner, no puede redactarse `plan.md` ni
  comenzar la implementación frontend; no se permite sustituir esa aprobación
  por valores visuales provisionales.

## Fuera de alcance

- Autenticación, sesiones y login.
- Usuarios y roles.
- Organizations y tenants.
- Tablas tenant y políticas RLS.
- Módulos de negocio.
- Navegación de negocio o principal, dashboards, formularios complejos, tablas,
  datos simulados y componentes específicos de dominios futuros; se
  incorporarán mediante sus specs posteriores.
- Rediseño integral de marca, materiales comerciales y definición de una
  identidad corporativa o guía de marketing completa.
- Elección de framework UI, librería de componentes, herramienta de catálogo o
  herramienta de regresión visual.
- Correo, push y notificaciones funcionales.
- Aplicaciones móviles.
- Moodle.
- IoT, monitor, desfibrilador y debrief.
- Despliegue productivo definitivo.
- SLA contractual.
- Demostración de capacidad para 500, 1000, 2500 o 5000 usuarios activos.
- Definición de un SLO global de latencia, perfiles de bursts, mezcla completa
  de journeys de negocio o presupuestos de reportes y trabajos pesados.
- Dimensionamiento definitivo de infraestructura y topología productiva.

## Dependencias

- `SIMUCENTER_CONSTITUTION.md`.
- `ADR-0001: Monolito modular orientado por dominios`, estado `Accepted`.
- `ADR-0002: PostgreSQL compartido con aislamiento tenant mediante RLS`, estado
  `Accepted`; sus controles tenant quedan fuera de implementación en esta spec.
- `docs/legacy-analysis/11_PERFORMANCE_EVIDENCE.md`, como evidencia histórica
  sanitizada para definir necesidades de medición sin atribuir capacidad a
  SimuCenter Next.
- Una spec posterior de capacidad y rendimiento para definir carga, journeys,
  volumen de datos, presupuestos de latencia, RPO, RTO y objetivos de capacidad.
- Specs posteriores para ampliar el sistema compartido con formularios
  complejos, tablas, navegación principal, dashboards o componentes propios de
  cada dominio, sin anticiparlos en la foundation.
- Creación, revisión y aprobación explícita por Product Owner de
  `specs/001-platform-foundation/ui-design.md` como artefacto versionado que
  concrete la dirección y los valores exigidos por `REQ-001-022` y
  `REQ-001-028`; su aprobación es obligatoria antes de redactar `plan.md` o
  comenzar la implementación frontend.
- Revisión completa y aprobación explícita de los requisitos; las decisiones
  parciales `Q-001-001`, `Q-001-002` y `Q-001-003` no equivalen a esa aprobación
  general.
- Revisión y actualización posterior, cuando corresponda, de `acceptance.md` y
  `security.md`, seguida de su aprobación explícita antes de redactar el plan,
  conforme al ciclo SDD.

## Contexto legacy relevante

### Estructura y ejecución actuales

- **Fuente consultada:**
  `docs/legacy-analysis/01_CURRENT_ARCHITECTURE.md`
- **Comportamiento encontrado:** El legacy demuestra una aplicación web con
  backend, frontend, contenedores, logs y CI, pero distribuye los dominios por
  capas horizontales y concentra responsabilidades en routers.
- **Decisión:** `REDESIGN`
- **Justificación:** Se conservan las necesidades operativas demostradas, pero
  la foundation debe respetar los límites del monolito modular de ADR-0001 y no
  copiar la estructura legacy.

### Seguridad y observabilidad

- **Fuente consultada:** `docs/legacy-analysis/06_SECURITY_ANALYSIS.md`
- **Comportamiento encontrado:** El legacy incorpora controles de CI,
  scrubbing de observabilidad y validaciones de configuración útiles, junto con
  riesgos que deben tratarse en specs posteriores de identity y tenancy.
- **Decisión:** `REDESIGN`
- **Justificación:** Esta spec adopta las necesidades de configuración segura,
  logs depurados y gates básicos, sin copiar mecanismos de autenticación o
  aislamiento fuera de alcance.

### Deuda de foundation

- **Fuente consultada:** `docs/legacy-analysis/07_TECH_DEBT.md`
- **Comportamiento encontrado:** Se identifican como deuda prioritaria los
  límites de dominio, OpenAPI versionado, i18n desde el primer componente,
  accesibilidad y una política de testing con ownership.
- **Decisión:** `REDESIGN`
- **Justificación:** La nueva base convierte esas necesidades en requisitos
  verificables antes de incorporar dominios funcionales.

### Requisitos base de Next

- **Fuente consultada:**
  `docs/legacy-analysis/09_SIMUCENTER_NEXT_REQUIREMENTS.md`
- **Comportamiento encontrado:** Los requisitos base establecen API-first,
  portabilidad, español e inglés, WCAG 2.2 AA, observabilidad correlacionada y
  CI como condiciones del producto.
- **Decisión:** `KEEP`
- **Justificación:** Son restricciones vigentes de SimuCenter Next y se
  concretan aquí solo para el alcance de platform foundation.

### Evidencia histórica de rendimiento

- **Fuente consultada:**
  `docs/legacy-analysis/11_PERFORMANCE_EVIDENCE.md`
- **Comportamiento encontrado:** Las muestras parciales registran sus propias
  distribuciones de latencia y entre 8.7 y 13 consultas por request; otros
  artefactos registran agotamiento de un pool y límites de volumen de logging.
  Las mediciones de CPU y memoria no permiten concluir ausencia de saturación,
  y los artefactos no demuestran una capacidad completa de 500 usuarios.
- **Decisión:** `REDESIGN`
- **Justificación:** La foundation incorpora instrumentación validable,
  procesamiento stateless, presupuestos agregados y un baseline reproducible;
  las metas de capacidad quedan para una spec posterior.

## Supuestos

- No se adopta ninguna suposición material para aprobar esta spec. Las
  decisiones de producto resueltas están registradas a continuación; las
  elecciones técnicas quedan para ADR o `plan.md` después de las aprobaciones
  requeridas.

## Decisiones resueltas

- **Q-001-001 — Idioma inicial:** Aprobada explícitamente por Product Owner.
  Los idiomas soportados son español e inglés. El orden futuro de preferencia
  es usuario, tenant, navegador y fallback. En esta foundation, si el navegador
  solicita español o inglés se usa ese idioma; para cualquier idioma no
  soportado o indeterminado se usa inglés como fallback.
- **Q-001-002 — Soporte web:** Aprobada explícitamente por Product Owner.
  Chrome, Edge, Firefox y Safari soportan la versión estable actual y la
  anterior; Safari en iOS y Chrome en Android soportan la versión estable
  actual. Internet Explorer y navegadores obsoletos quedan fuera de soporte. La
  compatibilidad debe verificarse mediante evidencia ejecutada.
- **Q-001-003 — Interfaz inicial:** Aprobada explícitamente por Product Owner.
  La interfaz inicial es una pantalla mínima con identidad de SimuCenter,
  nombre o marca, mensaje localizado, selector español/inglés, estado
  comprensible cuando el backend no está disponible, navegación por teclado y
  foco visible. No incluye login, navegación de negocio, dashboard ni datos
  simulados.

Estas decisiones fueron incorporadas antes de la aprobación original de los
requisitos y permanecen vigentes durante la revisión del cambio posterior.

## Control de cambios

- **Aprobación original de requisitos:** estado `Aprobado`, aprobado por
  `Product Owner` el `2026-09-19`, con la referencia
  `Aprobación explícita de los requisitos de SPEC-001 durante la revisión SDD.`
- Product Owner aprobó el `2026-09-19` la solicitud de incorporar la estrategia
  de rendimiento basada en evidencia del legacy.
- Los requisitos modificados fueron reaprobados explícitamente por Product Owner
  el `2026-09-19`.
- Product Owner autorizó el `2026-09-20` incorporar a los requisitos una base
  mínima de sistema de diseño. Esta autorización habilita la modificación
  controlada, pero no constituye reaprobación de los requisitos resultantes.
- Los requisitos modificados para incorporar la base del sistema de diseño
  fueron reaprobados explícitamente por Product Owner el `2026-09-20`.
- SPEC-001 alcanzó `Design Approved` el `2026-09-20` tras verificar las
  aprobaciones vigentes de `acceptance.md`, `security.md`, `ui-design.md`,
  `data-model.md` y `contracts/openapi.yaml`. Esta transición habilita la
  redacción de `plan.md`, pero no constituye aprobación anticipada del plan ni
  autorización de implementación.

## Aprobación de requisitos

- **Estado:** `Aprobado`
- **Aprobado por:** `Product Owner`
- **Fecha:** `2026-09-20`
- **Comentario o referencia:** `Reaprobación explícita de los requisitos modificados de SPEC-001 después de incorporar la base del sistema de diseño.`
