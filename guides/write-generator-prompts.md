---
name: write-generator-prompts
when_to_use: Asking the Hyperlambda Generator to create or change an endpoint, function, or snippet — i.e. any time you call generate-hyperlambda.
---

# Writing good Hyperlambda Generator prompts

> This guide carries examples and recipes. The normative rules — the preference order, the
> prompt/filename contract, and the verification policy — live in your operating instructions;
> where the two ever disagree, the instructions win.

`generate-hyperlambda` writes Hyperlambda for you; hand-writing is a last resort (see the
preference order in your operating instructions). Its output is only as good as
your prompt, and most failures come from vague or overloaded prompts. Treat the prompt as a
**spec with no blanks left**, not a sentence.

## Always specify, concretely
- **Operation** — the exact action, naming the *exact* artifacts: which file path is read,
  which file path is written *by the running code*, which table, which columns. "Load
  `/etc/data/orders.csv`" — not "read the orders". The path where the generated file itself is
  saved is not part of the prompt; pass it as the `filename` argument.
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
- **Don't** name internal tools, ask for JSON (it's the default), put the generated file's own
  save path in the prompt text, or name an HTTP verb there. Data paths the code reads or writes
  *do* belong in the prompt. For a reusable file with arguments, ask for an "Executable
  Hyperlambda file" and control saving via the `filename` argument; the verb comes from the
  `.get.hl` / `.post.hl` extension.

## Verify by running it, not by reading it
Check generated endpoints with `invoke-http` or the OpenAPI spec first — behaviour is the
evidence, not the source. Read the Hyperlambda only when a run fails and the result doesn't
explain why; if you then edit it, re-run `verify-hyperlambda`. **Always re-check auth**: a
security constraint can be silently dropped from code that otherwise compiles and runs fine.
