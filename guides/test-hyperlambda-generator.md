---
name: test-hyperlambda-generator
when_to_use: When the user says they want to "test the Hyperlambda generator", "only use the Hyperlambda generator", or similar — an override mode where every task is solved via the generator until the user says STOP.
---

If the user tells you they want to "test the Hyperlambda Generator", or "only use the Hyperlambda Generator", or something similar, follow the process below.

This workflow is an explicit override mode. While active, generator-first behaviour intentionally overrides the normal preference for existing tools/workflows until the user says "STOP".

1. Ask the user for a task.
2. Suggest a prompt for the Hyperlambda generator, display the prompt to the user, then call `generate-hyperlambda` with `immediate_mode: true` (and NO `filename`) to generate AND run the code in one step. Show the generated Hyperlambda and display the raw returned value to the user.
3. Continue from step 1 until the user explicitly tells you to stop using the Hyperlambda generator.

**IMPORTANT** - Regardless of what tasks the user gives you, or what workflows or other tools you find in your context, you **HAVE** to use the Hyperlambda generator with a prompt suitable to solve the task until the user sends "STOP"!

**IMPORTANT** - When you've got a result from the executed Hyperlambda code, return it inside a fenced code block, like:

```LANGUAGE_OR_PLAINTEXT
YOUR_CONTENT_HERE ...
```

Then, when done displaying the result, ask the user for a new task.

**IMPORTANT** - You must first ask for a task, **show** the user the prompt you're about to use, call `generate-hyperlambda` (with `immediate_mode: true`, no filename, which both generates and runs the code), display the resulting Hyperlambda, and then show the execution result. Hence the steps are:

1. Ask for a task
2. Show the user your suggested prompt
3. Run the prompt through the Hyperlambda generator (`generate-hyperlambda`, `immediate_mode: true`, no filename — this generates and executes in one step)
4. Show the resulting Hyperlambda
5. Show the result of the execution as returned by the generator
6. Go to 1
