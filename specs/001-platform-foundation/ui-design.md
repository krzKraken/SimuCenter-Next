# Diseño visual de Platform Foundation

## Metadata

- **Referencia:** `SPEC-001`
- **Versión del diseño:** `1.0.0`
- **Estado:** `Approved`
- **Responsable:** `Product Owner`
- **Fecha:** `2026-09-20`
- **Última actualización:** `2026-09-20`

Los valores concretos de este documento pertenecen a la versión aprobada
`1.0.0`. La dirección visual, los tokens, los componentes y las reglas fueron
aprobados explícitamente por Product Owner y constituyen la base visual
aprobada para `SPEC-001`.

## Dirección visual aprobada

El Product Owner aprobó el `2026-09-20` la dirección **SimuCenter Clinical
Precision**: IBM Carbon se usa como referencia de estructura y densidad
empresarial; Apple, de jerarquía y refinamiento; NHS, de claridad clínica y
accesibilidad; y Atlassian, de navegación y consistencia de flujos. La identidad
usa superficies sólidas, tema claro, navegación azul tinta oscura y teal como
acento principal.

Estas referencias expresan principios de diseño. No autorizan copiar
componentes, valores de marca, código, iconografía ni patrones propietarios, ni
mezclar librerías. Este documento define una identidad propia e independiente
de frameworks, librerías, herramientas y proveedores.

## Principios

1. **Claridad antes que decoración:** cada elemento debe aportar jerarquía,
   significado o acción reconocible.
2. **Alta densidad con legibilidad:** se reduce espacio desperdiciado sin
   comprimir texto, foco ni áreas de interacción.
3. **Jerarquía inequívoca:** tipografía, espaciado, bordes y color comunican qué
   es principal, secundario, agrupado o interactivo.
4. **Consistencia entre módulos:** una misma capacidad reutiliza tokens,
   primitivas y componentes compartidos.
5. **Accesibilidad desde el componente:** teclado, foco, semántica, nombre
   accesible y contraste forman parte del contrato, no son ajustes posteriores.
6. **Superficies sólidas:** los planos se distinguen mediante color opaco,
   borde y espaciado; los efectos de vidrio no forman parte de la identidad.
7. **Color semántico:** los nombres describen propósito y estado, nunca una
   tecnología ni un color literal como contrato de consumo.
8. **Progressive disclosure:** se muestra primero lo necesario y se revelan
   detalles adicionales mediante controles explícitos y accesibles.
9. **Eficiencia por teclado:** el orden DOM conserva el orden visual y las
   acciones frecuentes se alcanzan y operan sin puntero.
10. **Responsive legible:** el contenido cambia de flujo y prioridad sin
    convertir la versión móvil en una reducción tipográfica ilegible.

## Identidad inicial

- **Nombre visible:** `SimuCenter`.
- **Concepto:** `SimuCenter Clinical Precision`.
- **Wordmark tipográfico inicial:** texto `SimuCenter` en la familia inicial,
  `Inter`, peso `700`, tamaño `24 px`, line-height `32 px`, tracking `-0.01em`
  y color `text.inverse` sobre `color.navigation`. Es el wordmark aprobado para
  la primera versión y debe conservar el nombre como texto accesible.
- La identidad inicial no depende de un isotipo ni de un logo definitivo.
- Un logo futuro ocupará una región reemplazable y no podrá exigir cambios en
  tokens, layout, nombres accesibles o contratos de componentes.
- Ningún logo generado por IA constituye fuente normativa para proporciones,
  colores, iconos o geometría.

## Temas

- La implementación inicial usa tema claro y una franja de aplicación o
  navegación azul tinta oscura. `SPEC-001` no incorpora navegación de negocio.
- No se implementa un tema oscuro completo en `SPEC-001`.
- Los componentes consumen tokens semánticos para que un tema futuro pueda
  cambiar valores sin reescribir su anatomía o estados.
- Se prohíben transparencias de superficies, glassmorphism, desenfoque de fondo
  y cualquier efecto de vidrio.
- Los gradientes decorativos no se usan como estructura principal.
- Las sombras no sustituyen bordes, agrupación o jerarquía necesarios.
- La prohibición de transparencia se refiere a superficies y efectos visuales
  de interfaz. No impide el canal alfa necesario para antialias de imágenes,
  iconos o detalles internos del navegador, ni el canal alfa de
  `shadow.raised` usado solo para dibujar una sombra sutil alrededor de una
  superficie completamente opaca. Esta excepción no autoriza paneles, fondos o
  controles translúcidos.
- `SPEC-001` no define overlays. Si una spec futura realmente los necesita,
  deberá diseñarlos, verificarlos y obtener su aprobación sin heredar valores
  concretos de este documento.

## Tokens de color

Todos los valores son hexadecimales concretos del draft `1.0.0-draft`. Los
nombres son semánticos y no exponen referencias a sistemas externos.

### Superficies, navegación y texto

