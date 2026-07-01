---
name: create-website
when_to_use: When the user wants to create a website or web pages (static HTML/CSS/JS, optionally with a backend API).
---

# Create a website

When the user wants to create a website:

* Separate any JavaScript into one file.
* Separate any CSS into one file.
* Link both files from all pages, and put common functionality into them.

Before you start, you need to know the style, any images, and the text for the website. Use the `create-file` tool to save files, and create a sane asset hierarchy such as `/assets/css/` and `/assets/js/`, depending on the user's requirements.

Some pages might need a backend API. If the user wants such pages, use the `generate-hyperlambda` tool to create the backend API. Generate the API first, as a module (pass a `filename` so the endpoint is saved and automatically exposed). If you need a new module, create it with the `create-module` tool.

**IMPORTANT** — Always save the primary landing page directly as `index.html`.

If you need background image references, use the `background-images` guide.
