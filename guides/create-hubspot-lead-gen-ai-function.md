---
name: create-hubspot-lead-gen-ai-function
when_to_use: When the user wants an AI function that lets a chatbot or agent save a new lead/contact into HubSpot.
---

# Create a HubSpot lead-generation AI function

This AI function lets an AI chatbot or agent create a new contact (a saved lead) in HubSpot. To build it, gather the following.

1. **Machine learning type** to associate the function with. Offer to list all types (`list-types`) so the user can pick one.
2. **Module name** where the AI function will be saved. Offer to use the same name as the machine learning type.
3. **Fields to save.** By default `first_name`, `last_name`, and `email`, but the user can add any field supported by HubSpot's `https://api.hubapi.com/crm/v3/objects/contacts` POST API.

Once the user has provided the above, check whether the module exists and create it if not (`create-module`). Then generate the endpoint by passing this prompt to `generate-hyperlambda`:

```markdown
Executable Hyperlambda file that invokes HubSpot API to insert a new contact. Accepts the following arguments;

- first_name
- last_name
- email
```

Swap the arguments above to match the fields the user supplied. Save the file as `create-hubspot-contact.hl` inside the chosen module by passing that `filename` to `generate-hyperlambda`.

Use the following file-level comment in the generated Hyperlambda:

```hyperlambda
/*
 * Creates a new contact in HubSpot
 *
 * If the user provides you with his or her email address and name, you should save the user as a new lead in HubSpot by using this function.
 */
```

Once the file is saved with a `filename`, it is **automatically exposed as an MCP tool** — there is no separate registration step. Make sure the machine learning type from step 1 exists before associating the function with it.

When done, inform the user that the machine learning type is **not vectorized**, and that they must vectorize it (`vectorize-type`) before the RAG data takes effect.