| Token | Valor | Uso permitido |
|---|---:|---|
| `color.background` | `#F4F7FA` | Fondo general de la aplicación |
| `color.surface.primary` | `#FFFFFF` | Superficie principal |
| `color.surface.secondary` | `#EAF0F4` | Agrupación secundaria sólida |
| `color.surface.elevated` | `#FFFFFF` | Superficie elevada sólida, siempre con borde o elevación definida |
| `color.navigation` | `#0B1F33` | Franja de aplicación y navegación futura |
| `color.navigation.hover` | `#153A56` | Hover de controles en navegación |
| `color.navigation.active` | `#0E4F63` | Estado activo en navegación |
| `color.navigation.text` | `#F7FBFC` | Texto e iconos en navegación |
| `color.text.primary` | `#102A43` | Texto principal |
| `color.text.secondary` | `#415B6B` | Texto secundario |
| `color.text.muted` | `#5F7482` | Metadata, caption y ayuda no crítica |
| `color.text.inverse` | `#FFFFFF` | Texto sobre fondos oscuros o teal aprobado |

### Bordes, foco, acento, enlaces y disabled

| Token | Valor | Uso permitido |
|---|---:|---|
| `color.border.normal` | `#748995` | Contornos de controles y separadores necesarios |
| `color.border.strong` | `#526C7A` | Contornos con énfasis o límites estructurales |
| `color.focus.on-light` | `#007C91` | Anillo de `focus-visible` sobre superficies claras aprobadas |
| `color.focus.on-dark` | `#7DE3D8` | Anillo de `focus-visible` sobre navegación oscura aprobada |
| `color.teal.primary` | `#007C83` | Acción primaria y acento principal |
| `color.teal.hover` | `#00666E` | Hover de acción primaria |
| `color.teal.active` | `#00535A` | Active y texto teal sobre superficie subtle |
| `color.teal.subtle` | `#D9F3F1` | Fondo sólido de énfasis teal |
| `color.link.normal` | `#005FA3` | Enlace normal, siempre subrayado en texto corrido |
| `color.link.visited` | `#5B3F8C` | Enlace visitado, siempre subrayado en texto corrido |
| `color.disabled.foreground` | `#667985` | Contenido de control deshabilitado |
| `color.disabled.background` | `#E3E9ED` | Fondo sólido de control deshabilitado |
| `color.disabled.border` | `#9AACB6` | Borde de control deshabilitado |

`color.teal.primary` no se usa como texto normal sobre `color.teal.subtle`;
para esa combinación se usa `color.teal.active`.

### Estados semánticos

| Estado | Foreground | Background | Border | Uso |
|---|---:|---:|---:|---|
| `info` | `#174A72` | `#E5F2FB` | `#2B6F9F` | Información técnica o contextual |
| `success` | `#145A32` | `#E7F5EC` | `#2E7D4F` | Resultado completado o disponible |
| `warning` | `#6B4600` | `#FFF3CD` | `#A66C00` | Precaución o degradación recuperable |
| `danger` | `#8A1C1C` | `#FCE8E8` | `#B53B3B` | Error, indisponibilidad o acción riesgosa futura |

Cada estado combina texto explícito, icono cuando corresponda y color. El color
nunca es la única señal.

### Contraste reproducible

El cálculo usa luminancia relativa sRGB de WCAG 2.2:

1. cada canal hexadecimal se divide entre `255`;
2. si el canal es menor o igual a `0.04045`, se divide entre `12.92`; en otro
   caso se usa `((canal + 0.055) / 1.055) ^ 2.4`;
3. `L = 0.2126R + 0.7152G + 0.0722B`;
4. ratio = `(L mayor + 0.05) / (L menor + 0.05)`.

Los ratios se muestran truncados a cuatro decimales, nunca redondeados hacia
arriba. Texto normal exige `4.5:1`; componentes gráficos, bordes necesarios y
foco exigen `3:1`. El contenido disabled está exento del mínimo normativo, pero
este diseño adopta un objetivo interno de `3:1`.

