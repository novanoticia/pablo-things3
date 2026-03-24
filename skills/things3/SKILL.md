---
name: things3
description: >
  Crea tareas en Things 3 mediante AppleScript. Actívalo cuando el usuario diga
  "crea una tarea en Things 3", "añade esto a Things", "ponlo en Things",
  "recuérdame en Things que...", "agrega a mi lista de tareas en Things",
  o cualquier variante que implique crear, añadir o registrar algo en Things 3.
---

# Skill: Crear tarea en Things 3

## Flujo de trabajo

### Paso 1 — Recogida de datos

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

---

### Paso 2 — Interpretación de fechas

- Convierte fechas en lenguaje natural ("mañana", "el lunes", "en 3 días") a formato AppleScript: `date "dd/MM/yyyy"`
- Usa la fecha actual del sistema como referencia
- Si no se da fecha, omite ese campo del script (no uses fecha vacía)

---

### Paso 3 — Construcción del AppleScript

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

---

### Paso 4 — Ejecución

Ejecuta el script con el conector **Control your Mac → osascript**.

Si el conector no está disponible, entrega el script formateado para que el usuario lo ejecute en Terminal:

```bash
osascript -e '
tell application "Things3"
    ...
end tell
'
```

---

### Paso 5 — Confirmación

Tras ejecutar, confirma brevemente:
- Título de la tarea
- Destino (Inbox / área / proyecto)
- Fechas (si las hay)
- Etiquetas (si las hay)

Ofrece en una línea la posibilidad de mover la tarea o añadir fechas si no se especificaron.

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

## Errores a evitar

- No uses `remind me at` (Things 3 no soporta recordatorios vía AppleScript en versiones recientes)
- No uses nombres de área o proyecto que el usuario no haya confirmado — si hay duda, envía al Inbox y avisa
- No ejecutes el script antes de tener el título confirmado
