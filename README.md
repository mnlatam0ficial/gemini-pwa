# Gemini PWA

Cliente de chat instalable (Progressive Web App) para **Gemini 3.8 Flash**. Tú pegas tu propia API Key; no hay ninguna clave hardcodeada.

## Qué incluye

- Chat con historial en pantalla y en `localStorage`
- Campo de API Key (guardar / mostrar / ocultar)
- Toggle **Godmode / jailbreak**
- Indicador de “escribiendo…”
- Markdown básico en las respuestas
- Diseño mobile-first
- `manifest.json` + service worker para instalación y uso offline de la interfaz

## Cómo obtener una API Key

1. Entra en [Google AI Studio](https://aistudio.google.com/apikey)
2. Crea una clave para la API de Gemini
3. Pégala en Ajustes de esta PWA y pulsa **Guardar clave**

La clave se queda solo en `localStorage` de tu navegador. Cualquier llamada sale de tu dispositivo hacia `generativelanguage.googleapis.com`.

## Cómo usarla

Necesitas servir los archivos por HTTP (el service worker y los módulos ES no funcionan bien en `file://`).

```bash
# desde la carpeta mi-pwa-gemini
python3 -m http.server 8080
# o
npx --yes serve .
```

Abre la URL en el navegador. En Chrome / Edge / Safari puedes **Instalar** o **Añadir a la pantalla de inicio**.

## Modelo y SDK

- Modelo: `gemini-3.8-flash`
- SDK: `@google/genai` (`import { GoogleGenAI } from "@google/genai"`)
- Método principal: `ai.interactions.create(...)`
- Si esa llamada falla, hay un fallback a `ai.models.generateContent`

En el SDK actual de Interactions, los filtros van en `safety_settings` (snake_case) en el cuerpo de la interacción. El cliente también envía `config.safetySettings` con las categorías clásicas que pediste.

## Godmode / jailbreak

Cuando el interruptor está **activado**:

1. Se envían `safety_settings` / `safetySettings` a **BLOCK_NONE** para:
   - `HARM_CATEGORY_HARASSMENT`
   - `HARM_CATEGORY_HATE_SPEECH`
   - `HARM_CATEGORY_SEXUALLY_EXPLICIT`
   - `HARM_CATEGORY_DANGEROUS_CONTENT`
   - (y, en Interactions, también `jailbreak` y `civic_integrity`)
2. Se antepone el system prompt **Godmode** (inversión de rechazos) a cada mensaje.

Cuando está **apagado**, no se envía ese prompt ni se fuerzan los filtros a `BLOCK_NONE`.

## Advertencia

**BLOCK_NONE** y el prompt Godmode relajan o intentan eludir las salvaguardas del modelo.

- Puede violar las [normas de uso de Google](https://ai.google.dev/gemini-api/docs/usage-policies)
- Google puede restringir o cerrar la API Key
- El modelo puede seguir bloqueando algunas peticiones a nivel de cuenta
- Eres responsable del contenido que generas y de cómo lo usas
- No uses esto para actividad ilegal ni para dañar a otras personas

Esta PWA es una herramienta local. Úsala bajo tu responsabilidad.

## Archivos

```
mi-pwa-gemini/
├── index.html
├── manifest.json
├── sw.js
├── style.css
├── script.js
├── icon-192.png
├── icon-512.png
└── README.md
```

## Privacidad

- Sin backend propio
- Sin analítica
- Offline: se cachean los estáticos; las llamadas a Gemini requieren red
