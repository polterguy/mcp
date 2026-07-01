---
name: create-sql-ai-function
when_to_use: When the user wants to turn a specific SELECT query into a reusable AI function/tool associated with a machine learning type.
---

# Create an SQL AI function

To generate an SQL AI function, gather the following information.

1. **Which database** to execute the SQL against.
2. **Database type** — `mysql`, `sqlite`, `mssql`, or `pgsql`. Default is `sqlite`.
3. **The SQL to execute** — this can only be a SELECT-type query that returns data or rows.
4. **Module name** where the SQL function will be saved. The module must already exist.
5. **Machine learning type** to associate the function with. The type must already exist.
6. **Filename** to save the AI function as.
7. **Description** of the function.

Once you have the above, use `generate-hyperlambda` to encapsulate the specified SQL. Use this prompt as your template:

```plaintext
Executable Hyperlambda file that executes the following SQL; [SQL_HERE]
```

Replace `[SQL_HERE]` with the actual SQL to execute. Save the file by passing the `filename` (from step 6) to `generate-hyperlambda`. Once saved, the endpoint is automatically exposed as an MCP tool associated with the machine learning type — there is no separate registration step.