| Foreground | Background | Ratio | Objetivo aplicable | Resultado |
|---|---|---:|---:|---|
| `text.primary #102A43` | `background #F4F7FA` | `13.6176:1` | `4.5:1` texto normal | Pasa |
| `text.primary #102A43` | `surface.primary #FFFFFF` | `14.6416:1` | `4.5:1` texto normal | Pasa |
| `text.primary #102A43` | `surface.secondary #EAF0F4` | `12.7374:1` | `4.5:1` texto normal | Pasa |
| `text.secondary #415B6B` | `surface.primary #FFFFFF` | `7.1586:1` | `4.5:1` texto normal | Pasa |
| `text.muted #5F7482` | `surface.primary #FFFFFF` | `4.8756:1` | `4.5:1` texto normal | Pasa |
| `navigation.text #F7FBFC` | `navigation #0B1F33` | `16.0245:1` | `4.5:1` texto normal | Pasa |
| `navigation.text #F7FBFC` | `navigation.hover #153A56` | `11.3799:1` | `4.5:1` texto normal | Pasa |
| `navigation.text #F7FBFC` | `navigation.active #0E4F63` | `8.6997:1` | `4.5:1` texto normal | Pasa |
| `text.inverse #FFFFFF` | `teal.primary #007C83` | `4.9871:1` | `4.5:1` texto normal | Pasa |
| `text.inverse #FFFFFF` | `teal.hover #00666E` | `6.7184:1` | `4.5:1` texto normal | Pasa |
| `text.inverse #FFFFFF` | `teal.active #00535A` | `8.8052:1` | `4.5:1` texto normal | Pasa |
| `teal.active #00535A` | `teal.subtle #D9F3F1` | `7.5644:1` | `4.5:1` texto normal | Pasa |
| `link.normal #005FA3` | `surface.primary #FFFFFF` | `6.6334:1` | `4.5:1` texto normal | Pasa |
| `link.visited #5B3F8C` | `surface.primary #FFFFFF` | `8.2855:1` | `4.5:1` texto normal | Pasa |
| `info.foreground #174A72` | `info.background #E5F2FB` | `8.1567:1` | `4.5:1` texto normal | Pasa |
| `success.foreground #145A32` | `success.background #E7F5EC` | `7.3553:1` | `4.5:1` texto normal | Pasa |
| `warning.foreground #6B4600` | `warning.background #FFF3CD` | `7.5778:1` | `4.5:1` texto normal | Pasa |
| `danger.foreground #8A1C1C` | `danger.background #FCE8E8` | `7.8845:1` | `4.5:1` texto normal | Pasa |
| `disabled.foreground #667985` | `disabled.background #E3E9ED` | `3.6960:1` | `3:1` objetivo interno | Pasa |
| `border.normal #748995` | `surface.primary #FFFFFF` | `3.6490:1` | `3:1` no textual | Pasa |
| `border.strong #526C7A` | `surface.primary #FFFFFF` | `5.5484:1` | `3:1` no textual | Pasa |
| `focus.on-light #007C91` | `background #F4F7FA` | `4.5507:1` | `3:1` foco | Pasa |
| `focus.on-light #007C91` | `surface.primary #FFFFFF` | `4.8929:1` | `3:1` foco | Pasa |
| `focus.on-light #007C91` | `surface.secondary #EAF0F4` | `4.2565:1` | `3:1` foco | Pasa |
| `focus.on-light #007C91` | `teal.subtle #D9F3F1` | `4.2034:1` | `3:1` foco | Pasa |
| `focus.on-light #007C91` | `info.background #E5F2FB` | `4.2930:1` | `3:1` foco | Pasa |
| `focus.on-light #007C91` | `success.background #E7F5EC` | `4.3500:1` | `3:1` foco | Pasa |
| `focus.on-light #007C91` | `warning.background #FFF3CD` | `4.4161:1` | `3:1` foco | Pasa |
| `focus.on-light #007C91` | `danger.background #FCE8E8` | `4.1582:1` | `3:1` foco | Pasa |
| `focus.on-dark #7DE3D8` | `navigation #0B1F33` | `11.0102:1` | `3:1` foco | Pasa |
| `focus.on-dark #7DE3D8` | `navigation.hover #153A56` | `7.8189:1` | `3:1` foco | Pasa |
| `focus.on-dark #7DE3D8` | `navigation.active #0E4F63` | `5.9774:1` | `3:1` foco | Pasa |
| `info.border #2B6F9F` | `info.background #E5F2FB` | `4.7523:1` | `3:1` no textual | Pasa |
| `success.border #2E7D4F` | `success.background #E7F5EC` | `4.4852:1` | `3:1` no textual | Pasa |
| `warning.border #A66C00` | `warning.background #FFF3CD` | `3.9765:1` | `3:1` no textual | Pasa |
| `danger.border #B53B3B` | `danger.background #FCE8E8` | `4.8849:1` | `3:1` no textual | Pasa |

## Tipografía

La familia inicial aprobada es `Inter` y el fallback stack completo es:

```text
Inter, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif
```

Pesos permitidos: `400` regular, `500` medium, `600` semibold y `700` bold. No
se simulan pesos ausentes. `Inter` debe empaquetarse y servirse localmente; se
prohíbe cargarla desde CDN o cualquier destino externo. La licencia, la
integridad, los archivos exactos y la estrategia concreta de empaquetado se
verificarán en `plan.md` y deberán conservar estas métricas y contratos
semánticos.

### Escala tipográfica

| Token | Tamaño / line-height | Peso | Tracking | Uso |
|---|---:|---:|---:|---|
| `type.display` | `40 / 48 px` | `700` | `-0.02em` | Identidad o encabezado excepcional |
| `type.heading.lg` | `32 / 40 px` | `700` | `-0.015em` | Título principal de página |
| `type.heading.md` | `24 / 32 px` | `600` | `-0.01em` | Título de sección |
| `type.heading.sm` | `20 / 28 px` | `600` | `0` | Subtítulo o grupo |
| `type.body.lg` | `18 / 28 px` | `400` | `0` | Mensaje destacado |
| `type.body` | `16 / 24 px` | `400` | `0` | Texto operativo normal |
| `type.label` | `14 / 20 px` | `600` | `0.01em` | Etiqueta de control |
| `type.helper` | `14 / 20 px` | `400` | `0` | Ayuda y validación |
| `type.caption` | `12 / 16 px` | `500` | `0.01em` | Metadata breve no operativa |

El texto operativo normal usa como mínimo `16 px`. `type.caption` no puede
contener instrucciones, acciones, estados críticos ni información necesaria
para completar una tarea.

## Espaciado y tamaño

La escala base es de `4 px`.

| Token | Valor | Uso semántico |
|---|---:|---|
| `space.none` | `0 px` | Ausencia intencional de separación |
| `space.2xs` | `4 px` | Separación interna mínima entre icono y detalle |
| `space.xs` | `8 px` | Icono-texto y elementos estrechamente relacionados |
| `space.sm` | `12 px` | Contenido interno compacto permitido |
| `space.md` | `16 px` | Padding y separación base |
| `space.lg` | `24 px` | Separación de grupos y secciones menores |
| `space.xl` | `32 px` | Separación de secciones principales |
| `space.2xl` | `48 px` | Ritmo de página |
| `space.3xl` | `64 px` | Separación excepcional en desktop/wide |

