# ADR-0003: Envoy público con origen estático NGINX interno

- **Estado:** Accepted
- **Fecha:** 2026-09-30

## Contexto

SPEC-001 necesita publicar la pantalla construida por Vite y la tipografía
Inter sin añadir operaciones de API. Envoy ya está definido como único límite
HTTP público para TLS, listeners, rutas, límites, autorización operacional,
correlación, respuestas locales y observabilidad del borde.

`[Seguro]` La documentación oficial de Envoy 1.39.1 declara su filtro HTTP
`file_server` incompleto y no destinado a producción. Durante el inicio parcial
de `TASK-001-001` se detectó que usarlo para el frontend productivo dejaba una
decisión estructural sin registrar. El Product Owner autorizó documentar la
topología corregida el 2026-09-30; esa autorización no aprueba este ADR, el
plan ni las tareas reabiertas.

## Problema que resuelve

El origen productivo de los archivos estáticos debe quedar definido sin
atribuir a Envoy un servicio de archivos que su filtro no ofrece como
capacidad productiva, y sin abrir otra entrada pública ni ampliar el contrato
de la API.

## Decisión propuesta

1. Envoy permanece como único límite HTTP público. Conserva terminación TLS,
   listeners público y operacional separados, selección y precedencia de rutas,
   límites, control de acceso, correlación, respuestas locales y observabilidad
   del borde. No usa `file_server` para el frontend productivo.
2. Se incorpora `web-static` como cuarto servicio central, junto a backend,
   Envoy y OpenTelemetry Collector. Usa una imagen oficial NGINX basada en
   Debian 13 Trixie: `docker.io/library/nginx:<VERSION_ESTABLE_EXACTA>-trixie`.
   `TASK-001-001` resolverá la versión estable exacta, el
   digest del índice y el digest hijo `linux/amd64`; ninguna referencia flotante
   satisface ese cierre.
3. `web-static` sirve exclusivamente los artefactos Vite y la tipografía Inter,
   incluidos los archivos necesarios para el fallback SPA. No contiene lógica
   de negocio, no implementa endpoints de API y no actúa como proxy del backend
   ni de otros destinos.
4. El flujo público es cliente → Envoy → backend para `GET /health`, o cliente
   → Envoy → `web-static` para activos y fallback SPA. El flujo operacional es
   cliente operacional autorizado → listener operacional de Envoy → backend
   para `GET /ready`.
5. `/ready` está ausente del mapa público. Envoy resuelve su exclusión antes
   del catch-all estático, de modo que el fallback SPA no puede convertirlo en
   `index.html` ni en 200. Su 404 público representa ausencia de ruta, no una
   respuesta contratada de la operación operacional.
6. `web-static` no publica puerto ni DNS público. Solo Envoy puede alcanzarlo
   mediante la red interna correspondiente; NGINX no termina TLS público ni
   asume controles propios del borde.
7. Node se usa en build y herramientas, incluido el catálogo local/CI. No
   forma parte del runtime productivo del frontend.
8. El servicio estático no cambia el monolito modular de ADR-0001: no se
   extrae un dominio ni se traslada una regla de negocio. No persiste datos y
   no altera la decisión PostgreSQL/RLS de ADR-0002. Este ADR no reemplaza ni
   contradice esos dos registros.

## Alternativas consideradas

| Alternativa | Evaluación | Resultado |
|---|---|---|
| Envoy como borde y NGINX interno como origen estático | Separa la entrada pública del servicio de archivos y conserva los controles del borde. | Propuesta seleccionada y aceptada el 2026-10-01 |
| `file_server` de Envoy en producción | El filtro está documentado oficialmente como incompleto y no destinado a producción. | Descartada |
| Servir los estáticos desde FastAPI | Añade servicio de archivos a la aplicación y mezcla el origen estático con el backend. | Descartada |
| Exponer NGINX directamente | Crea una segunda entrada pública fuera de los controles de Envoy. | Descartada |
| Incorporar Node al runtime productivo | Mantiene un runtime de build/herramientas para archivos ya compilados. | Descartada |

## Consecuencias positivas

- Envoy conserva una sola frontera pública y NGINX una responsabilidad estática
  acotada.
- Se reduce el riesgo de depender productivamente de un filtro de Envoy no
  destinado a producción.
- La exclusión pública de `/ready` tiene un owner claro en las rutas de Envoy
  antes del fallback SPA.

## Consecuencias negativas y costos

- La topología central pasa a cuatro servicios y añade un salto interno.
- `web-static` añade imagen, fijación por digest, SBOM, escaneo, inventario,
  procedencia, logs, presupuesto de recursos, red y operación propios.
- La disponibilidad de los archivos estáticos depende de la conectividad
  interna entre Envoy y `web-static`.

## Riesgos

- Una ruta pública mal ordenada podría entregar la SPA en `/ready`.
- Una publicación accidental de NGINX o una red demasiado amplia podría
  eludir los controles del borde.
- Una referencia OCI flotante o un digest de arquitectura incorrecta impediría
  reproducir la imagen entregada.
- Logs o respuestas del servidor estático podrían exponer valores sensibles si
  no se aplican las reglas de observabilidad y reducción aprobadas.

## Restricciones que impone

- No aprobar el plan reabierto antes de aprobar independientemente este ADR.
- No cerrar `TASK-001-001` sin versión exacta de NGINX y digests de índice e
  hijo `linux/amd64` con procedencia verificable.
- No publicar puerto ni DNS de `web-static`; demostrar conectividad desde Envoy
  y ausencia de acceso directo externo en el entorno real.
- Mantener `/ready` fuera de la entrada pública antes del fallback SPA y sin
  convertir archivos estáticos en nuevas operaciones de API.
- Aplicar a `web-static` los controles de imagen, recursos, logs, SBOM,
  escaneo y procedencia del plan aprobado cuando se implemente.

## Criterios que obligarían a reconsiderarlo

- Una limitación demostrada impide servir de forma segura los artefactos Vite e
  Inter desde NGINX interno bajo los límites y la red aprobados.
- Un requisito aprobado exige otra frontera pública o un origen de archivos
  con capacidades diferentes.
- La topología no puede cumplir la exclusión de `/ready` ni el aislamiento de
  `web-static` en los entornos previstos.

## Referencias

- `SIMUCENTER_CONSTITUTION.md`: ADR obligatorio para cambios estructurales.
- `AGENTS.md`: cambio arquitectónico estructural requiere ADR.
- `docs/architecture/adr/ADR-0001-modular-monolith.md`: límites del backend.
- `docs/architecture/adr/ADR-0002-shared-postgresql-rls.md`: persistencia y
  aislamiento tenant futuros.
- `specs/001-platform-foundation/plan.md`: topología y controles propuestos.
- [Documentación oficial del filtro `file_server` de Envoy 1.39.1](https://www.envoyproxy.io/docs/envoy/v1.39.1/api-v3/extensions/filters/http/file_server/v3/file_server.proto).

## Aprobación

La aprobación de este ADR acepta la decisión arquitectónica; no aprueba el
plan ni las tareas reabiertas, ni constituye implementación de la topología.

- **Estado de aprobación:** Aprobado
- **Aprobado por:** Product Owner
- **Fecha:** 2026-10-01
- **Comentario o referencia:** Aprobación explícita del Product Owner del
  2026-10-01 de ADR-0003, comprendiendo la topología cliente → Envoy público
  → backend o `web-static` interno, sus límites, alternativas, consecuencias,
  riesgos y criterios de reconsideración.
