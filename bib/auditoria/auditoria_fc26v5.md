# Auditoría técnico-editorial de `fc26v5.qmd` y propuesta de reestructuración (v.6)

**Objeto:** *Filosofía y conocimiento*. Monografía técnica 26311M9.
**Fichero auditado:** `fc26v5.qmd` — 6.002 líneas, 721.554 bytes, ~110.000 palabras.
**Contraste normativo:** Guía docente 26311M9, curso 2025/26 (aprobada 23/06/2025), excluidos Temas 7 y 8.
**Fecha de auditoría:** 15/08/2026.

---

## 0. Resumen ejecutivo

El material tiene masa crítica y densidad documental muy por encima de lo habitual en un manual de asignatura: **887 URLs, 247 DOI distintos, 655 referencias numeradas repartidas en 103 bloques desplegables, 199 entradas en la bibliografía final, 215 filas de tabla, 30 estudios de caso indexados y un glosario de ~6.700 palabras.** La respuesta a la pregunta planteada es afirmativa y verificable: **son cientos de fuentes y sí están distribuidas por bloques y secciones, además del listado final**.

El problema no es la cantidad de fuentes sino que **el aparato crítico está formado por dos sistemas paralelos que no se comunican**, y que **el nivel de elaboración varía en un factor de 25 entre secciones**. Sobre esa base, cinco frentes de trabajo, en orden de retorno:

| # | Frente | Estado | Severidad |
|---|--------|--------|-----------|
| 1 | Gestión bibliográfica integrada | Inexistente (0 citas Pandoc) | **Crítica** |
| 2 | Navegación interna y anclas | 29 de 30 enlaces del índice de Casos rotos | **Crítica** |
| 3 | Alineación con guía docente | Desalineación en Temas 4 y 5 | **Alta** |
| 4 | Calidad desigual del contenido | 8 secciones con aspecto sintético | **Alta** |
| 5 | Tema claro/oscuro y accesibilidad | Bootswatch sin capa propia; bug en cabecera | **Media-alta** |

---

## 1. Gestión bibliográfica: el hallazgo principal

### 1.1. Situación medida

```
Bloques <details> ...................................... 103
Referencias numeradas dentro de esos bloques ........... 655
DOI distintos en todo el documento ..................... 247
Entradas <p> en la sección # Bibliografía .............. 199
DOI presentes en # Bibliografía ........................  92
DOI citados en secciones y AUSENTES de # Bibliografía .. 155   (63 %)
Claves autor-año en bloques de sección ................. 371
   de ellas, también en # Bibliografía .................  68   (18 %)
   de ellas, SOLO en las secciones ..................... 303   (82 %)
Citas en sintaxis Pandoc [@clave] ......................   0
Marcas de nota <sup>N</sup> ............................ 219
```

**Lectura:** la bibliografía final no es la consolidación de las fuentes usadas, sino **una lista independiente** que comparte con el cuerpo del texto menos de la quinta parte de sus referencias. Un lector que busque en `# Bibliografía` la fuente de la nota `<sup>7</sup>` de §3.4 no la encontrará en 4 de cada 5 casos. Para un proyecto con horizonte de distribución internacional, es la brecha que más compromete la credibilidad: es exactamente lo que un revisor externo comprueba primero.

### 1.2. Problemas asociados

- **Sin infraestructura de citación.** No hay `bibliography:` ni `csl:` en el YAML, no hay fichero `.bib`, no hay `nocite`. Todo es HTML manuscrito. Cualquier corrección de una referencia usada en tres apartados exige tres ediciones manuales sin garantía de consistencia.
- **Estilos mezclados.** Conviven Chicago autor-fecha (`Abramo, Giovanni, Andrea Ciriaco D'Angelo, y Gianluca Murgia. 2017. "…"`), APA 7 (`Albareda, J. L. (2025). …`), y variantes con/sin punto final, `y` frente a `&`, año entre paréntesis o tras el nombre. Un CSL resuelve esto de una vez.
- **Notas voladizas.** Las 219 marcas `<sup>N</sup>` no son enlaces: no llevan a ninguna parte y no tienen retorno. En un documento de 110.000 palabras esto obliga al lector a desplegar manualmente el bloque, contar hasta N y volver a buscar dónde estaba.
- **Bibliografía como HTML crudo.** `<div id="referencias">` con un `<style>` embebido que fija sangría francesa mediante `text-indent` — y que **no responde al tema oscuro**, porque no define color y depende de la herencia.
- **Referencias con riesgo de fabricación.** Al menos dos entradas del bloque de §4.1 tienen la morfología típica de referencia sintética no verificada (autores plausibles, revista plausible, paginación plausible). Dado que la propia Introducción documenta el uso de LLM en la elaboración, **una pasada de verificación DOI-a-DOI sobre las 655 referencias de sección no es opcional**: es el requisito de integridad que el propio texto se autoimpone en §4.7 de la guía.

