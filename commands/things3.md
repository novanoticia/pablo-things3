---
name: things3
description: Crea una tarea en Things 3. Recoge título, notas, fechas, etiquetas y destino, construye el AppleScript y lo ejecuta vía osascript.
---

# Comando /things3

Cuando el usuario invoque este comando, sigue el flujo del skill `things3` paso a paso:

1. Presenta el formulario de recogida de datos (título, notas, fecha de inicio, deadline, etiquetas, área/proyecto)
2. Espera la respuesta completa del usuario
3. Interpreta las fechas en lenguaje natural y conviértelas a formato AppleScript `date "dd/MM/yyyy"`
4. Construye el AppleScript con solo los campos proporcionados
5. Ejecuta el script con el conector Control your Mac → osascript; si no está disponible, muestra el script para ejecución manual en Terminal
6. Confirma brevemente qué se ha creado (título, destino, fechas, etiquetas)

Consulta el skill `things3` para los detalles completos de implementación, plantilla AppleScript y errores a evitar.
