# pablo-things3

Plugin para crear tareas en **Things 3** desde Cowork, usando AppleScript.

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

**Comando directo** — escribe `/things3` y sigue el formulario.

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