### 1.3. Propuesta

**Migración a bibliografía gestionada, en tres capas, sin perder el diseño actual de bloques desplegables.**

1. **Capa de datos.** Un `referencias.bib` único (exportable desde Zotero, que ya es la herramienta recomendada en la guía docente). Clave canónica `apellidoAAAApalabra`. Se construye automáticamente: los 247 DOI se resuelven vía `doi.org` con `content-negotiation` a BibTeX en un script R (`rcrossref::cr_cn()`), y las entradas sin DOI se completan a mano. Estimación: 480–520 entradas consolidadas tras deduplicar.
2. **Capa de citación.** Sustituir `<sup>N</sup>` por `[@clave]` / `[@clave1; @clave2]`. Quarto genera entonces enlaces bidireccionales, tooltip de referencia al pasar el cursor (`link-citations: true`) y una bibliografía final **generada, completa y ordenada**, con estilo único.
3. **Capa de presentación.** Mantener los `<details>` como **"lecturas del apartado" comentadas** —que es su valor real y diferencial— pero limitados a 6–10 entradas seleccionadas con una línea de justificación cada una, en lugar de volcados de 20-30 referencias sin jerarquía. Las fuentes restantes viven en el `.bib` y afloran en la bibliografía final.

```yaml
bibliography: referencias.bib
csl: apa-7-es.csl          # o chicago-author-date-es.csl
link-citations: true
citations-hover: true
reference-section-title: "Bibliografía"
```

**Ganancia:** una sola fuente de verdad, estilo homogéneo, navegación bidireccional, bibliografía final exhaustiva por construcción, y posibilidad de exportar a PDF/LaTeX o a JATS sin rehacer nada.

**Coste:** es el trabajo más pesado del proyecto. Automatizable al 70-80 % con un script R que detecte patrón autor-año en los bloques y proponga la clave; el 20-30 % restante requiere decisión humana caso por caso.

---

## 2. Navegación interna: 29 de 30 anclas rotas

El índice `# Casos` enumera 30 estudios de caso con enlaces internos. **29 no resuelven.** Los identificadores esperados (`#mapas-fiables-de-riesgo-de-inundacion-y-gestion-de-licencias-de-actividad`, `#pseudoscience-and-the-future-of-neuroscience`, etc.) corresponden a títulos que viven dentro de etiquetas `<summary>`, y **`<summary>` no genera anclas** en el HTML renderizado. El índice de casos —una de las mejores ideas del documento, y explícitamente anunciada en la Introducción— está inoperativo.

**Solución:** dar identidad estructural a los casos. Cada estudio de caso pasa a ser un bloque con ancla propia y clase semántica:

```markdown
::: {#caso-dana-valencia .estudio-caso}
### Caso 2. Gestión del riesgo y la DANA de Valencia (29/10/2024) {.unnumbered}
::: {.contenido}
…
:::
:::
```

Esto permite además: numeración automática de casos, listado generado en lugar de manual, estilo unificado en claro/oscuro, y —lo importante para el uso docente— **enlace estable y citable a cada caso individual**, que es lo que un profesor comparte con un grupo de trabajo.

---

## 3. Alineación con la guía docente 26311M9

### 3.1. Correspondencia actual

