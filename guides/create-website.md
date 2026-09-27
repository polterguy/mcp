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

## Server-side rendering with `{{` and `}}`

A page can pull content from the backend with no JavaScript, by putting a lambda
expression between `{{` and `}}` and pairing the page with a Hyperlambda
codebehind file of the same name — `/etc/www/foo/index.html` pairs with
`/etc/www/foo/index.hl`. The codebehind's root nodes are what the page's
expressions resolve against, so `{{*/.title}}` renders the `[.title]` node.
Generate the codebehind with the `generate-hyperlambda` tool like any other
Hyperlambda, passing the page's path with an `.hl` extension as `filename`.

1. **The codebehind file is what activates the syntax.** With no same-named
   `.hl` file the page is served statically and `{{ }}` reaches the browser
   verbatim. That is the usual reason braces show up on screen.
2. Only extension-less URLs and those ending in `.html`, `.xml` or `.txt` are
   rendered. CSS and JavaScript files are always static — never put `{{ }}` in
   them.
3. `[.oninit]` nodes in the codebehind run once, before any substitution. Load
   data there, and declare the nodes the page references above `[.oninit]` so
   its expressions can reach them.
4. A referenced node that has a value inserts that value. A node with no value
   is executed as a lambda and inserts whatever it returns, so one node can
   render a whole fragment.
5. Nothing is HTML-escaped on the way in. Escape anything user-supplied in the
   codebehind before it reaches the page.
6. A reference matching no node fails the request with `Expression '...'
   referenced in mixin file '...' returned no nodes`, and an unclosed `{{` with
   `Server side include was never closed!`. Neither renders as a blank.
7. In a rendered page `\` is an escape character: write a literal brace as `\{`.
   This also means backslashes in inline `<script>` blocks are consumed — one
   more reason to keep JavaScript in its own file, where it is served
   untouched.
