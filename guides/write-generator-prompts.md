---
name: write-generator-prompts
when_to_use: Asking the Hyperlambda Generator to create or change an endpoint, function, or snippet — i.e. any time you call generate-hyperlambda.
---

# Writing good Hyperlambda Generator prompts

You never hand-write Hyperlambda — `generate-hyperlambda` does. Its output is only as good as
your prompt, and most failures come from vague or overloaded prompts. Treat the prompt as a
**spec with no blanks left**, not a sentence.

## Always specify, concretely
- **Operation** — the exact action, naming the *exact* artifacts: which file path is read,
  which file path is written, which table, which columns. "Load `/etc/data/orders.csv`" — not
  "read the orders".
- **Inputs** — every argument by `name` and `type`, and whether it is **mandatory**.
- **Output shape** — enumerate the fields. "JSON array of objects with fields id, name, email."
  A bare "return X" frequently returns nothing.
- **Constraints** — auth especially. If it must be root-only, say so.

A structured prompt makes under-specification obvious. Example:

```json
{
  "operation": "Executable Hyperlambda file that loads /etc/data/customers.csv and returns rows whose country column equals the country argument",
  "inputs": [{"name": "country", "type": "string", "mandatory": true}],
  "output_shape": "JSON array of objects, each with fields id, name, email, country",
  "constraints": ["requires root role"]
}
```

## Rules that keep it reliable
- **One focused task per call.** Don't combine fetch/scrape + filter + sort + deep projection.
  Generate the records, then transform in a second call or in your own reasoning.
- **Flatten deeply nested data first** (e.g. GeoJSON `features[].properties.x`): return flat
  records first, then project.
- **Ephemeral fetch/compute/return** → `immediate_mode: true`, no filename. Don't
  save-execute-delete.
- **Don't** name internal tools, ask for JSON (it's the default), or put filenames/HTTP verbs
  in the prompt text. For a reusable file with arguments, ask for an "Executable Hyperlambda
  file" and control saving via the `filename` argument.

## Verify the result, never read the code
Check generated endpoints with `invoke-http`, `execute-file`, or the OpenAPI spec — not by
reading or editing the Hyperlambda. **Always re-check auth**: a security constraint can be
silently dropped from code that otherwise compiles and runs fine.