| Guía docente (Temas 1-6) | Capítulo `.qmd` | Estado |
|---|---|---|
| **T1.** Génesis y condiciones de posibilidad | **Cap. 1** (11 apartados) | Cubierto y ampliado |
| **T2.** Conocimiento científico e instrumentalización | **Cap. 2** (4 apartados) | Cubierto |
| **T3.** Consenso, escepticismo y negacionismo climático | **Cap. 3** (6 apartados) | Cubierto, correspondencia casi literal |
| **T4.** Intereses y sesgos en la producción de conocimiento | **Cap. 4** (parcial) | **Desalineado** |
| **T5.** Comunicación, divulgación y transferencia | **Cap. 4 + Cap. 5** (mezclados) | **Desalineado** |
| **T6.** De la sociedad de la información a la del conocimiento | **Cap. 6** (8 apartados) | Cubierto y ampliado |

El desajuste se concentra en la bisagra T4/T5. El capítulo 4 del `.qmd` se titula *Comunicación y difusión del conocimiento* —que es el enunciado del **T5**— pero su contenido (índices de impacto, plataformas de acceso, alfabetización científica) pertenece mayoritariamente al **T4**. El capítulo 5, *Acceso y apropiación*, absorbe el resto del T5.

### 3.2. Contenidos de la guía sin desarrollo en el `.qmd`

Verificados por búsqueda directa:

- **T4.1** Infraestructura y financiación de la investigación. Redes de conocimiento. — *sin sección propia*
- **T4.5** Limitaciones del proceso de revisión por pares: análisis de casos. — *hay casos de retractación dispersos en §4.3, pero no la sección*
- **T4.7** Integridad científica, responsabilidad profesional y ética de la investigación y comunicación. — *solo un vídeo enlazado en §1.10*
- **T4.8** Agentes cocientíficos de IA y automatización del proceso de investigación (revisión, hipótesis, diseño experimental). — *§6.8 trata agentes de IA de nivel doctoral, pero desde la sociedad del conocimiento, no desde el proceso de investigación*
- **T4.9** Conformidad bajo coacción y dinámicas de cancelación: vectores de irracionalidad en contexto académico. — *tratado tangencialmente en el Epílogo*
- **T5.6** Fomento de la investigación: *reality-based community* vs grupos de presión. — *ausente como tal*

Además, los **cinco temas de taller** de la guía (geopolítica y conflicto; ciberseguridad, desinformación y vigilancia; modelos ético-epistemológicos en psiquiatría; sistemas robóticos e IA; clima, energía y sostenibilidad) no tienen contrapartida estructurada en la monografía. Los talleres 4 y 5 están bien servidos por los capítulos 6 y 3 respectivamente; el 1 y el 2 no lo están; el 3 queda fuera del alcance (Prof. 2).

### 3.3. Propuesta de reestructuración

**Opción A — Conservadora (recomendada).** Mantener seis capítulos y reordenar la bisagra:

- **Cap. 4 → *Intereses, sesgos e integridad en la producción de conocimiento*** (= T4 completo)
  - 4.1 Infraestructura, financiación y redes de conocimiento **[nueva]**
  - 4.2 Cauces formales (ex-4.1, reelaborada)
  - 4.3 Cauces informales (ex-4.2)
  - 4.4 Índices de impacto y evaluación de la calidad (ex-4.3)
  - 4.5 Límites de la revisión por pares: análisis de casos **[nueva; absorbe los casos de retractación hoy dispersos]**
  - 4.6 Plataformas de acceso y apropiación de resultados (ex-4.4)
  - 4.7 Integridad científica y ética de la investigación **[nueva]**
  - 4.8 Agentes de IA en el ciclo de investigación **[nueva; hereda material de §6.8]**
  - 4.9 Conformidad bajo coacción y dinámicas de cancelación **[nueva; hereda del Epílogo]**
- **Cap. 5 → *Comunicación, acceso y transferencia del conocimiento*** (= T5 completo)
  - 5.1–5.5 actuales + 5.6 *Reality-based community* vs grupos de presión **[nueva]**
  - Incorpora aquí §4.5 actual (alfabetización científica y percepción pública), que es materia de comunicación, no de sesgos
- **Cap. 6** sin cambios estructurales, aligerado de §6.8 (que migra parcialmente a 4.8)

**Opción B — Ambiciosa.** Reorganizar en cuatro partes temáticas (I. Fundamentos epistemológicos; II. Ciencia, controversia y consenso; III. Producción, evaluación y circulación; IV. Conocimiento, tecnología y sociedad) con capítulos dentro. Mejor arquitectura para el horizonte de monografía internacional, pero rompe la correspondencia 1:1 con la guía docente que hoy facilita el uso en aula. **Viable si se acompaña de una tabla de equivalencias tema↔sección al inicio.**

