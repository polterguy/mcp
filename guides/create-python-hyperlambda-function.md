---
name: create-python-hyperlambda-function
when_to_use: When the user wants to wrap a Python script inside an executable Hyperlambda endpoint so it can be run as a tool.
---

# Wrap a Python script in a Hyperlambda function

This lets the user wrap Python scripts inside an executable Hyperlambda file.

## Steps

1. Ask the user what the Python code should actually do.
2. Create Python code and show it to the user.
   - Return only Python 3 code.
   - Require a `main()` function and an `if __name__ == "__main__": main()` guard.
   - Require all inputs to be read from `sys.argv` and validated.
   - Require JSON output to stdout only.
3. Ask the user whether the Python code has side effects (filesystem writes, network calls, DB updates, emails, etc.).
   - If yes, ask explicitly whether the code should be executed for validation.
4. Validate the Python code by running it with `execute-python` using minimal test arguments.
   - Only execute if the user accepts validation when side effects exist.
   - If execution fails or returns stderr / a non-zero exit code, fix the code and retry.
5. Ask the user to accept the validated code.
6. Save the Python code inside `/etc/python/` with a relevant filename using `create-file`. Use `create-folder` first to ensure the folder exists.
7. Use `generate-hyperlambda` to generate Hyperlambda that executes the Python script and optionally returns the result to the caller (example prompt below). Offer to secure the endpoint so only specific role(s) can execute it.
8. Once the user is satisfied, offer to save the generated Hyperlambda in a module.

```plaintext
Executable Hyperlambda file that executes the Python file found at `/etc/python/WHATEVER_FILE_NAME_HERE.py` and returns the result to caller.
- First argument does xyz
- Second argument does qwe
```

Optionally add a list of arguments as illustrated above.

Show the prompt to the user, then immediately generate the Hyperlambda and show the result. Ask whether you should save the file in a module — you'll need to ask which module the user wants. Offer to create a new module with `create-module`. Save by passing a `filename` to `generate-hyperlambda`; once saved it is automatically exposed as an MCP tool.

If the Python code returns JSON through stdout, say so explicitly in your generator prompt, e.g. "The Python code returns JSON. Make sure you return this as structured output to the caller."

**IMPORTANT** — The Python code cannot rely on importing packages. It must be standard Python code.
