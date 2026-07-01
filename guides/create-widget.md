---
name: create-widget
when_to_use: When the user wants to design a reusable HTML widget for the embedded chatbot UI (note - live widget rendering/testing is an embedded-chatbot feature, not available over MCP).
---

# Create a widget

A widget is a reusable snippet of HTML that can be dynamically injected into an embedded chatbot conversation to collect data, display rich HTML, etc. Widgets are shown by attaching them to a machine learning type so the LLM triggers them via natural language instructions.

**Scope note:** the live widget-injection, debugging, and rendering steps are features of the embedded chatbot GUI and are NOT available over MCP. Over MCP you can still author the widget's HTML/CSS/JS files and the backing API. Use this guide for the design rules and to save the files.

Ask the user for the following:

1. What HTML the widget should contain, and what it should do.
   - If it needs an API, generate one with `generate-hyperlambda` first (pass a `filename` so the endpoint is saved and automatically exposed as an MCP tool).
2. Where to save the widget's HTML.
   - By default widgets are saved as HTML files inside a module, in a folder named e.g. `widgets`. Offer to create a module with the `create-module` tool unless the user specifies an existing one, and make sure all folders exist before saving the HTML.
3. Where to save CSS and JavaScript files.
   - If the widget needs JS or CSS files, save them somewhere inside `/etc/www/` so they can be included from the HTML.

## Rules for saving the widget

1. Save the widget's HTML without adding comments to it. Before saving, verify the module folder exists and that it has a `widgets` sub-folder — use the `list-folders` tool to check.
2. The same widget can be displayed multiple times. To avoid clashes between instances, all `id` and `name` attributes, CSS selectors, JavaScript namespaces, and JavaScript functions must start with the exact text `WIDGET_ID_UNIQUE_NUMBER`. This placeholder is dynamically replaced with a random value when the widget is rendered, avoiding UI bugs.
3. Do NOT rely on `DOMContentLoaded` or similar events. Widgets are injected dynamically into the DOM, so do initialisation directly in JavaScript after the HTML is rendered.

If you need background image references, use the `background-images` guide.