Tokens semánticos: `gap.control = 8 px`, `gap.field = 12 px`,
`gap.group = 16 px`, `gap.section = 24 px` y `gap.page = 32 px` en desktop.

| Token de tamaño | Valor | Uso |
|---|---:|---|
| `size.control.default` | `44 px` | Altura mínima de control de la foundation |
| `size.control.large` | `48 px` | Control con énfasis o mayor espacio táctil |
| `size.target.minimum` | `44 × 44 px` | Área interactiva mínima para puntero y táctil |
| `size.icon.sm` | `16 px` | Indicador secundario |
| `size.icon.md` | `20 px` | Icono predeterminado de control |
| `size.icon.lg` | `24 px` | Icono destacado excepcional |
| `size.content.reading` | `720 px` | Ancho máximo del bloque de lectura inicial |
| `size.content.application` | `1600 px` | Ancho máximo del container de aplicación |

- El área de interacción conserva `size.target.minimum` aunque el gráfico
  visible sea menor.
- Padding horizontal de control: `16 px`; separación icono-texto: `8 px`.
- La densidad `comfortable` es la única densidad de `SPEC-001`.
- La densidad compacta queda reservada para futuras superficies de datos y
  requiere autorización en la spec que las introduzca.

## Bordes, radios, sombras y elevación

### Valores

| Categoría | Token | Valor |
|---|---|---:|
| Borde | `border.width.default` | `1 px` |
| Borde | `border.width.strong` | `2 px` |
| Foco | `focus.ring.width` | `3 px` exterior |
| Foco | `focus.ring.offset` | `2 px` de separación |
| Radio | `radius.none` | `0 px` |
| Radio | `radius.sm` | `4 px` |
| Radio | `radius.md` | `8 px` |
| Radio | `radius.lg` | `12 px` |
| Radio | `radius.pill` | `999 px` |
| Sombra | `shadow.none` | `none` |
| Sombra | `shadow.raised` | `0 1px 2px #102A4324` |
| Elevación | `elevation.base` | `0` |
| Elevación | `elevation.raised` | `1` |

`shadow.raised` usa alfa únicamente para dibujar una sombra sutil alrededor de
una superficie completamente opaca. No autoriza paneles, fondos ni controles
translúcidos. `radius.pill` se limita a indicadores breves cuya semántica sea de
etiqueta o estado; no se aplica por estilo a contenedores, botones generales o
campos. `SPEC-001` no define sombras ni niveles de elevación para overlays; una
spec futura deberá diseñarlos y aprobarlos si demuestra que son necesarios.

### Matriz de delimitación

| Caso | Borde | Sombra | Regla |
|---|---|---|---|
| Fondo de página | Ninguno | Ninguna | Jerarquía por espaciado |
| Superficie principal | Normal si colinda con otro plano | Ninguna | No duplicar borde cuando el fondo ya separa |
| Superficie secundaria | Normal cuando sea interactiva o estructural | Ninguna | Color sólido y separación |
| Control interactivo | Normal; fuerte solo por énfasis semántico | Ninguna | El foco usa anillo independiente |
| Alert/message | Borde semántico | Ninguna | No depender solo del fondo |
| Superficie elevada | Normal | `shadow.raised` | Solo si existe solapamiento real |
| Navegación oscura | Ninguno o separador fuerte | Ninguna | Jerarquía por superficie sólida |

## Breakpoints y layout

| Nombre | Rango | Gutter | Columnas | Ancho máximo de contenido |
|---|---:|---:|---:|---:|
| `mobile` | `0–599 px` | `16 px` | `4` | `100%` |
| `tablet` | `600–1023 px` | `24 px` | `8` | `960 px` |
| `desktop` | `1024–1439 px` | `32 px` | `12` | `1200 px` |
| `wide` | `≥1440 px` | `40 px` | `12` | `1600 px` |

`Container` ofrece modo `reading`, con máximo `720 px`, y modo `application`,
con máximo `1600 px`. `SPEC-001` usa `reading` para la interfaz inicial cuando
corresponda, aunque el container general de aplicación pueda ser mayor. No se
autorizan todavía superficies de datos fluidas sin ancho máximo; una spec
futura de tablas o dashboards podrá definirlas.

- `mobile`: una columna; controles críticos ocupan el ancho disponible cuando
  mejora la operabilidad; no se reduce tipografía para hacerlos caber.
- `tablet`: grupos relacionados pueden ocupar dos zonas, pero vuelven a una
  columna antes de producir solapamiento o truncar contenido.
- `desktop` y `wide`: se usan columnas para alineación, no para llenar espacio
  con contenido innecesario.
- El reflow modifica distribución visual mediante layout; conserva orden DOM,
  orden de lectura y orden de foco.
- Ningún breakpoint admite scroll horizontal accidental. Contenido largo debe
  envolver; controles se reubican o crecen verticalmente.
- La franja azul tinta de la interfaz inicial contiene identidad y controles
  globales permitidos. Una navegación oscura futura conservará contraste,
  áreas de interacción y orden DOM; sus destinos, agrupaciones y comportamiento
  móvil deberán definirse en la spec que introduzca navegación de negocio.

## Motion

