# Filosofía y conocimiento — v.6
### Monografía técnica 26311M9
Miguel Moreno Muñoz  
Departamento de Filosofía II — Universidad de Granada  
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.17284805.svg)](https://doi.org/10.5281/zenodo.17284805)  
[![Licencia: CC BY-NC-SA 4.0](https://img.shields.io/badge/licencia-CC%20BY--NC--SA%204.0-informational)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es)

Texto sobre epistemología aplicada con análisis de casos. Seis capítulos —génesis del
conocimiento, instrumentalización del debate público, consenso y escepticismo
climático, intereses e integridad de la investigación, comunicación y acceso,
sociedad del conocimiento— con estudios de caso documentados, glosario activo e
índices navegables. Cubre los temas 1 a 6 de la guía docente.

<p align="center">
  <img src="imagenes/portada-oscura-web" alt="Portada de «Filosofía y conocimiento», versión 6" width="430">
</p>

---

## Leer

| | |
|---|---|
| **Edición de referencia** | <https://www.ugr.es/~mm3/fc26/> |
| GitHub Pages | <https://utilizas.github.io/fc2026/> |
| Netlify | <https://fc2026.netlify.app/> |
| Vercel | <https://fc2026.vercel.app/> |
| Cloudflare Pages | <https://fc2026.pages.dev/> |
| Fichero único (Zenodo) | <https://doi.org/10.5281/zenodo.17284805> |

Netlify es la edición canónica; GitHub Pages, Vercel y Cloudflare Workers son
espejos del mismo contenido. Las cuatro publican el directorio `_salida/web` ya
renderizado.

## Qué cambia en la versión 6

La versión 5 era un fichero único de unas 110.000 palabras, resultado de
revisiones acumuladas desde 2024. Esta no añade una capa más: reorganiza lo
acumulado, subsana lagunas y actualiza los casos ligados a debates en curso.

- **Bibliografía gestionada y verificada.** 643 entradas en un único
  `referencias.bib`, cada una con su estado de comprobación declarado en un
  campo `verificacion`. La bibliografía final se genera desde las citas del
  texto. El cotejo contra Crossref y DataCite detectó once referencias que no
  correspondían a ningún trabajo localizable tal como se citaban, y otras tantas
  con revista, año o autoría equivocados.
- **Estructura declarada.** Seis capítulos con correspondencia explícita con el
  programa, agrupados en cuatro partes, frente al fichero corrido de la v5.
- **Casos con identidad propia.** Cada estudio de caso tiene número, enlace
  permanente y ficha de procedencia, e índice propio.
- **Destacados tipificados.** Los bloques resaltados declaran su función
  argumentativa —tesis, implicación, controversia abierta, dato, cautela
  metodológica—, lo que permite recorrer el libro por tipo de contenido.
- **Glosario activo.** 193 entradas; los términos marcados en el cuerpo abren su
  definición sin salir de la página y enlazan con la entrada completa.
- **Accesibilidad verificada.** Contraste AAA (7:1) comprobado por cálculo en
  los temas claro y oscuro, con métrica tipográfica idéntica entre ambos.
- **Método editorial declarado.** El apartado «Selección y verificación de
  fuentes» expone los criterios de admisión, el vocabulario de verificación y el
  uso de herramientas de IA generativa, con detalle de qué se redactó con
  asistencia y bajo qué control.
- **Identidad visual propia.** Portada, favicon y tarjeta social generados por
  código desde la paleta del propio libro (`py/portada.py`, `py/marcas.py`).

## Construir

```bash
quarto render --profile web      # libro navegable → _salida/web
quarto render --profile single   # fichero HTML único → _salida/single/fc26.html
quarto preview --profile web     # vista previa con recarga
```

Tras editar `glosario/glosario.yml` o añadir contenido, antes de renderizar:

```bash
python3 py/construir_glosario_js.py   # diccionario de los términos marcados
python3 py/construir_inventarios.py   # inventarios que alimentan los índices
python3 py/comprobar_estructura.py    # doce comprobaciones; el pre-render la repite
```

`R/preparar.R` corre como pre-render: descarga el CSL de APA 7 si falta,
regenera `glosario/glosario.qmd` y reensambla `fc26.qmd`. Es idempotente.

## Estructura

| Ruta | Contenido |
|---|---|
| `prefacio/` | Prefacio, guía de uso docente, método y criterios editoriales |
| `capitulos/` | Los seis capítulos |
| `apendices/` | Epílogo y colofón |
| `indices/` | Casos, figuras y tablas, destacados, recursos |
| `glosario/` | `glosario.yml` (fuente) y `glosario.qmd` (generado) |
| `bib/` | `referencias.bib`, CSL y material de auditoría |
| `estilos/` | SCSS: variables, componentes y los dos temas |
| `imagenes/` | Figuras e identidad visual |
| `R/`, `py/` | Verificación, índices, inventarios y generadores |

Se generan y no se editan a mano: `fc26.qmd`, `glosario/glosario.qmd`, las
tablas de `indices/*.qmd`, `bib/_complementaria.md` y el bloque `FC_GLOSARIO`
de `plantillas/head-extra.html`.

## Citar

> Moreno Muñoz, M. (2026). *Filosofía y conocimiento* (versión 6).
> Zenodo — CERN Research Repository. https://doi.org/10.5281/zenodo.17284805

## Licencia

Texto e imágenes bajo [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es).
© 2026 Miguel Moreno Muñoz.
