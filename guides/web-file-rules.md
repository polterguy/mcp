---
name: web-file-rules
when_to_use: Creating or updating frontend files (HTML, CSS, JavaScript) served from the web root.
---

# Web file rules

## Rules

1. Magic serves frontend files from `/etc/www/`.
2. When generating frontend HTML, CSS, or JavaScript files, use the `create-file` tool.
3. Do not save files directly in `/etc/www/` unless the user explicitly confirms that is OK. Prefer subfolders — the preferred way to create an app is `/etc/www/APP/index.html`.
4. Files saved under `/etc/www/` are served from web root `/`, not from `/etc/www/` in the URL.
5. Create the folder with `create-folder` before the first file write into a new path. If a file write fails with an unclear error, check that the parent folder exists before considering any other cause.
6. When updating CSS or JavaScript referenced by HTML, add a cache-busting query parameter named `v`, such as `app.js?v=SOME_VALUE` or `app.css?v=SOME_VALUE`, and change the `v` value every time the file changes.

## Canonical URLs and asset paths

Every HTML file is served ONLY at its canonical, extension-less URL:

- `/etc/www/foo/index.html` → canonical URL `/foo`. The forms `/foo/`, `/foo/index.html`, and `/foo/index` all 301-redirect to `/foo`.
- `/etc/www/foo/bar.html` → canonical URL `/foo/bar`. The form `/foo/bar.html` 301-redirects to `/foo/bar`.

Consequences:

1. Because canonical URLs have no trailing slash, the browser resolves relative asset paths against the parent path, not the page's folder: a relative `app.css` reference in `/foo/index.html` requests `/app.css`, which 404s and leaves the page unstyled and dead. **Always reference assets with root-absolute paths** — `/foo/app.css?v=1`, `/foo/app.js?v=1` — never relative paths.
2. Links between pages use canonical, root-absolute URLs too: link to `/foo/bar`, never `bar.html` or `/foo/bar.html`.
3. Use the canonical form in links, documentation, and testing. A 301 on the non-canonical forms is this canonicalization at work — but treat any redirect you did not expect as a signal to investigate what it does to path resolution, never as noise to dismiss.

## Visibility toggling (`hidden` vs `display`)

The HTML `hidden` attribute only applies user-agent-level `display: none`. ANY author stylesheet rule that sets `display` (such as `.modal-backdrop { display: grid }` or `#app-view { display: flex }`) overrides it by spec, making "hidden" elements visible — a classic bug is modals and the authenticated app shell rendering on top of the login screen. Therefore:

1. If visibility is toggled with the `hidden` attribute, the stylesheet MUST contain `[hidden] { display: none !important; }` before any layout rules.
2. Alternatively, toggle a class (e.g. `.is-hidden { display: none !important; }`) instead of the attribute.
3. Never mix the `hidden` attribute with elements that have author-level `display` rules unless one of the two guards above is in place.

## Verify by rendering

A frontend that returns HTTP 200 is NOT verified. Broken asset paths and wrong initial visibility are invisible at HTTP level and obvious at first render. After creating or updating a frontend, ALWAYS verify by rendering it with the headless-browser tools before declaring it done:

1. `puppeteer-connect`, then `puppeteer-goto` the app's canonical URL (e.g. `https://HOST/foo`).
2. `puppeteer-evaluate` to confirm assets loaded and the initial state is correct, for example:
   `JSON.stringify({css: document.styleSheets.length, visible: [...document.body.children].filter(e => getComputedStyle(e).display !== 'none').map(e => e.id || e.tagName)})`
   Expect `css` above zero, and only the intended initial containers visible (e.g. login visible; app shell and modals not).
3. `puppeteer-screenshot` for a visual check when in doubt, then `puppeteer-close`.
