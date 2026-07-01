---
name: create-workflow
when_to_use: When the user wants to define and save a reusable natural-language workflow (a guided multi-step process) into the machine learning data for later execution.
---

# Create a workflow

In Magic Cloud, a workflow is a pre-defined, high-level guided process that helps the user accomplish a complex task by orchestrating several individual steps into a logical sequence. Workflows are natural-language (plain English) instructions designed to simplify and automate multi-step operations. They are intended to be executed later by an LLM.

To create a workflow, the user describes each step, and you save it as a workflow in the `default` machine learning type with the `create-training-snippet` tool. Creating a workflow means writing natural-language instructions and saving them so the user can later execute the workflow.

**IMPORTANT** — The `meta` argument to `create-training-snippet` must be exactly:

```
WORKFLOW ==> [WORKFLOW_NAME_HERE]
```

Where `[WORKFLOW_NAME_HERE]` is substituted with a descriptive name containing only lower-case characters and hyphens (`-`).

**IMPORTANT** — Do **NOT** use `generate-hyperlambda` to create the workflow. Instead, write an English instruction that encapsulates the steps the user described.

## Details

- After creating and saving the workflow, offer the user the option to vectorize the type (with the `vectorize-type` tool) so the workflow becomes an active part of the RAG data.
- Before saving, show the user the prompt and completion you intend to save, and let them make modifications.
- The prompt should be only ~5 to 10 words: a high-level description starting with the word "Workflow", with keywords that will trigger the vector search later.
- The completion is the entire workflow — its sequence of steps for the AI to perform — plus a description of what the workflow does.
- If the workflow needs to generate SQL to execute correctly, add a step that first retrieves the database schema (with the `get-database-schema` tool) for the database the workflow connects to.
