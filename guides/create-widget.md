---
name: create-widget
when_to_use: When the user wants to create an HTML widget for the embedded chatbot UI, or when authoring/editing widget HTML/CSS/JS code.
---

# Create a widget

A widget is a reusable snippet of HTML that can be dynamically injected into an embedded chatbot conversation to collect data, display rich HTML, etc. Widgets are shown by attaching them to a machine learning type so the LLM triggers them via natural language instructions.

**Scope note:** live rendering/previewing a widget inline is an embedded chatbot GUI feature with no MCP equivalent. Over MCP you author and save the widget's HTML/CSS/JS files and the backing API, and can attach the widget to a machine learning type with the `add-html-widget` tool.

Ask the user for the following:

1. What HTML the widget should contain, and what it should do.
   - If it needs an API, generate one with `generate-hyperlambda` first (pass a `filename` so the endpoint is saved and automatically exposed as an MCP tool).
2. Where to save the widget's HTML.
   - Widgets are saved as HTML files inside a module, in a folder named e.g. `widgets` — never in `/etc/www/`. Offer to create a module with the `create-module` tool unless the user specifies an existing one, and verify the module folder and its `widgets` sub-folder exist with the `list-folders` tool before saving.
3. Where to save CSS and JavaScript files.
   - If the widget needs JS or CSS files, save them somewhere inside `/etc/www/` so they can be included from the HTML.
4. Whether to attach the widget to a machine learning type, using the `add-html-widget` tool.

## Widget code rules

1. The same widget can be displayed multiple times. To avoid clashes between instances, all `id` and `name` attributes, CSS selectors, JavaScript namespaces, and JavaScript functions must start with the exact text `WIDGET_ID_UNIQUE_NUMBER`. This placeholder is dynamically replaced with a random value when the widget is rendered, avoiding UI bugs.
2. Do NOT rely on `DOMContentLoaded` or similar events. Widgets are injected dynamically into the DOM, so do initialisation directly in JavaScript after the HTML is rendered.
3. Widgets are rendered into a shadow DOM, so do not use `document.querySelector`, `document.getElementById`, or `document.querySelectorAll`. Use:
   - `ainiro.$`
   - `ainiro.$id`
   - `ainiro.$$`
4. Always use absolute URLs with the backend URL in JavaScript when fetching data from the backend.
5. Account for HTTP endpoints returning empty string instead of `[]` or an empty object.
6. By default, the root HTML element should have `min-width: 80%` unless the user asks for something else.
7. Save the widget's HTML without adding comments to it.

If you need background image references, use the `background-images` guide.
