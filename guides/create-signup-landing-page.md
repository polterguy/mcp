---
name: create-signup-landing-page
when_to_use: When the user wants a landing page for signing up to something — a conference, newsletter, course, etc. — backed by a database and a public create API.
---

# Create a signup landing page

Use this when the user wants a landing page for signing up to things (a conference, an email newsletter, a course, etc.). Such a page needs a database storing the signup's name, email, and phone number, an API with a create method to save the data, and a frontend that talks to that API. Before starting, determine the following.

1. Ask whether the user wants to embed an AI chatbot on the landing page.
   - If yes, follow the `create-ai-chatbot` guide. Once the embed script is created, continue with step 2.
2. Suggest a database design, and create it once the user is satisfied.
   - Ask what data the user wants to save, and adjust the design accordingly.
   - Show the design as a SIMPLE MermaidJS chart, obeying all Mermaid rules (see the `mermaid-rules` guide).
   - When the user confirms, create the database with `create-sqlite-database` and apply the schema with `execute-sql`.
3. Ask what design the user wants — offer futuristic, modern, classic, etc. Default to "modern design with a background image and glass morphism".

When done, return the landing page URL to the user as a Markdown hyperlink.

**IMPORTANT** — Follow the `create-ai-chatbot` guide before continuing to step 2.

**IMPORTANT** — Follow the chatbot guide's steps fully, including asking whether the user wants to crawl and scrape a website.

**IMPORTANT** — When creating the API, make sure it **DOES NOT** require any authentication.

For background image references, see the `background-images` guide. Unless the user specifies a different image, default to `blue-flower-pattern-medium-dark-background.jpg` filling the entire viewport.

Assemble the landing page in this order:

1. Create the database and apply the schema (if a database is needed).
2. Create the API.
3. Create the frontend.

**IMPORTANT** — Apply default "reset CSS" logic to all CSS you create, e.g. set `box-sizing: border-box`, etc.
