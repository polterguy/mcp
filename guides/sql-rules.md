---
name: sql-rules
when_to_use: Writing SQL, querying a database, or running an INSERT/UPDATE/DELETE/DDL or SELECT statement.
---

# SQL rules

## Rules

1. Before writing SQL, know the schema of the target database.
2. If the schema is unknown, use the `get-database-schema` tool.
3. Unless the user explicitly asks for another database, assume SQLite.
4. Use `execute-sql` for updates, deletes, inserts, schema changes, and other SQL that does not need to return rows.
5. Use `select-sql` for SQL that returns rows.
6. Construct SQL using the actual table and column names from the schema, not guesses.
