---
name: things3
description: >
  Gestiona tareas en Things 3 mediante AppleScript: crea, lista y completa tareas.
  Actívalo cuando el usuario diga "crea una tarea en Things 3", "añade esto a Things",
  "ponlo en Things", "recuérdame en Things que...", "agrega a mi lista de tareas en Things",
  "muéstrame mis tareas de Things", "qué tengo pendiente en Things", "lista mis tareas",
  "completa la tarea X en Things", "marca como hecha", "tacha eso de Things",
  o cualquier variante que implique crear, listar, consultar o completar tareas en Things 3.
compatibility: >
  macOS con Things 3 instalado. Requiere un cliente capaz de ejecutar `osascript`
  (Claude Code, o Cowork con el conector "Control your Mac"). Sin eso el skill
  entrega el AppleScript para ejecutarlo a mano en Terminal, pero no puede actuar
  por sí mismo. No funcional en ChatGPT, Perplexity ni Mistral.
---

# Skill: Gestionar tareas en Things 3

Este skill permite tres operaciones: **crear**, **listar** y **completar** tareas en Things 3.

Detecta la intención del usuario y ejecuta el flujo correspondiente.

---

## A — Crear tarea

### Paso A1 — Recogida de datos

Antes de crear nada, presenta al usuario el formulario completo en un solo bloque. No ejecutes ningún script hasta tener al menos el título confirmado.

```
Para crear la tarea en Things 3 necesito estos datos:

1. **Título** (obligatorio):
2. **Notas** (opcional):
3. **Fecha de inicio** (opcional, ej: "mañana", "el lunes", "en 2 semanas"):
4. **Fecha límite / deadline** (opcional):
5. **Etiquetas** (opcional, separadas por comas):
6. **Área o Proyecto** (opcional — si no indicas nada, va al Inbox):
```

Espera respuesta completa antes de continuar.

### Paso A2 — Interpretación de fechas

- Convierte fechas en lenguaje natural ("mañana", "el lunes", "en 3 días") a formato AppleScript: `date "dd/MM/yyyy"`
- Usa la fecha actual del sistema como referencia
- Si no se da fecha, omite ese campo del script (no uses fecha vacía)

### Paso A3 — Construcción del AppleScript

Usa esta plantilla. Incluye solo los campos que el usuario haya rellenado:

```applescript
tell application "Things3"
    set newTask to make new to do with properties { ¬
        name: "TÍTULO" ¬
        [, notes: "NOTAS"] ¬
        [, activation date: date "DD/MM/YYYY"] ¬
        [, due date: date "DD/MM/YYYY"] ¬
        [, tag names: "etiqueta1, etiqueta2"] ¬
    }
    [move newTask to area "NOMBRE"]
    [move newTask to project "NOMBRE"]
end tell
```

Notas de implementación:
- `activation date` = fecha de inicio en Things 3
- `due date` = deadline
- Si el destino es Inbox (por defecto), no incluyas instrucción `move`
- Usa `move newTask to area "NOMBRE"` o `move newTask to project "NOMBRE"` según corresponda
- Las etiquetas se crean automáticamente en Things 3 si no existen

### Paso A4 — Ejecución

Ejecuta el script con el conector **Control your Mac → osascript**.

Si el conector no está disponible, entrega el script completo (con los valores reales ya sustituidos) para que el usuario lo ejecute en Terminal. Por ejemplo, para una tarea sin campos opcionales:

```bash
osascript << 'EOF'
tell application "Things3"
    set newTask to make new to do with properties {name: "TÍTULO"}
end tell
EOF
```

Sustituye siempre `TÍTULO` y cualquier otro campo por los valores reales del usuario. Nunca uses `...` como placeholder en el script de fallback.

### Paso A5 — Confirmación

Tras ejecutar, confirma brevemente: título, destino, fechas y etiquetas (si las hay). Ofrece en una línea la posibilidad de mover la tarea o añadir fechas si no se especificaron.

---

## B — Listar tareas

### Paso B1 — Determinar ámbito

Identifica qué quiere listar el usuario. Opciones posibles:
- Un **área** concreta (ej: "Sapame", "Informática")
- Un **proyecto** concreto
- La **bandeja de entrada** (Inbox)
- **Today** (tareas de hoy)
- **Upcoming** (próximas)

Si el usuario no especifica ámbito, **siempre ejecuta primero el script de listar áreas** y presenta el resultado antes de preguntar. No preguntes sin mostrar las opciones disponibles — el usuario puede no recordar los nombres exactos.

### Paso B2 — Consulta con AppleScript

Según el ámbito, usa una de estas plantillas:

**Listar tareas de un área:**
```applescript
tell application "Things3"
    set todoNames to {}
    set allToDos to to dos of area "NOMBRE_AREA"
    repeat with t in allToDos
        set tName to name of t
        set tNotes to notes of t
        if tNotes is "" then set tNotes to "(sin notas)"
        set end of todoNames to tName & " || " & tNotes
    end repeat
    set AppleScript's text item delimiters to linefeed
    set output to todoNames as string
    set AppleScript's text item delimiters to ""
    return output
end tell
```

