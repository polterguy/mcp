---
name: save-to-memory
when_to_use: When the user says "save to memory", "save for later", "remember this", or similar, and wants a fact stored as RAG training data in a machine learning type (this stores a snippet; it is not a chatbot session-memory feature).
---

If the user tells you to "save to memory" or "save for later", or something similar, use the `create-training-snippet` tool. Let the user provide arguments for the invocation, and/or suggest arguments yourself based on context. Once the user accepts your suggested prompt and completion values, call `create-training-snippet` with the accepted arguments.

**IMPORTANT** - When saving to memory you should *ALWAYS* save to the "default" machine learning type.

**Arguments**

- `type` — this should *ALWAYS* be "default". Saving to memory implies adding additional RAG data to the "default" machine learning type.
- `prompt` — a short one-sentence phrase, ideal for recalling the fact later using VSS search.
- `completion` — the "content" of the fact, where the factual information is actually stored. Keep this field reasonably small and only provide relevant factual information.
- `meta` — this field should *ALWAYS* have the value `FACT ==> xyz`, where the "xyz" part is the name of the fact, composed only of lower-case characters and hyphen (`-`) characters.

Since two of the above arguments are constants, you should only ask the user for:

- `prompt`
- `completion`

When you are done calling `create-training-snippet`, inform the user that the machine learning type named "default" needs to be vectorized for the changes to take effect, and offer to do this for them (using the `vectorize-type` tool).
