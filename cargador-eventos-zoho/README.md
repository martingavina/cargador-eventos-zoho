# Cargador de eventos · Zoho CRM

Plugin de Cowork para cargar eventos (oportunidades) en Zoho CRM en un clic.

## Qué hace

Abre un formulario donde se completan una o varias plantillas de evento. Al presionar **Crear eventos**, por cada uno:

1. Busca (o crea) el contacto.
2. Resuelve el Lugar (`ABM_Salones`) y la Clase de evento (`Condiciones_de_Salon`, filtrada por el salón).
3. Crea la oportunidad (Nombre = "A"; Zoho la renombra con el ID de evento).
4. Cierra la actividad automática "Revisar nueva Oportunidad asignada".
5. Crea las asignaciones de DJ (Estado del evento = Confirmado, Tipo = DJ, Estado = Borrador).

Detecta si se están cargando dos eventos idénticos y avisa. El botón muestra la cantidad de eventos a crear.

## Requisitos para cada usuario

- Cowork (app de escritorio de Claude).
- El **conector de Zoho CRM** conectado en su Cowork (Configuración → Conectores). El "Propietario" de los eventos queda como su usuario de Zoho.

## Actualizaciones

Al abrir, el plugin verifica si hay una versión más nueva publicada en el marketplace. Si la hay, muestra una pantalla de "Actualización requerida" y no deja usar la herramienta hasta actualizar el plugin.

## Para el administrador

Al publicar una versión nueva:

1. Editá la herramienta y/o la lógica en `skills/cargar-eventos/`.
2. Subí `version` en `.claude-plugin/plugin.json` y `LOCAL_VERSION` en `skills/cargar-eventos/SKILL.md`.
3. Subí `version` de este plugin en el `marketplace.json` del repo.
4. Hacé push al repositorio. Los usuarios reciben la actualización al correr `/plugin marketplace update`.

> Configurá `MARKETPLACE_RAW_URL` en `SKILL.md` con la URL "raw" del `marketplace.json` del repo para que funcione el chequeo de versión.
