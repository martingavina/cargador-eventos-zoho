---
name: cargar-eventos
description: Abre el Cargador de eventos de Zoho CRM (crea oportunidades/eventos, cierra la actividad automática y genera asignaciones de DJ). Usar cuando el usuario diga "cargar evento", "cargar eventos", "crear un evento en Zoho", "abrir el cargador de eventos", "cargá un evento", "dar de alta un evento" o similar, o cuando quiera cargar varios eventos de una tanda en Zoho CRM.
---

# Cargador de eventos · Zoho CRM

Esta skill abre una herramienta visual (formulario) para cargar uno o varios eventos (oportunidades) en Zoho CRM. Seguí estos pasos EN ORDEN cada vez que se invoque.

## Configuración (editada al publicar cada versión)

- `LOCAL_VERSION = 1.0.3`  (versión de ESTA copia instalada del plugin; coincide con plugin.json)
- `MARKETPLACE_RAW_URL = https://raw.githubusercontent.com/martingavina/cargador-eventos-zoho/main/.claude-plugin/marketplace.json`  (URL "raw" del marketplace.json publicado)

## Paso 1 — Chequeo de versión (obligatorio, primero)

1. Hacé `web_fetch` de `MARKETPLACE_RAW_URL`, pero agregándole al final `?nocache=` seguido de un número al azar (por ejemplo, la hora actual en milisegundos) para evitar el cache de GitHub.
2. Si la descarga funciona, parseá el JSON y buscá dentro de `plugins` la entrada cuyo `name` sea `cargador-eventos-zoho`; tomá su campo `version` como `REMOTE_VERSION`.
3. Compará `REMOTE_VERSION` contra `LOCAL_VERSION` como semver (compará MAJOR, luego MINOR, luego PATCH, numéricamente).
4. Si `REMOTE_VERSION` es MAYOR que `LOCAL_VERSION` (hay actualización):
   - Leé `${CLAUDE_PLUGIN_ROOT}/skills/cargar-eventos/references/update-required.html`.
   - Reemplazá `__LOCAL__` por `LOCAL_VERSION` y `__REMOTE__` por `REMOTE_VERSION`.
   - Escribí el HTML resultante en un archivo temporal y mostralo con `create_artifact` (id `cargador-eventos-zoho`, sin `mcp_tools`).
   - Decile al usuario en una frase que la herramienta está desactualizada y que tiene que actualizar el plugin antes de usarla. **DETENÉ el proceso acá. No abras el formulario de carga.**
5. Si la descarga falla (sin internet, URL no configurada, etc.), continuá igual pero avisá en una frase que no se pudo verificar la versión.
6. Si `REMOTE_VERSION` es igual o menor, continuá normalmente.

## Paso 2 — Detectar el conector de Zoho CRM

1. Revisá las herramientas MCP disponibles en la sesión y encontrá las del conector de Zoho CRM: son las que terminan en `__searchRecords`, `__getRecord`, `__createRecords`, `__executeCOQLQuery`, etc. (mismo prefijo de servidor para todas).
2. Tomá el **prefijo** = el nombre completo de una de esas herramientas sin el nombre base final, conservando el `__` final. Ejemplo: de `mcp__abcd-1234__searchRecords` el prefijo es `mcp__abcd-1234__`.
3. Si NO hay ninguna herramienta de Zoho CRM disponible, decile al usuario que primero conecte el **conector de Zoho CRM** en Cowork (Configuración → Conectores) y **DETENÉ**. No abras el formulario.

## Paso 3 — Abrir la herramienta

1. Leé `${CLAUDE_PLUGIN_ROOT}/skills/cargar-eventos/references/tool.html`.
2. Reemplazá en el texto:
   - `__ZOHO_PREFIX__` por el prefijo detectado en el Paso 2.
   - `__TOOL_VERSION__` por `LOCAL_VERSION`.
3. Escribí el HTML resultante en un archivo temporal y publicalo con `create_artifact` (o `update_artifact` si ya existe) usando:
   - `id`: `cargador-eventos-zoho`
   - `mcp_tools`: la lista de estas siete, cada una con el prefijo detectado:
     `<prefijo>searchRecords`, `<prefijo>executeCOQLQuery`, `<prefijo>createRecords`, `<prefijo>updateRecord`, `<prefijo>getRelatedRecords`, `<prefijo>getRecord`, `<prefijo>getRecords`
4. Decile al usuario, en una o dos frases, que el cargador está abierto: que complete la tabla y presione "Crear eventos". No repitas el contenido del formulario.

## Notas de funcionamiento (para responder dudas)

- La herramienta crea cada oportunidad con `Nombre de oportunidad = "A"` (Zoho la renombra sola con el ID de evento), resuelve Contacto, Lugar (módulo `ABM_Salones`) y Clase de evento (módulo `Condiciones_de_Salon`, filtrada por el salón), crea la oportunidad, cierra la actividad "Revisar nueva Oportunidad asignada", y crea las asignaciones de DJ con Estado del evento = Confirmado, Tipo = DJ, Estado = Borrador.
- Reintenta automáticamente ante errores transitorios (503/timeout) y lleva un panel de seguimiento con checklist de tareas por evento (persistente).
- El "Propietario" de los registros queda como el usuario del conector de Zoho de cada persona.
- El campo Productor es un lookup a usuario: la herramienta avisa que se cargue a mano.
