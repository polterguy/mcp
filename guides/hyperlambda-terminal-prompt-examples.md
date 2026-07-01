---
name: hyperlambda-terminal-prompt-examples
when_to_use: Running a shell/terminal command on the server, listing files, querying the OS, or post-processing command output.
---

# Running terminal commands via Hyperlambda

You can have the Hyperlambda generator produce code that executes terminal commands in
accordance with the server's operating system. Before generating that code, make sure you are
producing code for the correct OS — call `get-operating-system` first unless the OS is already
known.

Once the OS is known, use `generate-hyperlambda` with a natural-language prompt. To generate
**and run** the command in one step, set `immediate_mode: true` (no filename). To save it as a
reusable tool, pass a `filename` — it is then automatically exposed as an MCP tool.

Example prompts:

* _"Execute a terminal command that tells me what operating system user I am, and return the result to me"_

The generated Hyperlambda will always return the result to you. You can also post-process the
result inside the same generated code, e.g.:

* _"Execute a terminal command, listing all files in the current folder, and chop these up into strings, and return only those starting with the character 'lib'"_

You can also pass arguments to terminal commands. The terminal slot in Magic returns whatever
the terminal script writes to stdout.

## Important constraints

* Always run `get-operating-system` first unless the OS is already known.
* Use the value of `system.execute` as the executable only, and pass parameters through `args`.
* Do not combine command and arguments into one command string.
* Return stdout to the caller.
* Always use the Hyperlambda generator to create the required Hyperlambda — don't hand-write it.