**Listar tareas de un proyecto:**
```applescript
tell application "Things3"
    set todoNames to {}
    set allToDos to to dos of project "NOMBRE_PROYECTO"
    repeat with t in allToDos
        set tName to name of t
        set tNotes to notes of t
        if tNotes is "" then set tNotes to "(sin notas)"
        set end of todoNames to tName & " || " & tNotes
    end repeat
    set AppleScript's text item delimiters to linefeed
    set output to todoNames as string
    set AppleScript's text item delimiters to ""
    return output
end tell
```

**Listar tareas de Today:**
```applescript
tell application "Things3"
    set todoNames to {}
    set allToDos to to dos of list "Today"
    repeat with t in allToDos
        set tName to name of t
        set end of todoNames to tName
    end repeat
    set AppleScript's text item delimiters to linefeed
    set output to todoNames as string
    set AppleScript's text item delimiters to ""
    return output
end tell
```

**Listar todas las áreas:**
```applescript
tell application "Things3"
    return name of every area
end tell
```

**Listar proyectos de un área:**
```applescript
tell application "Things3"
    set projNames to {}
    set allProjects to projects of area "NOMBRE_AREA"
    repeat with p in allProjects
        set end of projNames to name of p
    end repeat
    set AppleScript's text item delimiters to linefeed
    set output to projNames as string
    set AppleScript's text item delimiters to ""
    return output
end tell
```

**Contar tareas de un área:**
```applescript
tell application "Things3"
    return count of to dos of area "NOMBRE_AREA"
end tell
```

### Paso B3 — Presentación

Presenta las tareas de forma clara y concisa. Incluye notas si las hay. Si hay muchas tareas, agrupa por proyecto si es posible.

---

## C — Completar tarea

### Paso C1 — Identificar la tarea

El usuario puede referirse a la tarea por:
- Nombre exacto o parcial
- Posición en una lista que se acaba de mostrar (ej: "la primera", "la 2")

**Antes de ejecutar el script de completar**, obtén la lista de candidatas con el texto de búsqueda. Si la búsqueda devuelve más de una tarea, o si el usuario no especificó área, usa el script de búsqueda global (ver abajo). Presenta las candidatas y pide confirmación explícita antes de marcar ninguna como completada.

### Paso C2 — Buscar candidatas (siempre antes de completar)

Si el usuario no especifica área o proyecto, busca en el Inbox primero, luego itera por todas las áreas:

```applescript
tell application "Things3"
    set matches to {}
    -- Buscar en Inbox
    set inboxTasks to to dos of list "Inbox"
    repeat with t in inboxTasks
        if name of t contains "TEXTO_BUSQUEDA" then
            set end of matches to name of t & " [Inbox]"
        end if
    end repeat
    -- Buscar en todas las áreas
    set allAreas to every area
    repeat with a in allAreas
        set areaTasks to to dos of a
        repeat with t in areaTasks
            if name of t contains "TEXTO_BUSQUEDA" then
                set end of matches to name of t & " [" & name of a & "]"
            end if
        end repeat
    end repeat
    set AppleScript's text item delimiters to linefeed
    set output to matches as string
    set AppleScript's text item delimiters to ""
    return output
end tell
```

Presenta los resultados al usuario y pide que confirme cuál completar.

### Paso C3 — Marcar como completada (solo tras confirmación)

Una vez confirmada la tarea y conocida su ubicación exacta:

```applescript
tell application "Things3"
    set targetToDo to first to do of area "NOMBRE_AREA" whose name contains "TEXTO_BUSQUEDA"
    set status of targetToDo to completed
    return name of targetToDo & " → completada"
end tell
```

Si la tarea está en un proyecto:
```applescript
tell application "Things3"
    set targetToDo to first to do of project "NOMBRE_PROYECTO" whose name contains "TEXTO_BUSQUEDA"
    set status of targetToDo to completed
    return name of targetToDo & " → completada"
end tell
```

### Paso C4 — Confirmación

Confirma brevemente qué tarea se ha completado. Si el usuario estaba trabajando con una lista, ofrece continuar con la siguiente.

---

## Valores por defecto

| Campo | Default |
|-------|---------|
| Destino | Inbox |
| Fecha de inicio | ninguna |
| Deadline | ninguna |
| Etiquetas | ninguna |
| Notas | ninguna |

---

## Notas técnicas de AppleScript

- Usa `linefeed` en lugar de `return` para separadores de texto en cadenas concatenadas
- NO uses la variable `result` — está reservada en AppleScript. Usa `output` u otro nombre
- Al listar tareas, captura las notas junto con el nombre para dar contexto completo
- Ejecuta siempre con el conector **Control your Mac → osascript**
- En el fallback de Terminal, usa heredoc (`<< 'EOF'`) para evitar problemas con comillas simples dentro del script

## Errores a evitar

- No uses `remind me at` (Things 3 no soporta recordatorios vía AppleScript en versiones recientes)
- No uses nombres de área o proyecto que el usuario no haya confirmado — si hay duda, ejecuta primero el script de listar áreas
- No ejecutes el script de completar antes de obtener confirmación explícita del usuario
- No ejecutes el script de crear antes de tener el título confirmado
- Nunca uses `...` como placeholder en scripts de fallback — sustituye siempre los valores reales
