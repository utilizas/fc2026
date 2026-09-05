# Fase 2 — Bibliografía integrada: informe de extracción y verificación

**Proyecto:** *Filosofía y conocimiento*. Monografía técnica 26311M9 (v.6)
**Fuente:** `fc26v5.qmd` — 6.002 líneas, 110.000 palabras
**Estilo destino:** APA 7 (español) · **Decisión estructural:** Opción A · **Bloques desplegables:** se conservan como lecturas comentadas
**Fecha:** 15/08/2026

---

## 1. Qué se ha hecho

Pipeline determinista en cuatro pasos, reproducible sobre el `.qmd`:

```
fc26v5.qmd
   ├─ extract.py    parseo de 103 bloques <details> + <div id="referencias">   → 802 apariciones
   ├─ classify.py   clasificación, normalización, deduplicación, claves de cita → 616 referencias únicas
   ├─ makebib.py    generación BibLaTeX con trazabilidad y marcas de control    → referencias.bib
   └─ cotejo.py     cotejo contra metadatos autorizados de Crossref             → cotejo_crossref.csv
```

La verificación contra Crossref se hizo consulta a consulta (174 DOI), no por muestreo.

---

## 2. Resultado cuantitativo

### 2.1. Volumen

| Magnitud | Valor |
|---|---:|
| Apariciones de referencia analizadas | 802 |
| Descartadas por no ser referencia (definiciones y viñetas de prosa dentro de bloques) | 101 |
| Apariciones válidas | 701 |
| **Referencias únicas tras deduplicar** | **616** |
| Referencias citadas en más de una sección | 84 |
| DOI distintos | 174 |
| Secciones con bloque de referencias propio | 42 |

Se confirma lo que planteabas: **son cientos de fuentes y están efectivamente distribuidas por secciones**, no concentradas en el listado final. El reparto por capítulo:

| Capítulo | Referencias |
|---|---:|
| 1. Génesis y condiciones de posibilidad | 212 |
| 6. De la sociedad de la información a la del conocimiento | 109 |
| Anexo I (sobreturismo) | 74 |
| 4. Comunicación y difusión | 55 |
| 5. Acceso y apropiación | 52 |
| 3. Consenso, escepticismo y negacionismo climático | 44 |
| 2. Conocimiento científico e instrumentalización | 42 |
| Epílogo + Sobre esta monografía | 15 |

### 2.2. El hallazgo estructural

| Situación | Referencias | % |
|---|---:|---:|
| **Solo en secciones** — citadas en el texto, ausentes del listado final | **417** | 68 % |
| **Solo en el listado final** — nunca citadas en ninguna sección | **122** | 20 % |
| En ambos sitios | 77 | 12 % |

Dos consecuencias directas:

1. **Dos de cada tres fuentes que sustentan el texto no aparecen en `# Bibliografía`.** Un lector que verifique una nota no la encuentra.
2. **Una de cada cinco entradas del listado final no respalda ninguna afirmación del libro.** Son 122 entradas que un revisor externo leerá como bibliografía de relleno.

La bibliografía gestionada resuelve las dos a la vez: la lista final se *genera* a partir de las citas, de modo que por construcción contiene todo lo citado y nada más (salvo lo que se añada deliberadamente vía `nocite`).

---

## 3. Verificación contra Crossref

### 3.1. Estado

| Estado | Refs. | Comentario |
|---|---:|---|
| **Verificado** contra metadatos autorizados | 131 | Título, revista, autor y año confirmados |
| **Discrepancia detectada** | 20 | Requieren decisión, detalle abajo |
| **Pendiente** | 23 | Límite de peticiones de Crossref (429), no ausencia del registro |
| Sin DOI (no verificable por esta vía) | 442 | Libros, capítulos, prensa, recursos web |

Los 151 DOI resueltos lo fueron consulta a consulta. **Los 23 pendientes no son referencias dudosas**: la consulta se cortó por límite de peticiones desde una IP compartida tras ~200 llamadas. Incluyen identificadores tan establecidos como el de la declaración PRISMA (`10.1371/journal.pmed.1000097`) y tus propios depósitos en Zenodo. El script `completar_verificacion.R` los resuelve en local en un par de minutos.

### 3.2. Discrepancia crítica: un DOI que no corresponde a la referencia

Una sola, pero es la que importa:

> **§1.8 «El desarrollo de la medicina científica»**
>
> **El `.qmd` cita:** Gómez-García, F. et al. (2019). *Systematic reviews and meta-analyses on psoriasis: role of funding sources, conflict of interest and efficacy of therapies*. British Journal of Dermatology. `https://doi.org/10.1111/bjd.17109`
>
> **Ese DOI resuelve a:** Moscarella (2018). *Image Gallery: Dermoscopy of lichen amyloidosis*. British Journal of Dermatology.

El artículo de Gómez-García sobre psoriasis existe y es real; el DOI adjunto pertenece a otro trabajo de la misma revista. Es la firma característica de una referencia compuesta sin verificar: autor real + título real + identificador incorrecto. Aparece precisamente en la sección dedicada al rigor metodológico en medicina, lo que la hace especialmente costosa si la detecta un lector antes que tú.