**Recomendación:** Opción A para la v.6, con la tabla de equivalencias de la Opción B como puente. Reservar la reorganización en partes para una eventual edición en inglés, donde la guía docente deja de ser la restricción.

**Añadido sugerido:** una sección `# Guía de uso docente` (no numerada, tras la Introducción) con la tabla Tema→Capítulo→Casos→Lecturas obligatorias. Es lo que convierte 110.000 palabras en un instrumento de preparación íntegra de la materia, que es el uso inmediato declarado.

---

## 4. Calidad desigual: dónde se nota el "contenido sintético"

### 4.1. Medición

Palabras por apartado, ordenadas. La densidad documental (enlaces y bloques de referencia por cada 1.000 palabras) es el indicador que separa elaboración propia de relleno.

**Apartados infra-elaborados** (baja extensión + baja densidad + prosa definicional sin tesis):

| Apartado | Palabras | Enlaces | Bloques ref. | Síntoma |
|---|---|---|---|---|
| **1.2** Nociones básicas de epistemología | 301 | 1 | 0 | Definición de diccionario + lista de 9 preguntas retóricas + dicotomía general/particular. Sin una sola fuente. Es el apartado más débil del libro y ocupa una posición fundacional. |
| **4.1** Cauces formales | 617 | 5 | 1 | Enumeración enciclopédica ("Los congresos científicos suelen…" ×4). Referencias no verificadas. |
| **6.3** Del acceso a la participación activa | 651 | 6 | 1 | Prosa ensayística sobre "trabajadores del conocimiento" sin dato, sin caso, sin controversia. Referencias del bloque no coinciden con el contenido del apartado. |
| **1.3** Versiones del escepticismo | 469 | 4 | 1 | Tipología sin desarrollo; no conecta con §3.1, que es donde el escepticismo se vuelve operativo. |
| **5.2** Quién se beneficia de los resultados | 1.012 | 6 | 2 | Planteamiento correcto sin caso que lo sostenga (el caso del ébola está en otra sección). |
| **3.2** Credibilidad de los expertos | 885 | 7 | 1 | Apoyado casi solo en el consenso del 97 %; no incorpora la literatura posterior sobre confianza epistémica. |
| **5.4** Modelo *open-access* y CC | 935 | 12 | 1 | El callout de licencias CC es bueno; el resto es descriptivo y se detiene en 2002 (BOAI) sin llegar a Plan S ni a los acuerdos transformativos. |
| **1.1** Tipos de conocimiento | 420 | 1 | 0 | Tipología escolar; su desarrollo real está en subsecciones mal jerarquizadas (ver §5). |

**Contraste:** §1.11 *Intrusismo profesional* (7.023 palabras, 33 enlaces, 9 bloques, 30 filas de tabla, 28 notas) y Anexo I.2 (8.281 palabras, 65 enlaces, 40 notas). **Relación 1:25 entre el apartado más y el menos elaborado.** Un lector externo percibe el libro como irregular: pasa de un ensayo documentado a una entrada de enciclopedia y vuelve.

### 4.2. Criterio de reelaboración

Cada apartado infra-elaborado debe alcanzar, como mínimo:

1. **Una tesis explícita** en el primer párrafo (qué se sostiene, no qué se define).
2. **Una controversia viva** con al menos dos posiciones identificadas y atribuidas.
3. **Un caso o dato verificable** con fuente primaria.
4. **Densidad ≥ 6 referencias por 1.000 palabras**, al nivel del resto del libro.
5. **Conexión explícita** hacia adelante y hacia atrás (§1.3 debe apuntar a §3.1; §5.2 al caso del ébola).

Aplicado a §1.2, por ejemplo: de "la epistemología estudia el conocimiento" se pasa a articular el apartado sobre el problema real —**el giro de la epistemología individualista a la social y aplicada**— con Goldman, Longino, Fricker y Coady, que es además el marco que el resto del libro presupone sin haberlo establecido. Ese apartado es hoy una deuda estructural, no solo un apartado corto.

---

## 5. Jerarquía de encabezados: bug estructural en el capítulo 1

