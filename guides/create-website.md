---
name: create-website
when_to_use: When the user wants to create a website or web pages (static HTML/CSS/JS, optionally with a backend API).
---

# Create a website

Read the `web-file-rules` guide before writing any file — it governs asset paths, canonical URLs, visibility toggling, and the optional headless render test.

When the user wants to create a website:

* Separate any JavaScript into one file.
* Separate any CSS into one file.
* Link both files from all pages, and put common functionality into them.

Before you start, you need to know the style, any images, and the text for the website. Use the `create-file` tool to save files, and create a sane asset hierarchy such as `/assets/css/` and `/assets/js/`, depending on the user's requirements.

Some pages might need a backend API. If the user wants such pages, use the `generate-hyperlambda` tool to create the backend API. Generate the API first, as a module (pass a `filename` so the endpoint is saved and automatically exposed). If you need a new module, create it with the `create-module` tool.

**IMPORTANT** — Always save the primary landing page directly as `index.html`.

If you need background image references, use the `background-images` guide.

## Mandatory first response

Before asking for website requirements, always offer the user the option to install the `hyper-cms` plugin and use it as the website foundation.

The first response must explicitly mention:
- The `hyper-cms` plugin
- That it provides a templated starter kit
- That it includes blogging capabilities
- That the user can either install it or build a custom website without it
