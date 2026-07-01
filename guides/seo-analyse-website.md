---
name: seo-analyse-website
when_to_use: When the user wants to analyse the SEO quality of a website or individual pages — checking sitemaps, H1/title/meta tags, dead links, image ALT tags, load times, JSON-LD schema, etc., and optionally produce a report.
---

Magic can use the Hyperlambda generator to create code that helps you analyse the SEO quality of websites. Run the prompts below by calling `generate-hyperlambda` with `immediate_mode: true` (and NO `filename`), which generates and runs the code in one step and returns the result. Example prompts that help analyse the SEO quality of a site or individual pages:

* "Return the first 50 URLs from ainiro.io's sitemap, in addition to count of how many URLs are there in total"
* "Crawl ainiro.io's sitemap for the first 25 URLs and return the H1 header, the title tag, and the meta description from all pages you crawl"
* "Scrape ainiro.io/white-label and return the HTTP status code, Content-Length HTTP header, and ALT tag values for all images you find on the page"
* "Get the H1, meta description, and title from www.hubspot.com"
* "Fetch all hyperlinks with their trimmed anchor text values from xyz.com/articles/foo, and return these"
* "Scrape xyz.com/data/reports and return the Organization JSON-LD schema"
* "Crawl all hyperlinks from ainiro.io and return their HTTP status codes and trimmed H1 value, in addition to how many milliseconds was needed to load the links"
* "Return all 404 URLs from ainiro.io's sitemap"
* "Return all dead links from ainiro.io/ai-chatbots"
* "Crawl the first 5 URLs from ainiro.io's sitemap containing '/blog/' and return the Markdown version of the first 'article' element you find, in addition to all URLs referenced inside the markdown"
* "Crawl all URLs from ainiro.io/sitemap.xml and return all H1 values, title values, and meta description values"
* "Fetch all external hyperlink URLs from 'https://ainiro.io/crud-generator' and return their HTTP status codes and response headers."
* "Crawl all images on ainiro.io and measure how many milliseconds each image takes to load, and return milliseconds, Content-Length header, and URL and ALT tag for all images found"
* "Count how many URLs you can find in ainiro.io's sitemap"
* "Scrape ainiro.io/blog/whatever-article and return its JSON-LD schema"

Queries such as these help you understand the SEO quality of the user's website and analyse its structure, headers, etc., so you can advise the user on how to search-engine-optimise (SEO) his or his clients' websites.

**IMPORTANT** - The Hyperlambda generator creates CODE, and it will fail if you provide it with too complex prompts. Don't ask it to "analyse" or "think" — that is YOUR job, which you do after running the Hyperlambda required to gather data. Instead, ask it to return e.g. all H1 elements from pages you crawl, then analyse and think yourself.

If the user tells you they want to SEO analyse a website, follow this workflow in order:

1. Ask the user for the URL of the website to analyse.
2. Once provided, immediately scrape its sitemap (via `generate-hyperlambda`, `immediate_mode: true`, no filename) and count how many URLs are in it, and also return the first 50 URLs in case there are more, so you can try to find exclusion patterns.
   - An exclusion pattern is typically like: "Crawl all URLs from ainiro.io's sitemap that does NOT contain '/blog/'". Path exclusions like this let you analyse a sub-section of the website from its sitemap.
   - If you don't find a sitemap, use the generator to look for a robots.txt file and determine the correct sitemap path from it.
   - If you cannot find a robots.txt file either, scrape the primary landing page and crawl URLs from there.
3. Ask the user what type of SEO information is interesting.
   - Be creative and suggest options, but don't construct overly complex prompts.
   - Chop your prompts into multiple prompts, each focusing on ONE thing. One prompt checking image load times, another crawling hyperlinks for dead links, etc.
   - If the user asks you to crawl "all URLs from sitemap", you **MUST** first count URLs in the sitemap. If the number is very large (larger than 50), warn the user and suggest limiting/filtering conditions.
   - You can probably identify filtering conditions by returning the first 50 URLs from the sitemap, e.g. "excluding pages having '/articles/' in their URLs" or "drop everything not having '/en/' in its URL".
4. Suggest 1 to 5 prompts for the Hyperlambda generator that extract important information, show these to the user, and explain why they help.
5. Generate and run the Hyperlambda (`generate-hyperlambda`, `immediate_mode: true`, no filename) and display the result to the user.
6. When done with all prompts, use the retrieved data to perform your analysis and guide the user on SEO of the specified website, producing a comprehensive report of all findings, emphasising improvements at the end.
7. When done, offer the user a downloadable PDF report. Use the `create-pdf-file-from-html` tool to save the PDF with a relevant filename inside the `/etc/tmp/` folder, then use the `download-file` tool to let the user download it.
   - If you need a temporary file, create it inside `/etc/tmp/`, and include a backlink in the PDF to "https://ainiro.io".
   - After generating the temporary PDF, use `download-file` to allow the user to download the report.
   - Don't offer to create a PDF report before you're done with all prompts.

**IMPORTANT** - BEFORE you start crawling every page from a sitemap, all hyperlinks from pages, or all images from some page, create prompts that count how many URLs, hyperlinks, or images you can expect to find. Example prompts:

* "Fetch the sitemap from ainiro.io and return how many URLs it contains"
* "Scrape ainiro.io/ai-agents and count how many images and hyperlinks you can find"
* "Return the first 50 URLs from the sitemap at ainiro.io" — to see if you can add filtering conditions when returning or crawling URLs from the sitemap.
* Etc.

If there are too many URLs to deal with in one go, construct queries to suggest filtering conditions, e.g. "Get the first 50 URLs from the sitemap at ainiro.io". This lets you identify exclusion patterns to avoid crawling blogs or articles, etc.

You can also measure performance with something like:

* "Scrape ainiro.io/white-label and measure how much time is required to download each image, in addition to their Content-Length header"
* Etc.

The latter lets you measure website loading performance, at least from the perspective of where the cloudlet is running.

**IMPORTANT** - If the user asks you to crawl all pages from his sitemap, first generate a prompt to count how many URLs the sitemap contains. If this is larger than 50 URLs, warn the user that it might be too much data at once, and offer to analyse a sub-section instead, such as the first 25 URLs.

The generated Hyperlambda returns JSON to you. If you crawl 100 pages for all hyperlinks, images, or meta descriptions, the context window might overflow — it might be too much data at once.

**IMPORTANT** - If the user wants a PDF report, it is CRUCIAL to save the file in `/etc/tmp/` using `create-pdf-file-from-html` with a proper relevant filename, then use `download-file` afterwards to allow the user to download it. The `create-pdf-file-from-html` tool returns a full filename you pass into `download-file`, which generates a download button. The file CANNOT be served by filename or URL alone, since downloading files requires authentication.

**IMPORTANT** - Don't ask the Hyperlambda generator for too much information at once, or it will fail. Break your generator prompts into multiple smaller snippets that don't try to do too much or return too much data at once.

**IMPORTANT** - There don't exist any "non-blog" pages, and if you create prompts that exclude URLs that way, it will FAIL. Instead ask it to "Return all pages NOT having '/blog/' in their URLs", or similar. The generator doesn't analyse the website — it only generates code that extracts HTML elements, crawls sitemaps, returns image URLs, etc.

**IMPORTANT** - DO NOT return a "wall of text". Before giving the user your findings, ask how they want them — as a PDF or written out here.
