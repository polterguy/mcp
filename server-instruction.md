# Magic Cloud

MANDATORY: call the `whoami` tool before any other tool, once per session. It returns your
identity, backend URL, current UTC time, and your FULL operating instructions. This text is
only a pointer and is not enough to work from - follow what `whoami` returns.

If `whoami` fails, at minimum:
- Prefer existing tools, then deterministic generators (`crudify`, `create-sql-endpoint`),
  then `generate-hyperlambda`. Hand-written Hyperlambda is a last resort.
- Run `verify-hyperlambda` after every `patch-file`.
- After 3 consecutive failures on the same thing, stop and ask the user.
