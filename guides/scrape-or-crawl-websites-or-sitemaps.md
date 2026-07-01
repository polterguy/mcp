---
name: scrape-or-crawl-websites-or-sitemaps
when_to_use: When the user wants to scrape a website, crawl a sitemap or hyperlinks, extract HTML elements/images/links, check for dead links, or convert pages to Markdown.
---

**IMPORTANT** - If the user wants to scrape a website, crawl a sitemap, or something similar, offer to follow this workflow and inform the user of what you can do using the Hyperlambda generator.

You can use the Hyperlambda generator to create code on the fly for web-scraping operations. Call the `generate-hyperlambda` tool with `immediate_mode: true` (and NO `filename`) to generate AND run the code in a single step, returning the result without saving it. Below are examples of prompts that work with the generator and produce working code whose results you can use:

* "Crawl ainiro.io's sitemap for its first three URLs not containing '/blog/' in their URLs and return H1 headers from all pages"
* "Scrape ainiro.io/white-label and return the first 20 images you find. Return both alt values and URLs. Make sure you return absolute URLs"
* "Get the H1, meta description, and title from www.hubspot.com"
* "Return all JSON-LD schema blocks from ainiro.io as structured content"
* "Fetch all hyperlinks with their trimmed text values from xyz.com/articles/foo, and return both URLs and a list of CSS classes associated with each hyperlink"
* "Scrape xyz.com/data/reports and return the trimmed text of all LI items having the 'product' CSS class"
* "Crawl all hyperlinks you find at howdy.com/whatever and return their HTTP status codes, in addition to their Content-Type"
* "Return all 404 URLs from ainiro.io's sitemap"
* "Return all dead links from ainiro.io/white-label"
* "Crawl the first 5 URLs from ainiro.io's sitemap containing '/blog/' and return the Markdown version of the first 'article' element you find, in addition to all URLs referenced inside the markdown"
* "Crawl all URLs from ainiro.io/sitemap.xml and return all H1 values, title values, and meta description values"
* "Crawl all URLs from ainiro.io/ai-agents and insert these into database x, table y, having columns 'url' and 'text'"
* "Fetch all external hyperlink URLs from 'https://ainiro.io/crud-generator' and return their HTTP status codes and response headers."
* "Scrape ainiro.io, fetch all image URLs, and insert these into the 'cms' database and its 'images' table as absolute URLs"

The above are just examples. If you describe what you want to retrieve from any HTML page, sitemap, or similar, the Hyperlambda generator can typically solve the problem — including crawling hyperlinks it finds on web pages and converting HTML to Markdown.

Hence, if the user asks you to scrape a website or anything related to web scraping, offer to use the Hyperlambda generator. Call `generate-hyperlambda` with `immediate_mode: true` and no filename to create throw-away Hyperlambda code that runs immediately and returns its result in the same step.

**IMPORTANT** - DO NOT save such tools unless the user explicitly tells you to! You also do NOT need to instruct the generator to return JSON — that is its default.

If the user wants a reusable tool, invoke the Hyperlambda generator once more with a similar prompt, but this time start with "Generate an Executable Hyperlambda file that ...", mentioning input arguments in your prompt. This gives you a function taking arguments. To persist it, call `generate-hyperlambda` with a `filename` so it is saved as a Hyperlambda file in a module. Once saved, the endpoint is AUTOMATICALLY exposed as an MCP tool — there is no separate registration step. Do NOT save it unless the user asks, and only offer to do so *after* having verified the function works as expected.

If the user asks you to create web-scraping tools, follow this process unless the user explicitly tells you otherwise:

1. Suggest using the Hyperlambda generator to create the scraping tools, and display the prompt(s) you intend to use to the user before running them.
2. Generate and run the Hyperlambda by calling `generate-hyperlambda` with `immediate_mode: true` and no filename.
3. The result is returned in that same call — display it to the user.
4. NEVER change the Hyperlambda code without using the Hyperlambda generator to create new code.

**IMPORTANT** - If the user asks you to change the Hyperlambda code, change your *prompt* and rerun it through the generator.

**NEVER** change the Hyperlambda returned by the generator. If the user wants to modify the code, modify your PROMPT and rerun it through `generate-hyperlambda`, then use the new code returned.

**CRITICAL RULE** — **DO NOT** manually modify or rewrite Hyperlambda code. If the user requests any change to previously generated Hyperlambda (even a small one), you must:

1. Create a new prompt describing the desired result, including any changes the user asked for.
2. Re-invoke `generate-hyperlambda` with that prompt.
3. Use the new code returned by the generator.
4. Never manually alter, patch, or extend existing Hyperlambda code — not even for demonstration purposes. All changes must go through the Hyperlambda generator to ensure correctness, reproducibility, and compliance with Magic Cloud's deterministic code-generation policy.

## No semantic prompts!

When suggesting prompts for the Hyperlambda generator that involve web crawling, scraping, or data extraction, you must:

1. Avoid semantic terms such as "product names," "prices," or "author."
2. Use only explicit structural selectors (e.g., HTML tags, CSS classes, or URL patterns).
3. Never assume semantic meaning of page content — only describe how to locate it.
4. If the user provides a semantic description, ask for the corresponding structural selectors before generating or suggesting a prompt. Alternatively, first generate and run a prompt that returns the *complete HTML* and determine selectors yourself that match the user's intentions.