| Token | Duración | Easing | Uso permitido |
|---|---:|---|---|
| `motion.instant` | `80 ms` | `linear` | Cambio discreto de color o borde que no comunica información por sí solo |
| `motion.fast` | `160 ms` | `cubic-bezier(0.2, 0, 0, 1)` | Hover y presión |
| `motion.standard` | `240 ms` | `cubic-bezier(0.2, 0, 0, 1)` | Expansión o reflow pequeño |
| `motion.deliberate` | `320 ms` | `cubic-bezier(0.2, 0, 0, 1)` | Transición contextual excepcional |

- Se permiten transiciones breves de color o borde que confirmen estado sin
  retrasar la acción. No se usan transiciones de opacidad en superficies o
  controles.
- El anillo de foco visible aparece inmediatamente, sin transición, animación
  ni retraso; ningún token de motion se aplica a su aparición.
- Se prohíben animaciones decorativas continuas, parallax, destellos,
  desplazamientos que oculten contenido y motion que bloquee interacción.
- Ninguna animación comunica información sin texto, semántica o cambio visual
  estático equivalente.
- Con `prefers-reduced-motion: reduce`, el motion no esencial usa `0 ms`; el
  contenido conserva el mismo estado final y el foco no se desplaza mediante
  animación.
- Un estado loading mantiene etiqueta textual; si usa un indicador animado,
  reduced motion lo presenta estático sin perder significado.

## Iconografía

- Estilo: geométrico, sobrio, de contorno y sin detalle decorativo innecesario.
- Tamaños normativos: `16 px`, `20 px` y `24 px`; controles de la foundation
  usan `20 px` por defecto.
- Grosor visual objetivo: `1.75 px`, consistente dentro del mismo contexto.
- Alineación: caja óptica centrada con la línea de texto; separación
  icono-texto de `8 px`.
- Los iconos heredan color semántico y no incorporan colores de marca propios.
- Un icono acompaña texto cuando el significado no es inequívoco. Un icono solo
  requiere nombre accesible y una convención ampliamente reconocible.
- No se selecciona una librería concreta. Los recursos deben ser locales o
  estar aprobados, inventariados y sin tracking externo, conforme a
  `security.md`.

## Componentes mínimos

Las variantes, estados y ejemplos descritos aquí forman el contrato del futuro
catálogo ejecutable. Todo componente consume tokens semánticos; ninguna
variante define colores, espaciado o tipografía paralelos.

### Button

- **Propósito:** iniciar una acción explícita.
- **Anatomía:** contenedor, etiqueta obligatoria, icono opcional e indicador de
  loading opcional. Un icono solo exige nombre accesible inequívoco.
- **Variantes:** `primary` (teal sólido), `secondary` (superficie, borde normal)
  y `quiet` (sin contenedor persistente, con estados visibles). No se incluye
  una variante destructiva en `SPEC-001`.
- **Tamaños:** `default` de `44 px` y `large` de `48 px`; padding horizontal de
  `16 px` y `20 px`, respectivamente.
- **Contenido:** verbo directo y breve; no usar “clic aquí”. La etiqueta no se
  reemplaza durante loading; se añade un texto localizado de estado.
- **Tokens:** `teal.*`, `text.*`, `surface.*`, `border.*`, `radius.md`,
  `type.label`, `space.*`, `color.focus.on-light`, `color.focus.on-dark`,
  `focus.ring.width`, `focus.ring.offset`, `motion.fast`.
- **Teclado y foco:** Tab alcanza el botón; Enter y Space lo activan una vez.
  `focus-visible` usa `focus.ring.width` de `3 px` y `focus.ring.offset` de
  `2 px`, con `color.focus.on-light` o `color.focus.on-dark` según la superficie
  adyacente. El anillo aparece inmediatamente, sin transición ni retraso;
  `motion.fast` se limita a hover y presión. Loading y disabled impiden
  activación duplicada.
- **Semántica:** botón con nombre accesible; disabled real cuando no es
  operable; loading expone estado ocupado sin eliminar la etiqueta.
- **Responsive:** permite wrap de etiqueta y puede ocupar ancho completo en
  mobile; no reduce texto ni área interactiva.
- **Usos prohibidos:** navegación presentada como botón, dos acciones primarias
  equivalentes en el mismo grupo, texto ambiguo, solo color para indicar estado
  o spinner sin etiqueta.

### Language selector

- **Propósito:** seleccionar español o inglés en la interfaz inicial.
- **Anatomía:** etiqueta visible localizada y control de selección única con
  las opciones `Español` y `English` en su forma propia.
- **Variantes:** `on-light` y `on-navigation`; ambas comparten anatomía y
  comportamiento.
- **Tamaño:** altura `44 px`; ancho según contenido con mínimo `144 px` y sin
  truncar nombres de idioma.
- **Contenido:** nombre completo del idioma; no usar banderas ni siglas como
  único nombre.
- **Tokens:** `surface.*`, `navigation.*`, `text.*`, `border.*`, `radius.md`,
  `type.label`, `space.*`, `color.focus.on-light`, `color.focus.on-dark`,
  `focus.ring.width`, `focus.ring.offset`.
- **Teclado y foco:** control de selección nativo de primera versión; Tab lo
  alcanza y su operación por teclado sigue el comportamiento de selección de
  la plataforma. El cambio no mueve el foco. `focus-visible` usa anillo de
  `3 px`, separación de `2 px` y el token `color.focus.on-light` o
  `color.focus.on-dark` según la superficie adyacente; aparece inmediatamente,
  sin transición ni retraso.