Cuatro encabezados de nivel 2 cuelgan lógicamente de §1.1 pero compiten con ella en el índice:

```
## 1.1. Tipos de conocimiento y metodologías asociadas    (420 palabras)
## Matices:                                               (284)      ← debería ser ###
## Componente problemático                                (239)      ← debería ser ###
## Validación problemática                                (153)      ← debería ser ###
## Relación entre emociones y procesos cognitivos       (3.137)      ← debería ser 1.2
```

Efecto en el TOC lateral: entre "1.1" y "1.2" aparecen cuatro entradas sin numerar, una de ellas —*Relación entre emociones y procesos cognitivos*, con 3.137 palabras, 18 filas de tabla y 4 bloques de referencias— **más extensa que la mayoría de apartados numerados del libro**. Es materialmente un apartado de pleno derecho disfrazado de subepígrafe. Además, "Matices:" con dos puntos finales es un encabezado provisional que se quedó.

**Corrección:** renumerar el capítulo 1 con `###` para los tres primeros y promover el cuarto a **§1.2 *Emoción, cognición y racionalidad***, desplazando la numeración posterior. Con `number-sections: true` en el YAML, la numeración pasa a ser automática y este tipo de deriva deja de ser posible.

---

## 6. Tema claro/oscuro: de dos Bootswatch a un sistema

### 6.1. Situación

```yaml
theme:
  dark: cyborg      # Bootswatch: fondo #060606, Roboto, escala propia
  light: journal    # Bootswatch: fondo #fff, News Cycle, otra escala
```

Son dos temas de terceros con **familias tipográficas distintas, escalas tipográficas distintas y paletas sin relación**. Al alternar, no cambia solo el fondo: cambia la tipografía, el interlineado y el ritmo vertical. Para un documento de lectura larga es una disrupción notable, y es la principal razón por la que el resultado "no parece" una edición profesional aunque el contenido lo sea.

Consecuencias medibles en el documento:

- El `<style>` de `#referencias` fija sangría pero **no color**: en oscuro depende enteramente de la herencia de `cyborg`.
- Los **103 bloques `<details>`** no tienen estilo propio: heredan el `summary` por defecto del navegador, sin indicador de estado ni foco visible.
- Los **655 listados de referencias van dentro de bloques ` ```markdown `**, es decir, se renderizan como *código fuente resaltado*. En `cyborg` el resaltado de sintaxis colorea aleatoriamente fragmentos de las citas (nombres propios, años, comillas) porque el resaltador intenta interpretar Markdown. Visualmente es el defecto más llamativo del documento en modo oscuro.
- Las **22 callouts** y las **215 filas de tabla** heredan colores de dos sistemas que no comparten variables de contraste.

### 6.2. Propuesta

**Un tema propio en dos ficheros SCSS que comparten variables**, en lugar de dos temas ajenos:

```yaml
theme:
  light: [journal, fc-light.scss]
  dark:  [cyborg,  fc-dark.scss]
respect-user-color-scheme: true
```

Con un `_variables.scss` común que fije:

- **Una sola familia tipográfica** para ambos modos, con escala modular idéntica (p. ej. serif de lectura para el cuerpo —Source Serif 4, Charter— y sans para interfaz). El cambio de tema debe alterar **solo color**, nunca métrica.
- **Pares de contraste verificados AA/AAA** (cuerpo ≥ 7:1, secundario ≥ 4.5:1) en ambos modos, comprobados programáticamente sobre la paleta, no a ojo. En oscuro, fondo `#12151a` en lugar del `#060606` de cyborg: el negro puro sobre texto claro produce *halation* y es peor para lectura larga que un gris muy oscuro.
- **Componentes estilados explícitamente**: `<details>/<summary>` con affordance de despliegue, foco visible y transición; bloques de referencias **fuera de ` ```markdown `** (ver abajo); callouts con la misma paleta semántica en ambos modos; tablas con zebra sutil y cabecera fija.
- **Sustituir ` ```markdown ` por un div con clase `.bloque-referencias`** con botón de copia propio. Se conserva la función que motivó la elección (copiar el bloque de un clic, pegar en Zotero/Obsidian) sin el efecto colateral del resaltado de sintaxis. Es un cambio de ~10 líneas de SCSS + ~15 de JS y resuelve simultáneamente estética, legibilidad y accesibilidad.

