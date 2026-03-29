---
name: things3
description: Gestiona tareas en Things 3. Crea, lista y completa tareas mediante AppleScript. Recoge datos del usuario, interpreta fechas en lenguaje natural y ejecuta el script vía osascript.
---

# Comando /things3

Cuando el usuario invoque este comando, detecta la intención (crear, listar o completar) y sigue el flujo correspondiente del skill `things3`:

**Para crear una tarea:**
1. Presenta el formulario de recogida de datos (título, notas, fecha de inicio, deadline, etiquetas, área/proyecto)
2. Espera la respuesta completa del usuario
3. Interpreta las fechas en lenguaje natural y conviértelas a formato AppleScript `date "dd/MM/yyyy"`
4. Construye el AppleScript con solo los campos proporcionados
5. Ejecuta con el conector Control your Mac → osascript; si no está disponible, genera el script completo con valores reales (nunca con `...`) para ejecución manual en Terminal
6. Confirma brevemente qué se ha creado (título, destino, fechas, etiquetas)

**Para listar tareas:**
1. Si el usuario no especifica ámbito, ejecuta primero el script de listar áreas y presenta las opciones
2. Ejecuta el script correspondiente al ámbito indicado
3. Presenta las tareas de forma clara, agrupando por proyecto si hay muchas

**Para completar una tarea:**
1. Busca las candidatas con el script de búsqueda global antes de ejecutar nada
2. Presenta los resultados y pide confirmación explícita
3. Solo tras confirmar, ejecuta el script de marcar como completada
4. Confirma qué tarea se ha completado

Consulta el skill `things3` para las plantillas AppleScript completas y los errores a evitar.
