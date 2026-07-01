---
name: what-hyperlambda-can-do
when_to_use: Deciding whether a task can be done with Hyperlambda, or scoping what the Hyperlambda generator is capable of.
---

# What Hyperlambda can do

If you want to know what Hyperlambda can do, or what to ask the generator for, here are the
core capabilities. Generate any of these with `generate-hyperlambda` — run with
`immediate_mode: true` for a one-off, or pass a `filename` to save it as a reusable tool.

* Create executable Hyperlambda files intended to be persisted and reused, optionally taking arguments. (Save them by passing a `filename` to `generate-hyperlambda`; a saved endpoint is automatically exposed as an MCP tool.)
* Create "immediate code" intended to be executed immediately on the fly (use `immediate_mode: true`, no filename).
* Scrape websites, crawl sitemaps, and return HTML elements.
* Load, move, create, and transform text files.
* Manipulate files and folders on disc (delete, move, create, etc).
* Do mostly anything related to SQL databases, such as SQL Server, PostgreSQL, MySQL, and SQLite.
  - Including CRUD, KPI stuff, aggregates, group by, sorting, filtering, etc.
* Send emails.
* Invoke HTTP endpoints.
  - Using the GET, DELETE, PUT, POST, and PATCH verbs.
* Read configuration settings.
* Import files or results from endpoints into databases.
* Transform and filter records.
* Create tasks and schedule tasks for repeated execution.
* Use RSA and AES cryptography.
* String manipulation.

Hyperlambda is particularly strong with files, HTTP endpoints, and databases.

If you want to create a file that you save, optionally taking arguments, start your generator
prompt with "Executable Hyperlambda file that ..." since this ensures the generator returns an
arguments collection and declaration in its code.

To understand all _"functions"_ that exist in Hyperlambda, use the following generator prompt,
which returns all available functions to you:

```plaintext
Return a list of all keywords I can use
```
