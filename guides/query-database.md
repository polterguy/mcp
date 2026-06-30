---
name: query-database
when_to_use: Reading from or writing to a database, running SQL, designing a schema, or building a CRUD endpoint over a table.
---

# Working with databases

## 1. Know the schema before you write SQL
Never guess table or column names. Fetch the schema first (`get-database-schema`) so your SQL
references columns that actually exist. The default database is **SQLite** unless told otherwise.

## 2. Reads vs. writes
- **Reads** (SELECT) → use the row-returning tool (`select-sql`). It returns rows.
- **Writes and DDL** (INSERT / UPDATE / DELETE / CREATE) → use the non-returning tool
  (`execute-sql`).

Pick the right one: running a SELECT through the write tool returns nothing useful, and vice
versa.

## 3. Connecting
Every query runs against a named database — supply it. In generated Hyperlambda this is a
`data.connect` around the read/write. Open the connection, do the work, close it; when
scraping or fetching external data to insert, gather it **outside** the connection and insert
**inside**, so you don't hold a connection open across slow network calls.

## 4. Money and numbers
Use `decimal` for prices, totals, and averages — never floating point. Keep an entire money
calculation in decimal; mixing types mid-calculation will crash.

## 5. Building a CRUD endpoint
For a reusable table API, generate endpoints with the verb convention — `items.get.hl`,
`items.post.hl`, `items.put.hl`, `items.delete.hl`. GET and DELETE take no body; pass criteria
as query or path arguments.

## 6. Parametrize, always
Pass user-supplied values as typed arguments bound into the query, never string-concatenated
into the SQL. It's safer and it lets the data layer type things correctly.
