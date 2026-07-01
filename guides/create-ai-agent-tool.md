---
name: create-ai-agent-tool
when_to_use: When the user wants to create a reusable AI agent tool/endpoint that does database CRUD, calls 3rd-party HTTP APIs, reads files, or any other backend task.
---

# Create AI agent tools

Using the `generate-hyperlambda` tool you can generate AI agent tools. These tools can do CRUD against databases, integrate with 3rd-party HTTP web APIs, read files, and anything else Hyperlambda can do.

To create a tool, start your prompt with "Executable Hyperlambda file that ..." so the generator correctly returns an `.arguments` declaration describing the tool's inputs.

Save the generated code by passing a `filename` to `generate-hyperlambda`. Once an endpoint is saved this way it is AUTOMATICALLY exposed as an MCP tool — there is no separate registration step. To generate and run code once without saving it (no tool created), call `generate-hyperlambda` with `immediate_mode: true` and no filename.

For concrete prompt examples covering databases, web scraping, files, cryptography, HTTP, email, and more, consult the `example-hyperlambda-generator-prompts` guide.
