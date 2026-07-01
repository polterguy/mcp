---
name: headless-browser-functions
when_to_use: When you need to drive a headless browser - navigate, click, fill forms, log in, run JavaScript, or read JavaScript-rendered pages - using the puppeteer tools.
---

# Headless browser functions

These are the available headless-browser tools, backed by PuppeteerSharp. They are low-level building blocks: you create a session, act on pages using the session id, and close the session when finished.

Each is exposed as a `puppeteer-*` MCP tool; call them directly by name.

If the user wants the browser to remember cookies, login state, or previous browsing state, call `puppeteer-connect` with a stable `user-data-dir` value and reuse the same directory in later sessions.

If the user needs to type a password or other secret into a form field, prefer `puppeteer-fill` or `puppeteer-type` with `config-key` instead of inline `text`, so the secret is resolved from configuration without exposing it to the LLM.

Available tools:

- `puppeteer-connect` — Creates a new headless browser session and returns a session id.
- `puppeteer-close` — Closes an existing session.
- `puppeteer-goto` — Navigates a session to a URL.
- `puppeteer-wait-for-url` — Waits until the current URL matches a value.
- `puppeteer-wait-for-selector` — Waits for a selector to appear (optionally visible/hidden).
- `puppeteer-click` — Clicks a selector.
- `puppeteer-type` — Types text into a selector.
- `puppeteer-fill` — Clears and types text into a selector.
- `puppeteer-press` — Presses a key on a selector.
- `puppeteer-select` — Selects option values in a `<select>` element.
- `puppeteer-content` — Returns the current page HTML.
- `puppeteer-title` — Returns the current page title.
- `puppeteer-url` — Returns the current page URL.
- `puppeteer-screenshot` — Saves a screenshot to disk.
- `puppeteer-evaluate` — Evaluates JavaScript in the page.

Pass the session id returned by `puppeteer-connect` to every subsequent action on that session, and to the final `puppeteer-close`.

**NOTICE** — You can open a maximum of 5 sessions at the same time, but you can reuse sessions. Keep a session open until the user tells you to close it, in case they want to continue. Remind the user to tell you when to close the session.
