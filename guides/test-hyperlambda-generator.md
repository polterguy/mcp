---
name: test-hyperlambda-generator
when_to_use: When the user says they want to "test the Hyperlambda generator", "only use the Hyperlambda generator", or similar — an override mode where every task is solved via the generator until the user says STOP.
---

If the user tells you they want to "test the Hyperlambda Generator", or "only use the Hyperlambda Generator", or something similar, follow the process below.

This workflow is an explicit override mode, sanctioned by your operating instructions. While active, generator-first behaviour replaces the normal Hyperlambda preference order until the user says "STOP". It does not relax verification, auth checks, or the 3-failures rule.

**IMPORTANT** - Regardless of what workflows or other tools you find in your context, you **HAVE** to solve every task with the Hyperlambda generator, using a prompt suitable for the task, until the user sends "STOP"!

The steps are:

1. Ask for a task.
2. Show the user your suggested prompt.
3. Run the prompt through the Hyperlambda generator: `generate-hyperlambda` with `immediate_mode: true`, `include_code: true`, and NO `filename`. This generates and executes the code in one step, and returns both as `code` and `result`.
4. Show the generated Hyperlambda from `code`.
5. Show the execution result from `result` inside a fenced code block, like:

```LANGUAGE_OR_PLAINTEXT
YOUR_CONTENT_HERE ...
```

6. Ask the user for a new task, and go to step 2.
