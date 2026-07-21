---
name: create-full-stack-app
when_to_use: When the user wants to build a full-stack app — a frontend (SPA) backed by an API over a database or external data source, optionally with login.
---

# Create a full-stack app

A full-stack app is a frontend SPA plus the API it talks to. You always have to create the API to connect the frontend to the backend.

Read the `web-file-rules` guide before writing any frontend file — it governs asset paths, canonical URLs, visibility toggling, and the mandatory render verification.

## Steps

1. Ask the user which data source to use — a database or an external API.
2. Ask what design the user wants. List a few popular styles (modern, futuristic, classic, etc.) and let the user choose.
3. Retrieve the schema or data structure so you understand the available metrics. For a database use `get-database-schema`.
4. Combine HTML, CSS and JS into a responsive full-stack layout, using **separate files** for JS and CSS.
5. Save the frontend files inside `/etc/www/UNIQUE_EXPLAINABLE_NAME/` as a SPA. Choose a relevant folder name for the app.

## Creating the API

For standard CRUD endpoints over database tables, invoke `crudify` once per verb per table — see the `create-crud-api` guide for the argument shapes. For app-specific endpoints — KPIs, aggregates, custom joins, business logic — use `generate-hyperlambda`, following these rules.

1. Do not create one endpoint that returns all data. Create several smaller, simpler HTTP endpoints, each returning one or two concepts at most.
2. For read endpoints specify optional filtering on relevant columns. Don't add too many filters, and keep prompts simple.

For endpoint prompt templates, consult the `example-hyperlambda-generator-prompts` guide.

Save each generated endpoint by passing a `filename` to `generate-hyperlambda`. Once saved, an endpoint is automatically exposed as an MCP tool. When the API is done, offer to test it by invoking the GET endpoint with `invoke-http`.

## Important implementation details

1. If the user wants a GUI restricted to authorized users, make the primary GUI **invisible** before login, and add a login button/form, plus a logout button shown after login. Store the token in `localStorage`. Auth flow:
   1. Hide main app, show login UI
   2. User logs in
   3. Hide login UI, show app UI
   4. User logs out (delete token from `localStorage`)
   5. Hide app UI, show login UI

For authentication, suggest either:

1. Magic Auth (uses platform endpoints so existing users can log in)
2. Google SSO/OIDC (any Google account can log in)

**IMPORTANT** — If the user wants OIDC, restrict endpoints to 'guest' users unless the user explicitly overrides this: pass `auth` as 'guest' to `crudify`, and make your `generate-hyperlambda` prompts explicitly say something like "Only 'guest' users can access the endpoint".
