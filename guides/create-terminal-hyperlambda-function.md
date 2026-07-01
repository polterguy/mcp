---
name: create-terminal-hyperlambda-function
when_to_use: When the user wants to wrap a terminal/shell command inside a reusable Hyperlambda endpoint or run a command from the backend and return its result.
---

# Create a terminal Hyperlambda function

## Purpose

Wrap a terminal command inside an executable Hyperlambda file so it can be run from the backend and, optionally, return its result to the caller.

## Steps

1. Ask the user what the terminal command should actually do.
2. Show a terminal command that accomplishes the request, and confirm it with the user.
3. Generate the Hyperlambda that executes the command (and optionally returns the result) with the `generate-hyperlambda` tool. See the example prompt below.
   - Offer the user the option to secure the endpoint so only specific role(s) can execute it.
4. To generate and run the code once without creating a tool, call `generate-hyperlambda` with `immediate_mode: true` and no filename. To save it as a reusable, automatically-exposed MCP tool, pass a `filename`. Once saved, the endpoint becomes an MCP tool automatically — there is no separate registration step.
5. Ask the user which module to save the Hyperlambda file in. Offer to create a new module with the `create-module` tool if needed.

Example generator prompt:

```plaintext
Executable Hyperlambda file that executes the following terminal command `ls -l blah, blah, blah` and returns the result to the caller.
```

Optionally add a list of arguments or authorization requirements to the prompt.

## Code generation rules (environment safety)

When generating code:

1. Assume NON-interactive, programmatic execution by default.
   - Do NOT use shell operators like `&&`, `|`, `>`, `*`, or globbing.
   - Do NOT assume a terminal or interactive shell unless explicitly requested.

2. Do NOT assume a specific operating system.
   - If OS-specific behavior is required, use the `get-operating-system` tool to determine the operating system at runtime.

3. Prefer native language/runtime APIs over shell commands.
   - Only use external process execution if no native API exists.
   - Never rely on tools that may not exist on all systems.

4. When executing external processes:
   - Specify executable and arguments separately.
   - Do not rely on shell parsing unless explicitly required.
   - Include proper error handling.

5. Before returning code, internally verify:
   - No shell-only syntax is used unintentionally.
   - No hidden OS assumptions exist.
   - The solution would run in a backend/server context.

If any rule is violated, regenerate the solution.
