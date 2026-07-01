---
name: transform-files-using-the-hyperlambda-generator
when_to_use: Transforming, parsing, filtering, or converting CSV/XML/YAML/JSON/Hyperlambda files, or using files as a lightweight database.
---

# Transform files using the Hyperlambda generator

Magic has strong support for transforming CSV, XML, YAML, JSON, and Hyperlambda files back and
forth, and for parsing text. Use `generate-hyperlambda` for these tasks — run with
`immediate_mode: true` for a one-off transform, or pass a `filename` to save reusable logic as
an MCP tool.

You can even query the generator for its capabilities with prompts such as:

* "Return all slots in the 'data.' namespace"
* "Return all slots in the 'strings.' namespace"

You can filter records loaded from a file, treating files almost like a database:

```plaintext
Load '/etc/customers.json', transform it to lambda, and return all rows having a 'status' value of 'xyz'
```

The same works for YAML, Hyperlambda, and CSV files. You can also convert between formats and
save the result:

```plaintext
Load '/etc/customers.json', transform it to YAML, and save it as '/etc/backup/foo.yaml'
```