### 6.3. Accesibilidad: incidencias concretas

- **Bug real en el YAML:** el bloque `include-in-header` **abre con `</script>`** (etiqueta de cierre sin apertura, línea 25). El navegador la descarta, pero es HTML inválido que fallará cualquier validación W3C —requisito habitual en depósitos institucionales.
- La hoja de Font Awesome está **entrecomillada dentro del bloque `text:`** (`"<link rel=…>"`), por lo que las comillas se emiten como texto en el `<head>`.
- **Font Awesome 4.7.0** (2016) está descontinuada; las clases `fa fa-video-camera` han cambiado en v6. Migrar o sustituir por iconos SVG inline (mejor para `embed-resources: true`).
- **`<sup>` sin semántica**: deben ser `<a>` con `role="doc-noteref"`. Resuelto automáticamente al migrar a citas Pandoc.
- **Fragmentos en inglés sin `lang="en"`**: el glosario contiene definiciones íntegras en inglés dentro de un documento `lang: es`. Los lectores de pantalla los pronuncian con fonética española. Afecta a decenas de entradas.
- **Texto alternativo heterogéneo** en 23 imágenes Markdown y 8 `<img>`: algunos alt son descriptivos, otros repiten el pie, otros son el prompt de generación (`"CPU con carcasa en forma de libro - Gemini 2.5 Flash, 11/09/2025"` es procedencia, no descripción).
- Faltan **skip-link** al contenido principal y `aria-label` en el TOC flotante.
- Con `embed-resources: true` y ~110.000 palabras, conviene medir el peso del HTML final: por encima de 8-10 MB la carga en móvil y la función de búsqueda del navegador se degradan. Alternativa: `self-contained` para el depósito en Zenodo y una versión con recursos externos para el despliegue web.

---

## 7. Metadatos e identidad de la obra

Incoherencias detectadas:

| Campo | Valor actual | Observación |
|---|---|---|
| `subtitle` | "Monografía técnica 26311M9, v.4 - 2025" | La guía docente vigente es 2025/26 |
| `date` | "10/07/2025" | Anterior a los Agradecimientos |
| Agradecimientos | "En Granada, a 25 de septiembre de 2025" | Tercera fecha distinta |
| Sobre esta monografía | "**Versión**: 4.0 (Octubre 202)" | **Errata**: año truncado |
| Cómo citar | "v.4 - 2025", DOI `10.5281/zenodo.17284805` | Debe actualizarse al DOI de la nueva versión |

Además, el bloque *Sobre esta monografía* afirma que "el código R incluido requiere los paquetes listados en el *chunk* de configuración inicial": hay **5 chunks R** en el documento pero **no existe chunk de configuración inicial** con `library()`. O se añade, o se corrige la afirmación. Para un documento que reclama reproducibilidad, es una promesa incumplida verificable en 10 segundos.

**Elementos ausentes que se esperan en edición científica profesional:**

- `citation:` en el YAML (Quarto genera automáticamente metadatos CSL, `schema.org` y bloque "Cómo citar" a partir de él — sustituye al bloque manual actual y garantiza coherencia con Zenodo).
- `lang` y `keywords` estructurados para indexación.
- **Abstract en español e inglés.** Dado el tráfico documentado desde países anglófonos y latinoamericanos, un `abstract` bilingüe y un `# Extended summary` en inglés al final multiplican la recuperabilidad del documento sin traducirlo entero.
- **Declaración de uso de IA.** La Introducción lo documenta de forma ejemplar en prosa; convertirlo en una sección formal etiquetada (siguiendo, p. ej., las recomendaciones COPE) es coherente con el T4.7 de la guía y refuerza la posición del texto.
- **ORCID/ROR** ya están en el YAML — bien.

### 7.1. Enlaces a proyectos previos

Las instrucciones del proyecto piden enlazar los desarrollos ya renderizados y desplegados (*Noise and Risk*, *Reversible Ageing*, entre otros). **Búsqueda directa: no aparecen en el `.qmd`.** Sí hay enlaces a los asistentes de razonamiento en `ugr.es/~mm3`, a Zenodo (DOI propios) y a despliegues en Vercel. Propuesta: una sección `# Proyectos relacionados` antes del Epílogo, con tarjetas enlazadas (título, una línea de descripción, DOI/URL), e inserción contextual de cada proyecto en el apartado donde es pertinente —*Noise and Risk* encaja naturalmente en §3.6 y en el Anexo I (donde ya hay una referencia sobre ruido antropogénico, Kok et al. 2023, sin conectar con el proyecto propio).

