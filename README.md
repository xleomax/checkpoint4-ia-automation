# Integración de Triage y Soporte con n8n (Módulo 4)

Workflow automatizado en n8n que procesa correos de soporte entrantes, ejecuta triage automático mediante LLM y sincroniza la información con HubSpot CRM, Gmail (borradores) y notificaciones en Slack.

## Funcionamiento del Flujo

1. **Trigger de entrada:** Escucha correos entrantes mediante el conector de Gmail.
2. **Filtro de autorespuestas:** Evalúa cabeceras y asuntos para descartar mensajes automáticos (`out of office`, `auto-reply`, casillas `no-reply`) y evitar loops infinitos.
3. **Limpieza de payload:** Extrae y sanitiza el remitente (`From`), asunto y cuerpo del mensaje en variables limpias para evitar errores de tipo en las APIs posteriores.
4. **Triage con Agente IA:** Clasifica la consulta y redacta una propuesta de respuesta técnica (`draft_body`).
5. **Lookup en CRM (HubSpot):** Consulta por email si el contacto ya existe en la base. Si existe actualiza sus propiedades; si no, lo da de alta, evitando duplicados (Error 409).
6. **Borrador en Gmail (Human-in-the-Loop):** Genera un borrador con el `draft_body` redactado por el modelo bajo la operación `create`, dejando la respuesta lista para validación humana antes del envío.
7. **Notificación en Slack:** Envía el resumen del ticket al canal interno del equipo.

## Servicios e Integraciones Utilizadas

* **Gmail Trigger / Gmail Node:** Recepción de emails y generación de borradores mediante OAuth2.
* **HubSpot CRM:** Búsqueda, actualización y creación de contactos.
* **Slack:** Notificaciones al canal de operaciones.
* **Groq:** Inferencia para el agente de clasificación y redacción.

## Requisitos y Pasos para Importar

1. Clonar este repositorio o descargar el archivo `checkpoint4_leonel_marinelli.json`.
2. En la instancia de n8n, ir a **Workflows** > **Import from File** y seleccionar el `.json`.
3. Configurar las credenciales correspondientes en cada nodo:
   * Cuenta de Google / Gmail (OAuth2).
   * Cuenta de desarrollador / App de HubSpot (OAuth2 o Private App Token).
   * Webhook o Bot Token de Slack.
   * API Key de Groq en el nodo de modelo del agente.
4. Guardar los cambios y activar el workflow o ejecutar pruebas mediante el disparador manual.