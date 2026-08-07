# pablo-things3

Plugin para crear tareas en **Things 3** desde Claude Code y Cowork, usando AppleScript.

> **Compatible con [Agent Plugins 1.0.0](https://agent-plugins.org/specification)** —
> el formato portátil de empaquetado de la Agentic AI Foundation (OpenAI, Amazon,
> Microsoft, Cursor y Vercel, con Google como *core maintainer*). El paquete lleva el
> manifiesto portable `plugin.json` en la raíz y el skill en `skills/things3/SKILL.md`,
> así que cualquier cliente conformante lo descubre.
>
> **Sobre ChatGPT:** el formato es portable y ChatGPT cargará el skill, pero **no podrá
> ejecutarlo**. Este plugin conduce Things 3 por AppleScript, lo que exige un Mac con la
> app instalada y un cliente capaz de lanzar `osascript`. Fuera de ese entorno el skill
> se carga y no puede hacer nada. Lo declara su campo `compatibility`, que es donde la
> especificación de Agent Skills manda poner los requisitos de entorno.

## Instalación

### Desde el marketplace (recomendado)

```
/plugin marketplace add https://github.com/novanoticia/pablo-things3
/plugin install pablo-things3@pablo-things3
/reload-plugins
```

Lo que va después de la arroba es el nombre del **marketplace** (el campo `name` de
`.claude-plugin/marketplace.json`), no un usuario de GitHub. Aquí los dos coinciden
porque el repositorio publica un único plugin.

### Sin instalar nada, para probarlo

```bash
git clone https://github.com/novanoticia/pablo-things3
claude --plugin-dir ./pablo-things3
```

### En Cowork

Comprime la **raíz del repositorio** —donde están `plugin.json`, `.claude-plugin/` y
`skills/`— y súbela en *Customize → Plugins → Upload*.

### Actualizar

```
/plugin marketplace update pablo-things3
/plugin update pablo-things3
/reload-plugins
```

## Qué hace

- Recoge los datos de la tarea mediante un formulario conversacional
- Interpreta fechas en lenguaje natural ("mañana", "el lunes", "en 2 semanas")
- Genera y ejecuta el AppleScript correspondiente vía `osascript`
- Confirma lo que se ha creado

## Cómo usarlo

**Lenguaje natural** — simplemente menciona Things 3:
- "Crea una tarea en Things 3 para revisar el contrato"
- "Añade a Things que tengo que llamar al médico el viernes"
- "Ponlo en Things con deadline el 1 de abril"

**Comando directo** — instalado como plugin, el comando va namespaced:

| Qué escribes | Qué hace |
|---|---|
| `/pablo-things3:things3` | Abre el formulario. Forma canónica. |
| `/things3` | Atajo. Funciona igual, salvo que otro plugin instalado use ya ese nombre. |

Escribe `/` y teclea `thin` para que el autocompletado lo ofrezca. Si no aparece,
ejecuta `/reload-plugins`.

## Campos disponibles

| Campo | Requerido | Notas |
|-------|-----------|-------|
| Título | Sí | Nombre de la tarea |
| Notas | No | Descripción o contexto adicional |
| Fecha de inicio | No | Cuándo aparece en "Hoy" |
| Deadline | No | Fecha límite visible en Things |
| Etiquetas | No | Se crean automáticamente si no existen |
| Área / Proyecto | No | Si no se indica, va al Inbox |

## Requisitos

- **Things 3** instalado en el Mac
- **Control your Mac** (conector de Cowork) para ejecutar `osascript` automáticamente
  - Si no está disponible, el plugin entrega el script para ejecutarlo manualmente en Terminal

## Limitaciones conocidas

- Things 3 no soporta recordatorios (`remind me at`) vía AppleScript en versiones recientes — no se usa
- Los nombres de área/proyecto deben existir previamente en Things 3