- **Semántica:** etiqueta programática y visible; valor actual anunciado; las
  dos opciones mantienen nombre accesible estable.
- **Responsive:** permanece visible y operable en todos los breakpoints; puede
  pasar bajo el wordmark sin alterar el orden DOM.
- **Usos prohibidos:** banderas, detección sin posibilidad de cambio, menú sin
  semántica de selección, nombres truncados o cambio de foco tras seleccionar.

### Status indicator

- **Propósito:** comunicar estado técnico de disponibilidad o comprobación.
- **Anatomía:** icono o marcador, etiqueta textual obligatoria y detalle breve
  opcional.
- **Variantes:** `neutral`, `checking`, `available`, `degraded` y
  `unavailable`, mapeadas a neutral/loading, success, warning y error.
- **Tamaños:** icono `16 px` o `20 px`; área mínima de lectura con padding
  vertical `8 px` y horizontal `12 px`.
- **Contenido:** términos directos y no clínicamente ambiguos, por ejemplo
  “Servicio disponible” o “Service unavailable”.
- **Tokens:** `text.*`, `info.*`, `success.*`, `warning.*`, `danger.*`,
  `radius.pill` solo para la etiqueta breve, `type.label`, `space.*`.
- **Teclado y foco:** no es interactivo y no entra en el orden de foco. Un
  control asociado usa Button y conserva su propio contrato.
- **Semántica:** texto siempre presente; cambios dinámicos usan anuncio cortés
  cuando corresponda, sin repetir anuncios durante polling.
- **Responsive:** permite wrap del detalle y nunca oculta la etiqueta de
  estado.
- **Usos prohibidos:** punto de color sin texto, terminología clínica para
  salud técnica, animación continua obligatoria o convertirlo en acción.

### Alert/message

- **Propósito:** presentar información, éxito, advertencia o error contextual.
- **Anatomía:** borde semántico, icono, título opcional y cuerpo obligatorio;
  una acción opcional usa Button.
- **Variantes:** `info`, `success`, `warning` y `danger`.
- **Tamaños:** padding `16 px`, gap `12 px`, icono `20 px`, radio `8 px`.
- **Contenido:** título corto, causa comprensible y siguiente paso cuando
  exista; nunca detalles internos o datos sensibles.
- **Tokens:** familias semánticas `info.*`, `success.*`, `warning.*`,
  `danger.*`, `type.body`, `type.heading.sm`, `space.*`, `radius.md`.
- **Teclado y foco:** el contenedor no recibe foco por defecto; sus acciones sí.
  No roba foco al aparecer salvo que una spec futura justifique una interrupción.
- **Semántica:** información y éxito usan estado no interruptivo; un error
  urgente originado tras una acción puede usar anuncio de alerta. Contenido ya
  presente al cargar no debe anunciarse como interrupción.
- **Responsive:** icono y texto conservan orden; acciones pasan debajo del
  mensaje en mobile.
- **Usos prohibidos:** color sin texto/icono, stacks de alertas redundantes,
  mensajes vagos, auto-dismiss de errores o inclusión de stack traces.

### Layout primitives

- **Propósito:** componer interfaces sin duplicar reglas de espaciado y reflow.
- **Primitivas:** `Container` limita ancho y gutters; `Stack` distribuye en eje
  vertical; `Inline` alinea elementos relacionados y permite wrap; `Cluster`
  agrupa acciones; `Grid` usa las columnas documentadas.
- **Anatomía:** contenedor semánticamente neutro, hijos y un gap tomado de la
  escala. La semántica HTML depende del contenido, no de la primitiva.
- **Variantes:** gaps `xs`, `sm`, `md`, `lg`, `xl`; alineación inicial, central
  o final cuando no altere orden de lectura.
- **Tamaños y tokens:** `space.*`, gutters y columnas de breakpoints;
  `Container` ofrece modo `reading` con `size.content.reading` de `720 px` y
  modo `application` con `size.content.application` de `1600 px`. `SPEC-001`
  usa `reading` para la interfaz inicial cuando corresponda. Las primitivas no
  introducen color propio ni habilitan superficies de datos fluidas sin máximo.
- **Teclado, foco y semántica:** no son interactivas, no reciben foco y no
  cambian el orden DOM. No sustituyen landmarks semánticos.
- **Responsive:** reflow por wrap, cambio de columnas o stack; nunca por
  reordenamiento visual distinto del DOM.
- **Usos prohibidos:** márgenes arbitrarios fuera de tokens, orden visual que
  contradiga el DOM, ancho fijo que provoque overflow o primitiva duplicada.

### Matriz de estados

`N/A` significa que el estado no pertenece al contrato del componente; no es
una omisión implícita.

