# ADR-0001: Monolito modular orientado por dominios

- **Estado:** Accepted
- **Fecha:** 2026-09-19

## Contexto

La Constitución de SimuCenter Next establece un monolito modular, dependencias
`API → application → domain`, infraestructura implementando puertos y
colaboración entre dominios mediante contratos explícitos.

`[Seguro]` El sistema legacy es un monolito por capas horizontales. Sus dominios
están distribuidos entre `models/`, `schemas/`, `routers/` y `services/`, y
varios routers concentran transporte, autorización, consultas, validación y
orquestación. Este diseño conserva conocimiento valioso, pero dificulta
identificar ownership, aplicar invariantes de forma uniforme y probar el
negocio de manera aislada.

`[Seguro]` Los requisitos base piden un monolito modular por dominios y un
despliegue portable. La matriz de disposición recomienda conservar el monolito
y rediseñar la estructura horizontal.

## Problema que resuelve

Se necesita una estructura que haga explícitos el ownership de datos y reglas,
impida dependencias accidentales entre dominios y permita evolucionar el
sistema sin asumir desde el inicio el costo distribuido de los microservicios.
El diseño debe mantener un despliegue y una operación iniciales sencillos sin
repetir el acoplamiento del legacy.

## Decisión propuesta

Adoptar un monolito modular orientado por dominios con estas reglas:

1. Cada dominio posee sus reglas, datos y contratos públicos. Los límites de
   dominio se definen explícitamente antes de implementar cada capacidad.
2. Dentro de cada dominio, las dependencias siguen
   `API → application → domain`:
   - `API` adapta transporte, autenticación, entrada y salida;
   - `application` coordina casos de uso y transacciones;
   - `domain` contiene reglas e invariantes sin depender de transporte ni de
     detalles de persistencia.
3. `infrastructure` implementa los puertos o interfaces requeridos por dominio
   y application. El dominio no depende de implementaciones de base de datos,
   red, archivos, colas ni frameworks.
4. Los routers no contienen lógica de negocio.
5. Un dominio no consulta directamente las tablas ni usa los repositorios
   internos de otro dominio.
6. La comunicación entre dominios ocurre mediante casos de uso públicos,
   interfaces o eventos aprobados y documentados. Todo acceso conserva el
   ownership del dominio proveedor.
7. El sistema se construye y despliega inicialmente como una sola unidad. Los
   módulos pueden compartir proceso y transacción únicamente a través de sus
   fronteras autorizadas.
8. Los microservicios se difieren hasta que exista evidencia operacional que
   justifique separar un módulo, como necesidades incompatibles de escala,
   disponibilidad, seguridad, despliegue o ownership de equipo.

`[No determinado]` El mapa definitivo de dominios y la ubicación de cada
capacidad se definirán mediante specs y ADR adicionales; este ADR establece las
reglas de frontera, no ese mapa.

## Alternativas consideradas

| Alternativa | Evaluación | Resultado |
|---|---|---|
| Monolito modular orientado por dominios | Conserva operación simple y transacciones locales, mientras hace explícitos ownership y contratos. | Propuesta |
| Monolito por capas horizontales como el legacy | Facilita agrupar por tecnología, pero dispersa cada dominio y favorece lógica en routers y acceso transversal a datos. | Descartada |
| Microservicios | Ofrecen aislamiento de despliegue y escala independiente, pero añaden red, consistencia distribuida, observabilidad, operación y versionado de contratos sin evidencia actual que compense ese costo. | Diferida |

## Consecuencias positivas

- Límites de dominio y ownership explícitos.
- Reglas de negocio más fáciles de probar sin transporte ni infraestructura.
- Menor complejidad de despliegue, debugging y operación inicial.
- Transacciones locales disponibles cuando un caso de uso las necesita.
- Contratos internos que permiten extraer un módulo en el futuro con evidencia.
- Menor riesgo de repetir routers grandes y acoplamiento horizontal del legacy.

## Consecuencias negativas y costos

- Requiere diseñar y revisar fronteras antes de implementar.
- Añade puertos, adaptadores y casos de uso incluso cuando una operación parece
  sencilla.
- Un solo artefacto de despliegue coordina releases de todos los módulos.
- Un fallo de proceso o consumo excesivo puede afectar a toda la aplicación.
- La disciplina modular debe verificarse con revisiones y controles de
  dependencias; el lenguaje por sí solo no impide accesos indebidos.
- Las operaciones que cruzan dominios pueden requerir contratos, eventos y
  consistencia eventual adicionales.

## Riesgos

- Que los módulos se conviertan en convenciones nominales sin aislamiento real.
- Que se creen dependencias cíclicas entre dominios.
- Que interfaces demasiado amplias expongan detalles internos.
- Que se use la transacción compartida para eludir contratos de dominio.
- Que una división de dominios prematura sea incorrecta y exija mover
  responsabilidades.
- `[No determinado]` Aún no existe una herramienta aprobada para validar
  automáticamente las dependencias entre módulos.

## Restricciones que impone

- Toda capacidad nueva debe tener un owner de dominio identificado.
- Las dependencias solo pueden avanzar de `API` a `application` y de
  `application` a `domain`; `infrastructure` depende de los puertos que
  implementa.
- La lógica de negocio no puede residir en routers, controladores, componentes
  visuales ni adaptadores de infraestructura.
- Ningún módulo puede consultar directamente tablas de otro dominio.
- Toda colaboración entre dominios debe usar un caso de uso, interfaz o evento
  aprobado y probado.
- Un cambio estructural de fronteras o dependencias requiere ADR.
- No se extraerá un microservicio solo como preparación especulativa.

## Criterios que obligarían a reconsiderarlo

- Un módulo necesita escalar o desplegarse de forma independiente de manera
  sostenida y medible.
- Requisitos de aislamiento, residencia de datos o cumplimiento no pueden
  satisfacerse dentro del proceso compartido.
- Los objetivos de disponibilidad exigen desacoplar fallos de una capacidad.
- Equipos independientes requieren ciclos de entrega incompatibles y los
  contratos internos ya son estables.
- La coordinación dentro del monolito produce un cuello de botella demostrado
  que no puede resolverse manteniendo la modularidad.
- Una tecnología imprescindible no puede operar razonablemente en la unidad de
  despliegue común.

## Referencias

- `SIMUCENTER_CONSTITUTION.md`: secciones 1, 2, 6, 8, 10, 11 y 12.
- `docs/legacy-analysis/01_CURRENT_ARCHITECTURE.md`: estructura horizontal,
  flujo real, límites actuales y decisión para Next.
- `docs/legacy-analysis/08_KEEP_REDESIGN_REMOVE.md`: conservar el monolito,
  rediseñar capas horizontales y regla de migración.
- `docs/legacy-analysis/09_SIMUCENTER_NEXT_REQUIREMENTS.md`:
  `REQ-ARCH-001`, `REQ-ARCH-002`, `REQ-ARCH-004`, `REQ-ARCH-005` y
  `REQ-ARCH-006`.

## Aprobación pendiente

La existencia de este ADR no constituye aprobación.

- **Estado de aprobación:** Aprobado
- **Aprobado por:** Product Owner
- **Fecha:** 2026-09-19
- **Comentario o referencia:** Aprobación explícita durante la revisión SDD.