---

## 8. Plan de trabajo propuesto

Ordenado por relación impacto/esfuerzo. Los frentes 1-3 son independientes entre sí y paralelizables.

### Fase 1 — Infraestructura (no toca contenido)
1. Corregir bug de `include-in-header` y sintaxis de Font Awesome.
2. Activar `number-sections`, corregir jerarquía del cap. 1, resolver metadatos y erratas.
3. Añadir `citation:`, abstract bilingüe, keywords.
4. **Entregable:** `fc26v6.qmd` que renderiza idéntico pero válido, más un informe de render limpio.

### Fase 2 — Bibliografía (el trabajo pesado)
5. Script R de extracción: parsear los 103 bloques, resolver 247 DOI vía Crossref, generar `referencias.bib` provisional.
6. Verificación: comprobar las 655 referencias, marcar las no resueltas para revisión manual, detectar posibles fabricaciones.
7. Sustitución progresiva de `<sup>N</sup>` por `[@clave]`, capítulo a capítulo.
8. Selección de CSL y consolidación de la bibliografía generada.
9. **Entregable:** `referencias.bib` + informe de verificación (resueltas / dudosas / no localizadas).

### Fase 3 — Navegación y casos
10. Convertir los 30 estudios de caso en bloques con ancla, clase y numeración automática.
11. Regenerar el índice `# Casos` desde el propio documento.
12. Revisar y actualizar los casos con datos posteriores a su redacción (retractaciones, percepción pública, IA generativa: los tres bloques que más han envejecido).

### Fase 4 — Contenido
13. Reestructuración caps. 4-5 según Opción A; redacción de las 6 secciones ausentes de la guía.
14. Reelaboración de los 8 apartados infra-elaborados según el criterio de §4.2.
15. Actualización de literatura por capítulo (2024-2026), con foco en: epistemología social y testimonio (cap. 1), *agnotology* y desinformación (cap. 2), atribución de eventos extremos (cap. 3), integridad y IA en investigación (cap. 4), Plan S y acuerdos transformativos (cap. 5), gobernanza de IA (cap. 6).
16. Sección `# Guía de uso docente` con tabla de equivalencias.
17. Sección `# Proyectos relacionados`.

### Fase 5 — Diseño y accesibilidad
18. `_variables.scss` + `fc-light.scss` + `fc-dark.scss` con contraste verificado programáticamente.
19. Componente `.bloque-referencias` con botón de copia, sustituyendo ` ```markdown `.
20. Auditoría de accesibilidad: alt, `lang` en fragmentos, skip-link, foco, orden de tabulación.
21. Verificación final: validación W3C, contraste AA/AAA, peso del HTML, prueba en lector de pantalla.

---

## 9. Decisiones que requieren criterio del autor

1. **Alcance de la reestructuración**: Opción A (conservadora, alineada con la guía) u Opción B (cuatro partes temáticas, mejor para edición internacional).
2. **Estilo de citación**: APA 7 (dominante en ciencias sociales hispanohablantes, coherente con parte de la bibliografía actual) o Chicago autor-fecha (dominante en filosofía anglosajona, coherente con la otra parte). Determina el CSL y el trabajo de normalización.
3. **Destino de los bloques desplegables**: ¿se conservan como "lecturas comentadas del apartado" (6-10 entradas seleccionadas) o se suprimen en favor de una bibliografía final exhaustiva con citas en el texto? La primera opción conserva el valor docente diferencial; la segunda es el estándar de monografía académica.
4. **Fichero único vs proyecto Quarto Book**: 6.002 líneas en un `.qmd` hace la edición frágil. Un proyecto `_quarto.yml` con un `.qmd` por capítulo mantiene la salida HTML única (`output-file`) y hace el trabajo por capítulos mucho más seguro. Recomendado, pero cambia el flujo de trabajo en RStudio.
5. **Versión en inglés**: ¿objetivo a medio plazo o se prioriza el uso docente inmediato en español?