| Componente | default | hover | focus-visible | active | disabled | loading | success | warning | error |
|---|---|---|---|---|---|---|---|---|---|
| Button | Aplica: variante estable | Aplica: puntero | Aplica: anillo | Aplica: presión | Aplica: no operable | Aplica: conserva etiqueta y bloquea repetición | N/A: feedback va en Status/Alert | N/A: feedback va en Status/Alert | N/A: feedback va en Status/Alert |
| Language selector | Aplica: idioma actual | Aplica: puntero | Aplica: anillo | Aplica: selección abierta/accionada | Aplica: solo por causa documentada | N/A: selección local y síncrona | N/A: no representa resultado | N/A: opciones fijas | N/A: opciones fijas; fallo se muestra en Alert |
| Status indicator | Aplica: neutral | N/A: no interactivo | N/A: no recibe foco | N/A: no interactivo | N/A: no es control | Aplica: checking con texto | Aplica: available | Aplica: degraded | Aplica: unavailable |
| Alert/message | Aplica: info | N/A: contenedor no interactivo | N/A: acciones usan Button | N/A: contenedor no interactivo | N/A: no es control | N/A: progreso usa Status | Aplica: success | Aplica: warning | Aplica: danger |
| Layout primitives | Aplica: composición base | N/A: sin presentación interactiva | N/A: no reciben foco | N/A: no interactivo | N/A: no es control | N/A: no representa proceso | N/A: sin semántica de resultado | N/A: sin semántica de resultado | N/A: sin semántica de resultado |

Cada estado aplicable debe tener ejemplo ejecutable en el catálogo. Cada N/A y
su justificación forman parte de la validación de cobertura.

## Contenido y localización

- Idiomas incluidos: español e inglés; inglés es el fallback para idioma no
  soportado, indeterminado o traducción ausente.
- El layout soporta al menos `30%` de expansión general; las etiquetas breves
  pueden crecer hasta `100%` respecto de su texto base.
- Acciones, estados e instrucciones necesarias no se truncan. Se permite wrap
  cuando conserve la comprensión y el tamaño de control; nunca se reduce la
  tipografía para hacer caber texto.
- Se usa lenguaje directo, orientado a la acción y consistente entre módulos.
- Ningún texto se rasteriza dentro de imágenes o iconos.
- Estado, selección, foco y error no dependen solo del color.
- Los estados técnicos usan “disponible”, “degradado”, “no disponible” y sus
  equivalentes ingleses; no usan términos clínicos ambiguos como “estable”,
  “crítico” o “paciente”.
- Los textos visibles proceden del sistema de internacionalización; ejemplos
  del catálogo incluyen ambos idiomas, expansión de texto, pseudolocalización
  y datos exclusivamente sintéticos.

## Catálogo ejecutable y regresión visual

- El catálogo declara la misma versión de diseño que este documento y consume
  los mismos tokens y componentes que la interfaz inicial. No mantiene una
  implementación visual paralela.
- Debe poder ejecutarse localmente y validarse en CI sin incorporar navegación,
  journeys, credenciales ni datos de negocio.
- Incluye el inventario completo de tokens, cada componente mínimo, sus
  variantes, la matriz de estados, un ejemplo por estado aplicable, cada N/A
  justificado y ejemplos en español e inglés.
- Las referencias a token, componente, variante y estado deben resolver contra
  contratos existentes de la misma versión; una referencia ausente o
  incompatible hace fallar la validación.
- Los escenarios responsive cubren cada breakpoint y tamaños intermedios
  representativos, con contenido expandido y sin reordenar el DOM.
- La estrategia visual produce snapshots reproducibles de cada combinación
  aplicable de componente, variante, estado y breakpoint. Cada resultado
  registra versión de diseño, revisión de código, ejecución, navegador y su
  versión, sistema operativo o plataforma y viewport.
- Los controles nativos pueden renderizarse de forma distinta según navegador
  y sistema operativo. Cada navegador y plataforma aprobados se comparan contra
  su propio baseline autorizado; no se exige igualdad de píxeles entre
  plataformas diferentes.
- Entre plataformas se exige equivalencia de contenido, jerarquía, estados,
  interacción, accesibilidad y comportamiento responsive. Una diferencia
  dentro del mismo contexto aprobado de navegador, versión, sistema o
  plataforma y viewport debe detectarse y revisarse.
- Un diff controlado debe ser detectado. Un cambio esperado requiere revisión
  explícita vinculada a la revisión de código antes de actualizar o promover el
  baseline; la actualización silenciosa está prohibida.
- El resultado visual se informa separado de los resultados funcionales, de
  teclado, accesibilidad y seguridad. Cualquier gate fallido mantiene fallida
  la verificación global aunque el snapshot coincida.
- El catálogo queda fuera de artefactos productivos por defecto. Su acceso,
  publicación y evidencia aplican los controles de `security.md`.

| Componente | Variantes visuales mínimas | Estados visuales cubiertos | Breakpoints |
|---|---|---|---|
| Button | `primary`, `secondary`, `quiet`; `default`, `large` | default, hover, focus-visible, active, disabled, loading | mobile, tablet, desktop, wide |
| Language selector | `on-light`, `on-navigation` | default, hover, focus-visible, active, disabled | mobile, tablet, desktop, wide |
| Status indicator | neutral, checking, available, degraded, unavailable | default, loading, success, warning, error | mobile, tablet, desktop, wide |
| Alert/message | info, success, warning, danger | default, success, warning, error | mobile, tablet, desktop, wide |
| Layout primitives | Container, Stack, Inline, Cluster, Grid; gaps aprobados | default y reflow responsive; demás estados N/A | mobile, tablet, desktop, wide |

## Relación trazable

### Cobertura normativa

