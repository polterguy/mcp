---
name: create-crud-api
when_to_use: Generating a CRUD API (create/read/update/delete HTTP endpoints) over a database table.
---

# Create a CRUD API

This workflow generates CRUD HTTP endpoints over a database table. Steps:

1. Ask which database and table to use, then call `get-database-schema` to retrieve the schema —
   use it to draw a simple Mermaid chart and suggest columns to the user.
2. Ask for a module name (or whether to reuse the database name as the module name).
3. Ask which CRUD verb(s) to generate. Let the user override the templates below.
4. Ask about authorization requirements. For a publicly available AI chatbot or full-stack app,
   use "no authorization" in your prompt.
5. Check whether the module exists. If not, create it with `create-module`. If it already
   exists, pause and confirm the user really wants to use it.
6. Run through each selected CRUD prompt below, one at a time, generating each endpoint with
   `generate-hyperlambda` and saving each (pass `filename`) before moving to the next verb. A
   saved endpoint is automatically exposed as an MCP tool.
   - Default filename = table name + verb. For a `contacts` table: `contacts.get.hl` for read,
     `contacts.post.hl` for create, `contacts.put.hl` for update, `contacts.delete.hl` for
     delete. Let the user change this.
   - The generator doesn't distinguish an HTTP endpoint from a plain executable file — the verb
     comes from the filename. Always prefix prompts with "Executable Hyperlambda file ...".
7. For read endpoints, specify optional sorting, optional paging, and (if relevant) filtering on
   appropriate fields. Keep prompts SIMPLE — don't overload them.

**Filtering** — describe the filter type, e.g. "equality filtering on column xyz" or "pattern
matching filter on column xyz" (less-than, less-than-or-equal, equal, greater-than, contains,
etc.). Don't add too many filters per prompt.

**Arguments** — always state whether each argument is optional or mandatory.

## Create verb

```plaintext
Executable Hyperlambda file creating a single item in [DATABASE] database and its [TABLE] table.
The endpoint can only be invoked by 'root' users and returns the ID of the newly created record.

The endpoint accepts the following arguments.
- [ARG1] of type [TYPE_HERE1]
- [ARG2] of type [TYPE_HERE2]

Return the primary key of the new item as `result`.
```

Replace `[ARG1]`/`[ARG2]` with column names from the table.

## Read verb

```plaintext
Executable Hyperlambda file returning items from [DATABASE] database and its [TABLE] table.
The endpoint can be optionally paged and sorted, only be invoked by 'root' users, and returns a list of rows from the database.

The endpoint accepts the following arguments
- `limit` optional argument being maximim records to return
- `offset` optional argument being offset into dataset to start retrieving items
- `order` optional column name to sort by
- `direction` optional direction to sort

Return the following columns
- COLUMN_A
- COLUMN_B
- COLUMN_C
```

For read endpoints also specify optional filtering on relevant columns, e.g. "Add pattern
matching filtering on the 'CompanyName' column." Keep it simple.

## Update verb

```plaintext
Executable Hyperlambda file updating a single item in [DATABASE] database and its [TABLE] table.
The endpoint can only be invoked by 'root' users and returns how many rows were affected.

The endpoint accepts the following arguments.
- [PRIMARY_KEY_FOR_TABLE] of type [TYPE_HERE1]
- [ARG1] of type [TYPE_HERE1]
- [ARG2] of type [TYPE_HERE1]

Return how many rows was affected as `result`.
```

Replace `[ARG1]`/`[ARG2]` with column names; `[PRIMARY_KEY_FOR_TABLE]` is the table's primary key.

## Delete verb

```plaintext
Executable Hyperlambda file deleting a single item from [DATABASE] database and its [TABLE] table.
The endpoint can only be invoked by 'root' users.

The endpoint accepts the following arguments.
- [PRIMARY_KEY_FOR_TABLE] of type [TYPE_HERE1]

Return how many rows was affected as `result`.
```

`[PRIMARY_KEY_FOR_TABLE]` is the table's primary key.

## After generating

When done with all selected verbs, offer to test the GET endpoint with `invoke-http`.
