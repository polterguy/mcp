---
name: create-full-stack-app
when_to_use: When the user wants to build a full-stack app — a frontend (SPA) backed by an API over a database or external data source, optionally with login.
---

# Create a full-stack app

A full-stack app is a frontend SPA plus the API it talks to. You always have to create the API to connect the frontend to the backend.

## Steps

1. Ask the user which data source to use — a database or an external API.
2. Ask what design the user wants. List a few popular styles (modern, futuristic, classic, etc.) and let the user choose.
3. Retrieve the schema or data structure so you understand the available metrics. For a database use `get-database-schema`.
4. Combine HTML, CSS and JS into a responsive full-stack layout, using **separate files** for JS and CSS.
5. Save the frontend files inside `/etc/www/UNIQUE_EXPLAINABLE_NAME/` as a SPA. Choose a relevant folder name for the app.

## Rules for the Hyperlambda Generator

When you use `generate-hyperlambda` to create CRUD or other endpoints, follow these rules.

1. Do not create one endpoint that returns all data. Create several smaller, simpler HTTP endpoints, each returning one or two concepts at most.

For other endpoint types, consult the `example-hyperlambda-generator-prompts` guide for prompt templates.

Example CRUD generator prompts — pass each as the prompt to `generate-hyperlambda`:

**Create**

```plaintext
Executable Hyperlambda file creating a single item in [DATABASE] database and its [TABLE] table.
The endpoint can only be invoked by 'root' users and returns the ID of the newly created record.

The endpoint accepts the following arguments.
- [ARG1]
- [ARG2]
```

Replace `[ARG1]`/`[ARG2]` with column names from the table.

**Read**

```plaintext
Executable Hyperlambda file returning items from [DATABASE] database and its [TABLE] table.
The endpoint can be optionally paged and sorted, only be invoked by 'root' users, and returns a list of rows from the database.

The endpoint accepts the following arguments
- `limit` optional argument being maximum records to return
- `offset` optional argument being offset into dataset to start retrieving items
- `order` optional column name to sort by
- `direction` optional direction to sort
```

**IMPORTANT** — For read endpoints also specify optional filtering on relevant columns. Don't add too many filters, and keep prompts simple.

**Update**

```plaintext
Executable Hyperlambda file updating a single item in [DATABASE] database and its [TABLE] table.
The endpoint can only be invoked by 'root' users and returns how many rows were affected.

The endpoint accepts the following arguments.
- [PRIMARY_KEY_FOR_TABLE]
- [ARG1]
- [ARG2]
```

`[PRIMARY_KEY_FOR_TABLE]` is the primary key of the table being updated.

**Delete**

```plaintext
Executable Hyperlambda file deleting a single item from [DATABASE] database and its [TABLE] table.
The endpoint can only be invoked by 'root' users.

The endpoint accepts the following arguments.
- [PRIMARY_KEY_FOR_TABLE]
```

Save each endpoint by passing a `filename` to `generate-hyperlambda`. Once saved, an endpoint is automatically exposed as an MCP tool. When all verbs are done, offer to test the API by invoking the GET endpoint with `invoke-http`.

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

**IMPORTANT** — If the user wants OIDC, your `generate-hyperlambda` prompts must explicitly say something like "Only 'guest' users can access the endpoint", so only guest users are authorized — unless the user explicitly overrides this.
