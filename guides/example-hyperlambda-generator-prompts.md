---
name: example-hyperlambda-generator-prompts
when_to_use: When you need example prompts for the `generate-hyperlambda` tool, or rules for phrasing prompts about databases, web scraping, files, HTTP, cryptography, or email.
---

# Example Hyperlambda Generator prompts

Hyperlambda is a DSL that solves most backend software development tasks, and it's especially strong on APIs, database CRUD, web scraping, and file management. Below are example prompts for the `generate-hyperlambda` tool that should work reliably.

* "Create an executable Hyperlambda file accepting paging and sorting arguments that connects to the 'chinook' database and returns 'Artist' rows"
  - Add filtering arguments if you wish.
  - Create HTTP endpoints that create, delete, or update rows.
  - Add logging, or combine with 3rd-party APIs.
  - Return graph objects by saying e.g. "For each 'Artist' row, include all 'Album' rows using 'ArtistId' as your foreign key."
* "Return the first 5 'users' rows for 'magic' database sorted by 'created' descendingly"
  - Creates code that can be immediately executed, returning the requested data.
* "Crawl ainiro.io's sitemap for all URLs that contain '/blog/' in their URLs and return all H1 elements from all pages, in addition to the Markdown of the first 'ARTICLE' element you find."
  - Or return content from individual URLs, save content from URLs into a database, etc.
* "Load the file '/README.md', transform it to Markdown, and return its OpenAI API token count, in addition to the Markdown"
* "Crawl all URLs from ainiro.io's sitemap that contain '/blog/' in their URLs, extract trimmed H1 value and Markdown of the first ARTICLE element you find in HTML. Then insert H1 into 'prompt' and Markdown into 'completion' in the 'magic' database and its 'ml_training_snippets' table. Use 'my_chatbot' as a literal for your 'type' value during insertion."
* "Download 'https://ainiro.io' and measure and return how many milliseconds it took to retrieve the page."
* "Get 'https://ainiro.io/image.jpeg' and return its Content-Type HTTP header"
* "Load '/README.md', encrypt it using AES with password 'xyz', and save the cipher text as 'README_BAK.md'"
* "Create a new RSA keypair for me with bit strength of 4096, and save its public/private keypair as '/etc/public.txt' and '/etc/private.txt'"
* "Load '/public.txt' which is a public RSA key, and use it to encrypt the text 'Thomas Hansen was here' and save the cipher text to '/encrypted.txt'."
* "Calculate the result of (5 + 4) × (3 - 1) and return the result."
* "What weekday is it 5 days from now?"
* "Create a new RSA keypair and save the public key to '/etc/public.txt' and the private key to '/etc/private.txt'"
* "Use the public key at '/etc/john/public_key.txt' to encrypt the file '/README.md' and save the cipher text to '/encrypted.txt'"
* "Executable Hyperlambda file that takes a [channel] argument, and emits a SignalR message to the specified channel, passing in [name] and [email] specified as input arguments to the endpoint."
* "Invoke Stripe API using my bearer token from my 'magic:stripe:secret' configuration setting and return a checkout URL for the price ID 'price_xyz123'."
* "Resize the image found at '/etc/profile.png' to a maximum width of 200px and overwrite the old file."
* "Search DuckDuckGo for 'Thomas Hansen Hyperlambda' and return the first 5 matches."
* "Invoke Chuck Norris API and return a joke"
* "Invoke the Cat Fact Ninja API and return a random cat fact"
* "Create a new user in the 'magic' database and its 'users' table. Insert 'username' as 'foo' and 'password' as 'xyz'. Hash password before saving it."
* "Scrape www.billion-air.org and return the first 5 image URLs found on the landing page. For each image, measure how many milliseconds it takes to load, and return its Content-Length and Content-Type HTTP headers."
* "Get the first user from the 'magic' database and its 'users' table. Return 'username' and 'created' columns only."
* "Load the file '/etc/foo.hl' and replace its existing authorisation requirements, such that only 'root' and 'admin' users can execute the file, then save it back to itself again."
* "Log the following to the audit log; 'Backup of magic database was successfully created'"
* "Select top 20 items from the 'log_entries' table in the 'magic' database sorted by 'created' descending, convert the result to CSV and send as an attachment on email to 'thomas@ainiro.io'"
* "Select all records from 'crm' database and its 'contacts' table. For each row, send an email using the [email] and [name] column. Load the file '/etc/email.html' and apply template arguments for the '[name]' field on the file before using it as your body. Use 'Welcome to Magic' as your subject."
* "Download https://ainiro.io and insert all FAQ items from its JSON schema as question/answer pairs into the 'magic' database and its 'ml_training_snippets' table using the question as the 'prompt' column and the answer as the 'completion' column."
* "Load the CSV file at '/etc/contacts.csv' and insert each row into 'crm' database and its 'contacts' table. `name` from the CSV file goes into the 'full_name' column. `email` from the CSV file goes into the 'company_email' column."
* "Create a QR code leading to ainiro.io and save to '/etc/tmp/'"
* "Send an email to thomas@ainiro.io with subject 'xyz' and body 'qwe'"
* "Start a headless browser instance, go to ainiro.io/contact-us and fill out #name with 'Thomas Hansen', #email with 'thomas@ainiro.io', '#info' with 'Hello from AI agent' and click the #submit_contact_form_button button"

Once generated with `generate-hyperlambda`, the code can be immediately executed and the execution result is returned to you. Call `generate-hyperlambda` with `immediate_mode: true` (no filename) to generate and run in one step; pass a `filename` to save a reusable tool instead.

**IMPORTANT** — The Hyperlambda Generator depends on *exact information*. If the user asks you to create Hyperlambda that interacts with a database but doesn't give the exact fields or columns, use the `get-database-schema` tool to retrieve the database schema so you can generate a correct prompt referencing the right columns and returning the required fields.

**IMPORTANT** — DO NOT ask the Hyperlambda Generator to create code that "returns JSON" to you. This is its *DEFAULT* behaviour and it will *ALWAYS* do that. Saying "return JSON" only confuses it.

**IMPORTANT** — If the user asks you to generate Hyperlambda that interacts with a database you have access to, and the user doesn't give exact fields, use the `get-database-schema` tool to retrieve the schema. This lets you build better prompts that reference all tables, columns, indexes, and foreign keys correctly.

**IMPORTANT** — When creating prompts to scrape a website you CANNOT ask for "get me all products from ...". What you CAN do is ask for "Return all hyperlinks inside of DIV elements containing the 'product' CSS class", etc. The Hyperlambda generator doesn't analyse the HTML — it only generates Hyperlambda code, and it has no idea how to find "the product" or "all services".

## Features

* Anything related to CRUD for MySQL, SQL Server, SQLite, and PostgreSQL.
* Loading and saving files, listing files and folders, and managing files. Mostly text-based files are supported, with rudimentary support for binary files.
* Create an executable Hyperlambda file, or executable Hyperlambda files taking arguments.
* Anything related to web scraping.
* Basic cryptography using AES and RSA.
* Reading configuration settings.
* Performing basic calculations.
