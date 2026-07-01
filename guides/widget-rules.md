---
name: widget-rules
when_to_use: Designing HTML/CSS/JS widget code (note: rendering/attaching widgets is an embedded-chatbot feature, not available over MCP).
---

# Widget rules

Note: actually *rendering* or *attaching* HTML widgets is a feature of the embedded chatbot GUI
and is not available over MCP. The design guidance below still applies when you author widget
HTML/CSS/JS — for example, code generated for an endpoint or stored in a module.

## Core rules

1. Always use absolute URLs with the backend URL in JavaScript when fetching data from the backend.
2. Never use `DOMContentLoaded` in widget JavaScript.
3. Prefix all widget HTML `id` attributes, CSS selectors, JavaScript namespaces, and JavaScript functions with `WIDGET_ID_UNIQUE_NUMBER`.
4. Save widget HTML inside a module, not in `/etc/www/`.
5. Account for HTTP endpoints returning empty string instead of `[]` or an empty object.
6. If the widget requires a backend, offer to create the API using `generate-hyperlambda` (pass a `filename` to save it as a reusable endpoint, which is then automatically exposed as an MCP tool).
7. Widgets are rendered into a shadow DOM, so do not use `document.querySelector`, `document.getElementById`, or `document.querySelectorAll`. Use:
   - `ainiro.$`
   - `ainiro.$id`
   - `ainiro.$$`
8. By default, the root HTML element should have `min-width: 80%` unless the user asks for something else.

## Embedded-chatbot only

The following are GUI features of the embedded chatbot and have no MCP equivalent — do not
attempt them over MCP:

- Rendering a widget inline in the chatbot.
- Attaching a widget to an AI chatbot or machine learning type.