**Acción:** localizar el DOI correcto del artículo de Gómez-García y sustituirlo.

### 3.3. Discrepancias de año (19 casos)

Casi todas responden al mismo patrón: el `.qmd` recoge el año de aparición *online-first* o el de la reimpresión, y Crossref el del número o la edición de registro.

| Clave | Año en `.qmd` | Año de registro | Sección |
|---|:--:|:--:|---|
| `hmielowski2014attack` | 2014 | 2013 | 3.2 |
| `haustein2015bibliometrics` | 2015 | 2014 | 4.3 |
| `leydesdorff2017spots` | 2017 | 2016 | 4.3 |
| `molas-gallart2016towards` | 2016 | 2015 | 5.2 |
| `wei2020research` | 2020 | 2019 | 5.4 |
| `he2013retraction` | 2013 | 2012 | 2.4 |
| `covello2022development` | 2022 | 2021 | 1.6 |
| `theocharis2023platform` | 2023 | 2022 | listado final |
| `williams2022signalling` | 2022 | 2021 | listado final |
| `rapeli2013conception` | 2013 | 2014 | listado final |
| `kjell2023beyond` | 2023 | 2024 | 6.8 |
| `oreskes2021trust` | 2021 | 2019 | listado final |
| `ladyman2001understanding` | 2001 | 2012 | listado final |
| `boland1996scientific` | 1996 | 2005 | 3.4 |
| `aerts2023integrating` | **2023** | **2018** | 1.6 |
| *(y 4 más en `cotejo_crossref.csv`)* | | | |

Aisladamente son menores, pero **`aerts2023integrating` desplaza un artículo de 2018 a 2023** y `ladyman2001understanding` mueve un manual once años. En un texto cuyo argumento depende de la cronología de los debates, un error de cinco años cambia el sentido de la cita. Los tres últimos casos (Oreskes, Ladyman, Boland) reflejan además una confusión entre edición original y reedición digital que conviene resolver de forma explícita, no automática.

**Ya aplicado:** en `referencias.bib` los metadatos verificados sustituyen a los parseados, y las entradas discrepantes llevan `note = {REVISAR-discrepancia-crossref | anio-en-qmd:XXXX}` para que puedas confirmar caso por caso sin perder el dato original.

### 3.4. Artefactos de parseo descartados

Durante el proceso se detectaron y corrigieron tres fallos propios del extractor, no del material: DOI de Elsevier truncados por paréntesis internos (`10.1016/0048-7333(90)90017-Z`), números de fascículo leídos como año (`Lancet 375 (9715)` → año 9715) y entradas en estilo Chicago cuyo año precede al título. Tras la corrección, la confianza alta de parseo subió de 363 a 447 registros.

---

## 4. Segundo hallazgo: la numeración de notas no cuadra

Al cruzar las llamadas `<sup>N</sup>` del cuerpo con las listas numeradas de cada bloque, **el desajuste es la norma, no la excepción**. En 25 de las 42 secciones con bloque los dos recuentos no coinciden:

| Sección | Llamadas `<sup>` | Referencias listadas | Lectura |
|---|:--:|:--:|---|
| 3.4. Falsación en dominios complejos | 20 | 9 | **11 llamadas apuntan a una referencia inexistente** |
| 1.3. Versiones del escepticismo | 2 | 11 | 9 referencias sin llamada |
| 1.6. Paradigmas de racionalidad | 3 | 60 | 57 referencias sin llamada |
| 4.3. Índices de impacto | 1 | 28 | 27 sin llamada |
| Relación emociones/procesos cognitivos | 6 | 73 | 67 sin llamada |
| 1.8. Desarrollo de la medicina científica | 10 | 22 | 12 sin llamada |
| 1.4. Heterodoxia científica | 11 | 8 | **3 llamadas huérfanas** |
| 3.6. Implicaciones sociales del consenso | 6 | 4 | **2 llamadas huérfanas** |

El fallo grave es el primer tipo: **una llamada `<sup>12</sup>` en una sección cuyo bloque solo tiene 9 entradas remite a nada**. Ocurre en §3.4, §1.4, §1.9, §3.2, §3.6, §1.11 y el Anexo I. El segundo tipo (referencias sin llamada) no rompe nada, pero confirma que los bloques crecieron como acumulación documental mientras las llamadas del texto quedaban congeladas.

Es el argumento decisivo para la migración: con `[@clave]` la correspondencia es imposible de romper, porque Pandoc genera el vínculo y falla ruidosamente en el render si la clave no existe.

---

## 5. Entregables

