---
name: create-crud-api
when_to_use: Generating a CRUD API (create/read/update/delete HTTP endpoints) over a database table, using the crudify CRUD generator.
---

# Create a CRUD API

This workflow generates CRUD HTTP endpoints over a database table with the `crudify` tool — the
same battle tested CRUD generator the dashboard is using. Steps:

1. Ask which database and table to use, then call `get-database-schema` to retrieve the schema —
   use it to draw a simple Mermaid chart and suggest columns to the user.
2. Ask for a module name (or whether to reuse the database name as the module name).
3. Ask which CRUD verb(s) to generate.
4. Ask about authorization requirements, and pass the roles as `auth` (comma separated), or `*`
   for any authenticated user. **Omitting `auth` creates endpoints anyone can invoke** — only do
   that when the user explicitly wants a public API, e.g. for a publicly available AI chatbot or
   full-stack app.
5. Check whether the module exists. If not, create it with `create-module`. If it already
   exists, pause and confirm the user really wants to use it.
6. Invoke `crudify` once per selected verb. Generated endpoints are instantly available as HTTP
   endpoints and MCP tools — no separate registration step — and the GET verb also generates a
   record count endpoint. GET endpoints support paging, sorting and filtering out of the box.
   Pass `overwrite` as true to regenerate endpoints that already exist.

## crudify arguments

- `database` is on the format `[connection-string|database]`, e.g. `[generic|chinook]`.
- `table` — for PostgreSQL tables outside the public schema use `schema.table`.
- `moduleUrl` is the relative URL of the endpoint, typically the table name.
- For `post` also pass `returnId` as true to return the id of the created record.
- `args` holds the column declarations, and differs per verb:

| Verb   | `columns`                                     | `primary`                                    |
|--------|-----------------------------------------------|----------------------------------------------|
| get    | all columns, as `{name, type}` objects        | —                                            |
| post   | insertable columns, as `{name, type}` objects | —                                            |
| put    | updatable non-key columns, as `{name, type}`  | key columns, as `{name, type}` objects       |
| delete | —                                             | key columns, as `{"column": "type"}` objects |

- Exclude auto increment, identity and defaulted columns from `post` and `put` columns.
- Composite keys are simply multiple entries in `primary`.
- `type` is the Hyperlambda type of the column. Map SQL types as follows:

| SQL type                                       | Hyperlambda type |
|------------------------------------------------|------------------|
| int, integer, bigint, smallint, tinyint        | long             |
| varchar, nvarchar, char, text, uuid            | string           |
| decimal, numeric, money                        | decimal          |
| real, float, double                            | double           |
| date, datetime, datetime2, timestamp           | date             |
| bit, boolean, tinyint(1)                       | bool             |

Example — delete endpoint for a `track_tags` table with a composite key:

```json
{
  "databaseType": "sqlite",
  "database": "[generic|chinook]",
  "table": "track_tags",
  "moduleName": "music",
  "moduleUrl": "track_tags",
  "verb": "delete",
  "auth": "root",
  "args": { "primary": [ { "track_id": "long" }, { "tag_id": "long" } ] }
}
```

## Custom endpoints

When the standard CRUD shape doesn't fit — aggregates, custom joins, business logic inside the
endpoint, or non-standard return shapes — generate the endpoint with `generate-hyperlambda`
instead, using the template prompts below.

- Save each generated file by passing `filename`. Default filename = table name + verb; for a
  `contacts` table: `contacts.get.hl` for read, `contacts.post.hl` for create, `contacts.put.hl`
  for update, `contacts.delete.hl` for delete. Let the user change this.
- The generator doesn't distinguish an HTTP endpoint from a plain executable file — the verb
  comes from the filename. Always prefix prompts with "Executable Hyperlambda file ...".
- Keep prompts SIMPLE — don't overload them.

**Filtering** — describe the filter type, e.g. "equality filtering on column xyz" or "pattern
matching filter on column xyz" (less-than, less-than-or-equal, equal, greater-than, contains,
etc.). Don't add too many filters per prompt.

**Arguments** — always state whether each argument is optional or mandatory.

### Create verb

```plaintext
Executable Hyperlambda file creating a single item in [DATABASE] database and its [TABLE] table.
The endpoint can only be invoked by 'root' users and returns the ID of the newly created record.

The endpoint accepts the following arguments.
- [ARG1] of type [TYPE_HERE1]
- [ARG2] of type [TYPE_HERE2]

Return the primary key of the new item as `result`.
```

Replace `[ARG1]`/`[ARG2]` with column names from the table.

### Read verb

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

### Update verb

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

### Delete verb

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
