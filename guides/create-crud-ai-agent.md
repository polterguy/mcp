---
name: create-crud-ai-agent
when_to_use: Giving an AI agent (machine-learning type) database tools — creating a database from a schema and generating CRUD AI functions over its tables.
---

# Create a CRUD AI agent

An AI agent in Magic Cloud is a machine-learning type with access to tools, giving the AI an
"opposable thumb". This workflow creates a database and generates CRUD AI functions over it,
wired to a machine-learning type. Gather from the user:

1. The name of an existing machine-learning type.
   - Offer to list types (`list-types`), or to create a new one (`create-type`).
2. A schema for a database.
   - Suggest examples, and let the user specify what they want to store; suggest a schema if
     they don't want to define one manually.
   - Optionally ask whether to use an existing database; if so, list databases
     (`list-databases`) and offer one. Magic supports SQLite, SQL Server, MySQL, and
     PostgreSQL when configured.

## Steps (once the schema is agreed)

1. Verify the machine-learning type exists (unless you already know it does).
2. Create a new database and apply the schema (`create-database` / `execute-sql`).
   - You may apply the whole schema at once.
   - Offer to insert example records via `execute-sql` after applying the schema.
   - If using an existing database, retrieve its schema (`get-database-schema`) for the next
     step.
3. Generate CRUD AI functions for every table in the database. Use the `create-crud-api` guide's
   template prompts. Save each generated file with a `filename`; a saved endpoint is
   automatically exposed as an MCP tool and available to the type — no separate registration
   step.

When done, offer to generate example records and insert them. Display database schemas as
Mermaid charts using simple syntax.