| Fichero | Contenido | Uso |
|---|---|---|
| **`referencias.bib`** | 616 entradas BibLaTeX. Metadatos de Crossref donde se verificaron. Campo `keywords` con la sección de procedencia; campo `note` con marcas de control | Se referencia desde el YAML del `.qmd` |
| **`inventario_referencias.csv`** | Inventario completo con procedencia, número de apariciones, presencia en secciones y en el listado final, confianza de parseo y texto original | Revisión general y decisiones de poda |
| **`cotejo_crossref.csv`** | Las 151 verificaciones con similitud de título, año contrastado y diagnóstico. Ordenado con las discrepancias primero | Resolver los 20 casos del §3 |
| **`mapa_notas.csv`** | 655 filas: sección → bloque → ordinal `N` → clave de cita → línea del `.qmd` | **Clave de sustitución** de `<sup>N</sup>` por `[@clave]`, sección a sección |
| **`completar_verificacion.R`** | Script RStudio: resuelve los 23 DOI pendientes vía `rcrossref` y DataCite, actualiza el `.bib` y extrae las huérfanas | Cerrar la verificación en local |
| `extract.py` · `classify.py` · `makebib.py` · `cotejo.py` · `mapa_notas.py` | Pipeline completo | Reejecutable si el `.qmd` cambia |

### 5.1. Marcas de control en `referencias.bib`

| Marca en `note` | Refs. | Significado |
|---|---:|---|
| `VERIFICADO-crossref` | 131 | Metadatos confirmados contra el registro |
| `REVISAR-discrepancia-crossref` | 20 | Requiere decisión tuya (§3.2 y §3.3) |
| `PENDIENTE-verificacion-doi` | 23 | Ejecutar `completar_verificacion.R` |
| `NO-CITADA-en-texto` | 122 | Está en el listado final sin respaldar nada |
| `PENDIENTE-sin-autor` | 88 | Recurso web sin autoría identificada |
| `PENDIENTE-sin-anio` | 57 | Falta fecha; muchos son enlaces sin datar |
| `PENDIENTE-parseo(baja)` | 147 | El extractor no separó los campos con seguridad |

Las marcas están pensadas para trabajar por lotes: filtras por marca en Zotero o en el CSV y resuelves una categoría entera de una vez, en lugar de recorrer 616 entradas en orden alfabético.

---

## 6. Qué falta para cerrar la Fase 2

En orden:

1. **Ejecutar `completar_verificacion.R`** en RStudio → cierra los 23 pendientes. (5 min)
2. **Corregir el DOI de `gomez-garcia2019systematic`** (§3.2). (5 min)
3. **Resolver las 19 discrepancias de año** desde `cotejo_crossref.csv`, con atención especial a Aerts, Ladyman, Boland y Oreskes. (30 min)
4. **Decidir sobre las 122 huérfanas**: convertirlas en citas reales donde aporten, retirarlas del resto. Alternativa intermedia: mantenerlas mediante `nocite` en una sección explícita *«Bibliografía complementaria»*, que es honesto y además recupera la distinción fundamental/complementaria de la propia guía docente. (2-3 h)
5. **Depurar las 147 entradas de parseo bajo** — casi todas recursos web sin autoría formal. La vía rápida es importar `referencias.bib` en Zotero, que completa metadatos desde la URL. (2-3 h)
6. **Sustituir `<sup>N</sup>` por `[@clave]`** usando `mapa_notas.csv`, capítulo a capítulo. Es lo más laborioso y donde aparecerán las llamadas huérfanas del §4. (1-2 días)
7. **Reducir los bloques `<details>` a 6-10 lecturas comentadas** por apartado, según lo decidido. El resto de fuentes ya vive en el `.bib` y aflorará en la bibliografía generada.

### 6.1. Cambio en el YAML cuando se active

```yaml
bibliography: referencias.bib
csl: apa.csl                        # APA 7ª ed., variante española
link-citations: true
citations-hover: true
reference-section-title: "Bibliografía"
lang: es
```

El CSL de APA 7 en español se descarga del repositorio oficial de Zotero (`https://www.zotero.org/styles/apa`, versión localizada `es-ES`). Conviene fijarlo como fichero local en la carpeta del proyecto en lugar de referenciarlo por URL, para que el render sea reproducible sin conexión.

---

## 7. Nota sobre el alcance de lo verificado

Para que el informe sea utilizable como respaldo del propio proyecto, conviene ser explícito sobre qué se ha comprobado y qué no:

- **Verificado contra registro autorizado:** 131 referencias (los DOI resueltos en Crossref).
- **Pendiente por límite técnico, no por duda:** 23.
- **No verificable por DOI:** 442 — libros, capítulos, prensa, informes y recursos web. Su comprobación exige otras vías (ISBN vía OpenLibrary/WorldCat para los libros; consulta directa para los enlaces). **De estas 442 no se afirma nada sobre su exactitud en este informe.**

Dicho de otro modo: se ha auditado el 24 % del corpus con garantía plena y se ha encontrado en él un error de identificador y diecinueve de fecha. No es razonable suponer que el 76 % restante esté libre de errores del mismo tipo; sí lo es suponer que la tasa será parecida. Si esa proyección se sostiene, quedan por localizar del orden de **tres a cuatro identificadores incorrectos y sesenta desajustes de fecha** en la parte no verificada. Comprobar los enlaces (¿siguen vivos? ¿apuntan a lo que dice el texto?) es un paso independiente y probablemente el siguiente en rentabilidad, dado que el material acumula versiones desde 2024.