| Requisito | Decisión de este documento |
|---|---|
| `REQ-001-022` | Tokens concretos de color, tipografía, espacio, tamaño, bordes, radios, sombras, breakpoints y elevación |
| `REQ-001-023` | Button, Language selector, Status indicator, Alert/message y Layout primitives compartidos |
| `REQ-001-024` | Matriz completa de estados aplicables y N/A justificados |
| `REQ-001-025` | Contrato de catálogo con variantes, estados, ejemplos y seguridad |
| `REQ-001-026` | Reutilización obligatoria y prohibición de tokens, estilos o primitivas paralelas |
| `REQ-001-027` | Cobertura visual por componente, variante, estado y breakpoint sin sustituir otros gates |
| `REQ-001-028` | Dirección visual, valores, componentes, responsive, interacción, trazabilidad y aprobación previa a `plan.md` |
| `NFR-001-018` | Teclado, foco visible, semántica, nombre accesible y contraste por estado aplicable |
| `NFR-001-019` | Reflow, contenido, orden DOM/foco y matriz responsive/navegadores |
| `NFR-001-020` | Inventario versionado y catálogo verificable con referencias completas |
| `NFR-001-021` | Baselines revisados y evidencia separada de pruebas funcionales, accesibles y de seguridad |

### Decisiones, consumidores y evidencia

| Decisión o token | Componente consumidor | Criterio AC relacionado | Futura evidencia o prueba |
|---|---|---|---|
| Metadata, dirección visual y aprobación registrada | Todo el sistema visual | `AC-001-042` | `TEST-001-042`: validación versionada de existencia, contenido y aprobación antes de `plan.md` |
| Inventario completo de tokens semánticos | Todos los componentes | `AC-001-043` | `TEST-001-043`: validación de categorías, valores, aplicabilidad y cálculo de contraste |
| Base compartida y consumo exclusivo de tokens | Button, Language selector, Status indicator, Alert/message, Layout primitives | `AC-001-044` | `TEST-001-044`: inspección de componentes y uso en la interfaz inicial |
| Matriz de estados aplicables y N/A | Todos los componentes mínimos | `AC-001-045` | `TEST-001-045`: validación de matriz y ejemplos ejecutables |
| Teclado, foco, semántica, nombre y contraste | Button y Language selector; estados anunciados de Status y Alert | `AC-001-046` | `TEST-001-046`: revisión automatizada y manual dirigida por variante y estado |
| Breakpoints, gutters, columnas y reflow | Layout primitives y todos los componentes | `AC-001-047` | `TEST-001-047`: E2E en breakpoints y tamaños intermedios |
| Tokens y comportamiento independientes del navegador | Todos los componentes | `AC-001-048` | `TEST-001-048`: selección representativa en la matriz aprobada de navegadores |
| Contrato versionado de catálogo y ejemplos | Todos los tokens, componentes y estados | `AC-001-049` | `TEST-001-049`: arranque local y validación equivalente en CI |
| Reutilización de tokens/primitivas y revisión de excepciones | Todo consumidor del sistema compartido | `AC-001-050` | `TEST-001-050`: controles mecánicos y revisión estructural documentada |
| Cobertura visual por variante, estado y breakpoint | Todos los componentes mínimos | `AC-001-051` | `TEST-001-051`: ejecución reproducible, diff controlado y aprobación vinculada |
| Independencia de gates visuales | Todos los componentes interactivos | `AC-001-052` | `TEST-001-052`: fallo funcional, de teclado, accesibilidad o seguridad conserva resultado global fallido |

## Seguridad del sistema visual

Este documento aplica los controles de `security.md` sin duplicar su threat
model:

- el catálogo se limita a ejecución local o CI y queda fuera de artefactos
  productivos por defecto;
- ejemplos, estados, snapshots, baselines y diffs usan exclusivamente datos
  sintéticos;
- recursos y destinos externos se deniegan por defecto y requieren procedencia
  y autorización documentadas;
- secretos, credenciales, URLs internas, identificadores reales y datos
  sensibles están prohibidos en ejemplos, contenido renderizado, snapshots,
  diffs y metadatos;
- la versión aprobada de `ui-design.md`, el catálogo y la implementación deben
  concordar; las excepciones de reutilización requieren justificación completa;
- la regresión visual nunca sustituye ni desactiva gates funcionales, de
  teclado, accesibilidad o seguridad.

## Fuera de alcance

- Logo definitivo o isotipo.
- Rediseño corporativo completo, guía de marketing y materiales comerciales.
- Tema oscuro completo.
- Formularios complejos.
- Tablas y superficies de datos compactas.
- Dashboard.
- Navegación de negocio.
- Componentes propios de dominios futuros.
- Selección de framework UI, librería de componentes, herramienta de catálogo,
  herramienta de regresión visual, proveedor o servicio.

## Preguntas abiertas

Ninguna. `SPEC-001`, sus criterios aprobados, su diseño de seguridad y la
dirección aprobada por Product Owner permiten resolver todas las decisiones
materiales de esta versión. Cualquier cambio material posterior exige actualizar
y volver a aprobar este documento antes de redactar `plan.md`.

## Aprobación del diseño visual

- **Estado:** `Aprobado`
- **Aprobado por:** `Product Owner`
- **Fecha:** `2026-09-20`
- **Comentario o referencia:** `Aprobación explícita del diseño visual SimuCenter Clinical Precision de SPEC-001 durante la revisión SDD.`

La existencia de este archivo por sí sola no constituye aprobación; esta versión
cuenta con la aprobación explícita de Product Owner registrada en el bloque
anterior.
