# pablo-things3

[![Versión en main](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fraw.githubusercontent.com%2Fnovanoticia%2Fpablo-things3%2Fmain%2Fplugin.json&query=%24.version&prefix=v&label=Versi%C3%B3n%20en%20main&color=blue)](https://github.com/novanoticia/pablo-things3/blob/main/plugin.json)
[![Última versión publicada](https://img.shields.io/github/v/release/novanoticia/pablo-things3?display_name=tag&label=%C3%9Altima%20versi%C3%B3n%20publicada&color=green)](https://github.com/novanoticia/pablo-things3/releases/latest)

Plugin para **crear, listar y completar** tareas en **Things 3** desde Claude Code y Cowork, usando AppleScript.

**En esta página:** [En 3 pasos](#en-3-pasos) · [Instalación](#instalación) · [Qué hace](#qué-hace) · [Cómo usarlo](#cómo-usarlo) · [Campos disponibles](#campos-disponibles) · [Requisitos](#requisitos) · [Limitaciones](#limitaciones-conocidas)

## En 3 pasos

1. **Instálalo** en Claude Code con los tres comandos de [Instalación](#instalación) (en Cowork, sube el repo comprimido).
2. **Pídeselo con tus palabras**: «crea una tarea en Things 3 para revisar el contrato», «qué tengo pendiente hoy en Things», «marca como hecha la de llamar al médico».
3. **Confirma** lo que te propone. Antes de completar una tarea siempre te enseña las candidatas y espera tu «sí».


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

**Crear tareas**

- Recoge los datos de la tarea mediante un formulario conversacional
- Interpreta fechas en lenguaje natural ("mañana", "el lunes", "en 2 semanas")
- Genera y ejecuta el AppleScript correspondiente vía `osascript`
- Confirma lo que se ha creado

**Listar tareas**

- De un área, de un proyecto o de Hoy (*Today*)
- Si no dices de dónde, primero te enseña tus áreas para que elijas

**Completar tareas**

- Por nombre (completo o parcial) o por su posición en una lista recién mostrada («la primera»)
- Busca las candidatas y **pide confirmación antes de marcar** ninguna como hecha

## Cómo usarlo

**Lenguaje natural** — simplemente menciona Things 3:
- "Crea una tarea en Things 3 para revisar el contrato"
- "Añade a Things que tengo que llamar al médico el viernes"
- "Ponlo en Things con deadline el 1 de abril"
- "Qué tengo pendiente en Things" / "Muéstrame mis tareas del área Informática"
- "Completa la tarea de revisar el contrato" / "Tacha la primera"

**Comando directo** — instalado como plugin, el comando va namespaced:

| Qué escribes | Qué hace |
|---|---|
| `/pablo-things3:things3` | Detecta si quieres crear, listar o completar. Forma canónica. |
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

## Compatibilidad con otros clientes

> **Compatible con [Agent Plugins 1.0.0](https://agent-plugins.org/specification)** —
> el formato portátil de empaquetado de la Agentic AI Foundation (OpenAI, Amazon,
> Microsoft, Cursor y Vercel, con Google como *core maintainer*). El paquete lleva el
> manifiesto portable `plugin.json` en la raíz y el skill en `skills/things3/SKILL.md`,
> así que cualquier cliente conformante lo descubre.
>
> **Qué necesita el cliente para ejecutarlo.** El skill viaja a cualquier cliente
> conformante, pero para *crear tareas de verdad* el cliente tiene que poder ejecutar
> `osascript` en el mismo Mac donde está Things 3. Con esa capacidad funciona; sin ella
> el skill sigue siendo útil —entrega el AppleScript listo para pegar en Terminal— pero
> no actúa por sí mismo. Está declarado en el campo `compatibility`, que es donde la
> especificación de Agent Skills manda poner los requisitos de entorno.
>
> Deliberadamente no lleva una lista de qué productos pueden y no pueden: las
> capacidades de cada cliente cambian y una lista así envejece mal. Si no sabes si el
> tuyo puede, pídele que ejecute `osascript -e 'tell application "Things3" to activate'`
> y observa si Things 3 se abre.

## Requisitos

- **Things 3** instalado en el Mac
- **Control your Mac** (conector de Cowork) para ejecutar `osascript` automáticamente
  - Si no está disponible, el plugin entrega el script para ejecutarlo manualmente en Terminal

## Limitaciones conocidas

- Things 3 no soporta recordatorios (`remind me at`) vía AppleScript en versiones recientes — no se usa
- Los nombres de área/proyecto deben existir previamente en Things 3
