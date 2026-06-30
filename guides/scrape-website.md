---
name: scrape-website
when_to_use: Scraping or crawling a website, extracting structured data from HTML pages, or pulling content that sits behind navigation, a login, or JavaScript rendering.
---

# Scraping a website

## 1. Pick the right tool first
- **Static HTML** — the content you want is already in the raw response. Use an HTTP GET
  (`invoke-http`, or `http.get` in generated Hyperlambda) and parse the markup. Cheapest and
  fastest, so always try this first.
- **JavaScript-rendered, or behind navigation / login** — the content only appears after the
  page runs scripts or you click around. Drive a headless browser with the `puppeteer-*`
  tools so the page is fully rendered before you read it.

## 2. Static pages
1. GET the URL.
2. Convert the HTML to a lambda structure (`html2lambda`) so you can navigate it as nodes.
3. Locate the elements you want, then project their text / attribute values into clean records.

Gotchas:
- Filtering an element by attribute must anchor the attribute step under the element's own
  children — descend one level per nesting level.
- The inner text of an element can be split across several text fragments; join them when you
  read a node's text.

## 3. Dynamic pages (headless browser)
Open a session, reuse its **session id** for every action *and* the final close:
1. `puppeteer-connect` → returns a session id.
2. `puppeteer-goto` the URL.
3. `puppeteer-wait-for-selector` for the element that proves the content rendered.
4. `puppeteer-content` (read rendered HTML) or `puppeteer-evaluate` (pull values via JS).
5. `puppeteer-close` the session — always, even on failure.

## 4. Be a good citizen
- Put a small delay between requests; do not hammer a host.
- Send a sane User-Agent; some hosts reject blank ones.
- Respect the site's terms and robots — only scrape what you are allowed to.

## 5. Shape it in a second step
Scrape first into flat records, **then** filter / sort / project. Do not try to fetch + filter
+ deeply project in a single pass — it degrades both the generation and the result.
