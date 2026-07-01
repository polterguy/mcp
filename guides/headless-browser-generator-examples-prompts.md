---
name: headless-browser-generator-examples-prompts
when_to_use: When generating Hyperlambda that drives a headless browser (Puppeteer) to navigate, fill forms, click, screenshot, or scrape rendered pages.
---

# Headless browser generator example prompts

Magic uses Puppeteer as a headless browser to automate web browsing. Below are example prompts for the `generate-hyperlambda` tool that produce Hyperlambda using Magic's Puppeteer slots.

- "Create a headless browser, open 'ainiro.io', wait until the page has loaded, and create a screenshot and save to '/etc/tmp.png'. When done, make sure you close the browser session."
- "Use a headless browser to go to 'ainiro.io/contact-us', wait for the '#name' selector to be visible, fill in '#name' with 'Thomas', '#email' with 'thomas@gaiasoul.com', and click the '#submit' button. Wait for two seconds, and close the headless browser instance."
- "Go to www.hubspot.com using puppeteer and retrieve the HTML, transform it to markdown, and return the result."
- "Go to www.salesforce.com using a headless browser and retrieve the HTML, then traverse the HTML for all hyperlinks, and return their anchor text and absolute URLs. Make sure you close the browser session when done."
- "Go to www.google.com using a headless browser and return the browser session id to the caller as 'browser_session'"

Etc ...

The headless browser uses a lot of memory, so for most prompts you'd either want to return the browser session to reuse it, and/or close it immediately to avoid a 'dangling' session. You cannot open more than 5 browser sessions at the same time. If a browser session is not used for 15 minutes it is automatically closed (default, can be overridden). Always disconnect from a headless session when done, since it keeps the browser alive and consumes RAM.

## Slot documentation

The following documents the Puppeteer Hyperlambda slots so you know what natural-language prompts to give `generate-hyperlambda`.

### [puppeteer.connect]

Launches Chromium, opens a page, and returns a `session_id`.

Arguments:

* `headless` - Boolean. Launch Chromium in headless mode. Defaults to true.
* `executable` - Full path to Chromium/Chrome binary.
* `executable-path` - Alias for `executable`.
* `timeout` - Launch timeout in milliseconds.
* `user-data-dir` - Directory for Chromium user data.
* `args` - Additional Chromium arguments. Each child value is one argument string.
* `timeout-minutes` - Sliding inactivity timeout in minutes. Defaults to 15.
* `max-lifetime-minutes` - Maximum session lifetime in minutes. Defaults to 120.

### [puppeteer.close]

Closes the browser and page associated with a session.

Arguments:

* Slot value - Session identifier returned by **[puppeteer.connect]**.

### [puppeteer.goto]

Navigates to a URL.

Arguments:

* Slot value - Session identifier.
* `url` - URL to navigate to.
* `timeout` - Navigation timeout in milliseconds.
* `wait-until` - One of `load`, `domcontentloaded`, `networkidle0`, `networkidle2`.

### [puppeteer.wait-for-url]

Waits until the page URL matches.

Arguments:

* Slot value - Session identifier.
* `url` - URL string to wait for.
* `timeout` - Timeout in milliseconds.

### [puppeteer.wait-for-selector]

Waits until a selector appears in the DOM.

Arguments:

* Slot value - Session identifier.
* `selector` - CSS selector to wait for.
* `timeout` - Timeout in milliseconds.
* `visible` - Boolean. Require element to be visible.
* `hidden` - Boolean. Require element to be hidden.

### [puppeteer.click]

Clicks a selector.

Arguments:

* Slot value - Session identifier.
* `selector` - CSS selector to click.
* `button` - Mouse button: `left`, `middle`, or `right`.
* `click-count` - Number of clicks.
* `delay` - Delay between down and up in milliseconds.

### [puppeteer.type]

Types text into a selector.

Arguments:

* Slot value - Session identifier.
* `selector` - CSS selector to type into.
* `text` - Text to type.
* `delay` - Delay between key presses in milliseconds.

### [puppeteer.fill]

Clears and types text into a selector.

Arguments:

* Slot value - Session identifier.
* `selector` - CSS selector to fill.
* `text` - Text to type after clearing.
* `delay` - Delay between key presses in milliseconds.

### [puppeteer.press]

Presses a key on a selector.

Arguments:

* Slot value - Session identifier.
* `selector` - CSS selector to focus.
* `key` - Key name (for example `Enter`).
* `delay` - Delay in milliseconds.

### [puppeteer.select]

Selects option values from a selector.

Arguments:

* Slot value - Session identifier.
* `selector` - CSS selector for a `<select>` element.
* `values` - Values to select. Provide as children or a comma-separated string.

### [puppeteer.content]

Returns page HTML.

Arguments:

* Slot value - Session identifier.

### [puppeteer.title]

Returns page title.

Arguments:

* Slot value - Session identifier.

### [puppeteer.url]

Returns current URL.

Arguments:

* Slot value - Session identifier.

### [puppeteer.screenshot]

Saves a screenshot to disk.

Arguments:

* Slot value - Session identifier.
* `filename` - Output file path.
* `full-page` - Boolean. Capture full scrollable page.
* `type` - `png` or `jpeg`.
* `quality` - JPEG quality 0-100 (only for `jpeg`).

### [puppeteer.evaluate]

Evaluates a JavaScript expression in the page.

Arguments:

* Slot value - Session identifier.
* `expression` - JavaScript expression string.
* `args` - Optional arguments for JS function invocation. Each child value is one argument.
