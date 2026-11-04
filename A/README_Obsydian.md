# 📚 Obsidian + Zotero: guía para gestionar referencias y notas de lectura

Tutorial paso a paso para conectar **[Zotero](https://www.zotero.org/)** (gestor de referencias bibliográficas) con **[Obsidian](https://obsidian.md/)** (base de conocimiento en Markdown). Con esta integración podrás:

- Importar tus referencias como notas de Obsidian con metadatos (autor, año, DOI, etc.).
- Extraer tus **anotaciones y subrayados de PDF** directamente a tus notas.
- Insertar **citas** mientras escribes (`[@clave]`).
- Enlazar de vuelta desde Obsidian al PDF o ítem exacto en Zotero.
- Exportar tus textos con bibliografía a Word, PDF o LaTeX mediante Pandoc.

---

## 📑 Índice

1. [Requisitos](#-requisitos)
2. [Flujo de trabajo general](#-flujo-de-trabajo-general)
3. [Paso 1: Configurar Zotero](#paso-1-configurar-zotero)
4. [Paso 2: Configurar Obsidian](#paso-2-configurar-obsidian)
5. [Paso 3: Crear una plantilla de importación](#paso-3-crear-una-plantilla-de-importación)
6. [Paso 4: Importar notas y anotaciones](#paso-4-importar-notas-y-anotaciones)
7. [Paso 5: Insertar citas mientras escribes](#paso-5-insertar-citas-mientras-escribes)
8. [Paso 6: Exportar con bibliografía (Pandoc)](#paso-6-exportar-con-bibliografía-pandoc)
9. [Alternativa sin plugins: enlaces `zotero://`](#alternativa-sin-plugins-enlaces-zotero)
10. [Consejos y buenas prácticas](#-consejos-y-buenas-prácticas)
11. [Solución de problemas](#-solución-de-problemas)
12. [Recursos](#-recursos)

---

## ✅ Requisitos

| Herramienta | Para qué sirve | Enlace |
|---|---|---|
| Zotero 7 (o superior) | Gestor de referencias | <https://www.zotero.org/download/> |
| Obsidian | Editor de notas Markdown | <https://obsidian.md/download> |
| Better BibTeX for Zotero | Genera claves de cita estables (`citekey`) | <https://retorque.re/zotero-better-bibtex/> |
| Plugin *Zotero Integration* (Obsidian) | Conecta Obsidian con Zotero | Plugins de la comunidad |

> 💡 Zotero debe estar **abierto** cuando uses la integración desde Obsidian.

---

## 🔄 Flujo de trabajo general

```text
┌────────────┐   guarda / lee / anota   ┌───────────┐
│  Navegador │ ───────────────────────▶ │  Zotero   │
│ (Connector)│                          │ (PDFs +   │
└────────────┘                          │ metadatos)│
                                        └─────┬─────┘
                                              │ Better BibTeX + Zotero Integration
                                              ▼
                                       ┌──────────┐
                                       │ Obsidian │  notas de literatura,
                                       │          │  citas, enlaces, ideas
                                       └────┬─────┘
                                            │ Pandoc (opcional)
                                            ▼
                                  Word / PDF / LaTeX con bibliografía
```

1. **Captura** el artículo con Zotero Connector (extensión del navegador).
2. **Lee y subraya** el PDF en el lector de Zotero.
3. **Importa** la referencia y sus anotaciones a Obsidian con un comando.
4. **Escribe** tus ideas en Obsidian citando las fuentes.
5. **Exporta** el texto final con la bibliografía formateada.

---

## Paso 1: Configurar Zotero

### 1.1 Instalar Better BibTeX

1. Descarga el archivo `.xpi` de la última versión desde la [página oficial](https://retorque.re/zotero-better-bibtex/installation/).
2. En Zotero: **Herramientas → Complementos** (*Tools → Plugins*).
3. Haz clic en el engranaje ⚙️ → **Instalar complemento desde archivo…** y selecciona el `.xpi`.
4. Reinicia Zotero.

### 1.2 Permitir la comunicación con otras aplicaciones

1. Ve a **Configuración → Avanzado** (*Settings → Advanced*).
2. Activa la opción **"Permitir que otras aplicaciones de este equipo se comuniquen con Zotero"** (*Allow other applications on this computer to communicate with Zotero*).

### 1.3 (Opcional) Ajustar el formato de la clave de cita

En **Configuración → Better BibTeX → Citation keys** puedes definir cómo se generan las claves. Un formato habitual es:

```text
auth.lower + shorttitle(3,3) + year
```

Ejemplo de resultado: `garciaredesneuronales2023`.

> ⚠️ Una vez que empieces a citar, **evita cambiar** el formato de las claves: rompería las citas existentes.

---

## Paso 2: Configurar Obsidian

### 2.1 Crear (o abrir) una bóveda

Abre Obsidian y crea una *vault* o abre la que ya usas.

### 2.2 Instalar el plugin Zotero Integration

1. **Configuración → Plugins de la comunidad** → desactiva el *Modo restringido* (*Restricted mode*).
2. Haz clic en **Explorar** y busca **"Zotero Integration"** (autor: *mgmeyers*).
3. **Instalar** y luego **Activar**.

### 2.3 Configurar el plugin

En **Configuración → Zotero Integration**:

- **Note Import Location**: carpeta donde se guardarán las notas importadas (por ejemplo, `Literatura/`).
- **Database**: deja *Zotero* y selecciona tu biblioteca si usas varias.
- **Citation formats / Import formats**: aquí crearás las plantillas (ver siguiente paso).

---

## Paso 3: Crear una plantilla de importación

Las plantillas usan el lenguaje [Nunjucks](https://mozilla.github.io/nunjucks/) y definen cómo se ve cada nota importada.

1. Crea un archivo en tu bóveda, por ejemplo `Plantillas/zotero-nota.md`.
2. Pega este contenido de ejemplo:

````markdown
---
title: "{{title}}"
citekey: "{{citekey}}"
autores: "{% for c in creators %}{{c.firstName}} {{c.lastName}}{% if not loop.last %}, {% endif %}{% endfor %}"
año: {{date | format("YYYY")}}
publicacion: "{{publicationTitle}}"
doi: "{{DOI}}"
tags: [literatura]
---

# {{title}}

**Zotero:** [Abrir ítem]({{desktopURI}})
**DOI:** {{DOI}}

## Resumen original

{{abstractNote}}

## Anotaciones

{% for annotation in annotations -%}
{%- if annotation.annotatedText %}
> {{annotation.annotatedText}} [(p. {{annotation.pageLabel}})]({{annotation.desktopURI}})
{%- endif %}
{%- if annotation.comment %}

**Comentario:** {{annotation.comment}}
{%- endif %}

{% endfor %}

## Mis notas

-
````

3. En **Configuración → Zotero Integration → Import Formats**, haz clic en **Add Import Format** y configura:
   - **Name**: `Nota de literatura`
   - **Output Path**: `Literatura/@{{citekey}}.md`
   - **Template File**: `Plantillas/zotero-nota.md`
   - **Image Output Path**: `Literatura/imagenes` (si extraes imágenes de áreas).

> 📖 Consulta la [documentación oficial del plugin](https://github.com/mgmeyers/obsidian-zotero-integration) para ver todas las variables disponibles (`creators`, `tags`, `annotations`, `attachments`, etc.).

---

## Paso 4: Importar notas y anotaciones

1. En Zotero, **lee tu PDF y haz tus subrayados y comentarios** con el lector integrado.
2. En Obsidian abre la paleta de comandos con `Ctrl/Cmd + P`.
3. Ejecuta **`Zotero Integration: Nota de literatura`** (el nombre que le diste al formato).
4. Se abrirá el selector de Zotero: busca y elige la referencia.
5. Obsidian creará `Literatura/@clave.md` con los metadatos y tus anotaciones.

> 🔁 Si subrayas más cosas después, **vuelve a ejecutar el comando** sobre el mismo ítem. Para que no se pierdan tus notas personales, escríbelas fuera de los bloques generados por la plantilla o usa la sección de marcadores de actualización del plugin.

---

## Paso 5: Insertar citas mientras escribes

El plugin también permite insertar citas con el selector de Zotero.

1. En **Configuración → Zotero Integration → Citation Formats** añade un formato, por ejemplo:
   - **Name**: `Cita Pandoc`
   - **Output format**: `Pandoc`
2. En cualquier nota, pulsa `Ctrl/Cmd + P` y ejecuta **`Zotero Integration: Cita Pandoc`**.
3. Elige la referencia y se insertará algo como:

```markdown
Según los estudios recientes [@garciaredesneuronales2023, p. 14], el modelo...
```

También puedes añadir formatos tipo **Wikilink** para enlazar con la nota de literatura:

```markdown
Ver [[@garciaredesneuronales2023]]
```

---

## Paso 6: Exportar con bibliografía (Pandoc)

Si quieres convertir tus notas a Word, PDF u otros formatos con la bibliografía automática:

1. Instala [Pandoc](https://pandoc.org/installing.html).
2. En Zotero, haz clic derecho en tu colección → **Exportar colección…** → formato **Better BibLaTeX** (o *CSL JSON*) y marca **Mantener actualizado** (*Keep updated*). Guarda el archivo, por ejemplo `biblio.bib`, en tu bóveda.
3. Añade en la cabecera de tu documento:

```yaml
---
title: Mi ensayo
bibliography: biblio.bib
csl: apa.csl
---
```

4. Exporta desde la terminal:

```bash
pandoc ensayo.md --citeproc -o ensayo.docx
# o a PDF
pandoc ensayo.md --citeproc -o ensayo.pdf
```

> Puedes descargar estilos `.csl` (APA, Chicago, Vancouver, etc.) desde el [repositorio de estilos de Zotero](https://www.zotero.org/styles).

---

## Alternativa sin plugins: enlaces `zotero://`

Si prefieres una configuración mínima, puedes enlazar a tus ítems directamente:

1. En Zotero, clic derecho sobre un ítem → **Copiar enlace al ítem** (o copia el URI `zotero://select/library/items/XXXXXXXX`).
2. Pégalo en Obsidian como enlace Markdown:

```markdown
[Artículo de García (2023)](zotero://select/library/items/ABCD1234)
```

3. Para abrir un PDF en una página concreta:

```markdown
[Ver página 5](zotero://open-pdf/library/items/ABCD1234?page=5)
```

Es menos automático, pero no requiere instalar nada más.

---

## 💡 Consejos y buenas prácticas

- **Una nota por fuente.** Nombra los archivos con la clave de cita (`@clave.md`) para evitar duplicados.
- **Separa lo ajeno de lo propio.** Mantén las citas textuales en bloques `>` y tus ideas en secciones aparte (*notas de literatura* vs. *notas permanentes*).
- **Usa etiquetas y propiedades.** Los metadatos del encabezado YAML permiten filtrar con el plugin **Dataview** o con **Bases** de Obsidian.
- **Sincroniza con cuidado.** Si usas Git, iCloud, Syncthing u Obsidian Sync para la bóveda, no guardes los PDFs de Zotero dentro de ella; deja que Zotero los gestione.
- **Código de colores en las anotaciones.** Puedes asignar un significado a cada color (p. ej. amarillo = idea clave, rojo = crítica) y filtrarlos en la plantilla con `annotation.colorCategory`.
- **Haz copias de seguridad** de tu biblioteca de Zotero y de tu bóveda.

---

## 🛠️ Solución de problemas

| Problema | Posible solución |
|---|---|
| El selector de Zotero no aparece | Verifica que Zotero esté abierto y que la opción de **comunicación con otras aplicaciones** esté activada. |
| Las claves de cita salen vacías | Asegúrate de tener **Better BibTeX** instalado y reinicia Zotero. |
| No aparecen las anotaciones | Comprueba que el PDF tenga anotaciones creadas en el lector de Zotero y que no estén solo en un PDF externo. |
| Error en la plantilla | Revisa la sintaxis Nunjucks (`{% %}` y `{{ }}` bien cerrados) y que la ruta de la plantilla sea correcta. |
| Caracteres extraños en el nombre del archivo | Simplifica el formato de la clave en Better BibTeX o ajusta el *Output Path*. |

---

## 🔗 Recursos

- [Documentación de Zotero](https://www.zotero.org/support/)
- [Better BibTeX for Zotero](https://retorque.re/zotero-better-bibtex/)
- [Plugin Zotero Integration (GitHub)](https://github.com/mgmeyers/obsidian-zotero-integration)
- [Ayuda de Obsidian](https://help.obsidian.md/)
- [Manual de Pandoc: citas](https://pandoc.org/MANUAL.html#citations)
- [Repositorio de estilos CSL](https://www.zotero.org/styles)

---

## 🤝 Contribuciones

¿Encontraste un error o quieres mejorar esta guía? Abre un *issue* o envía un *pull request*.

## 📄 Licencia

Este contenido se distribuye bajo licencia [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.es). Puedes compartirlo y adaptarlo citando la fuente.
