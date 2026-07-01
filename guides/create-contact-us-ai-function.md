---
name: create-contact-us-ai-function
when_to_use: Adding a "contact us" / escalate-to-human tool to an AI chatbot or agent that emails a conversation summary to a support or lead-gen address.
---

# Create a contact-us AI function

A contact-us AI function lets an AI chatbot or agent escalate a conversation to human support by
emailing a summary to an address (customer service or lead generation). To build one you need:

1. The email address to send to.
2. The module name.
3. The machine-learning type to associate the function with.

## Steps

1. Check whether the module exists; if not, create it with `create-module`.
2. Generate the Hyperlambda with `generate-hyperlambda`, using the prompt below.
3. Save it as `contact-us.hl` inside the chosen module (pass `filename` to
   `generate-hyperlambda`). Once saved, the file is AUTOMATICALLY exposed as an MCP tool and
   becomes available to the machine-learning type — there is no separate registration step.
   Make sure the type exists first.
4. Remind the user the machine-learning type is NOT vectorized, and must be vectorized before
   the RAG data takes effect.

### Generator prompt

```markdown
Executable Hyperlambda file that sends an email. It takes the following arguments.
- name
- email
- subject
- body

The function will send an email to '[EMAIL_AS_SPECIFIED_BY_USER]' using the [name] and [email] arguments as [reply-to].
```

`[EMAIL_AS_SPECIFIED_BY_USER]` is the address from step 1.

### File-level comment to use

```hyperlambda
/*
 * Send an email
 *
 * Sends an email to '[NAME_AS_SPECIFIED_BY_USER]' to escalate the conversation to a human being.
 *
 * All arguments are mandatory, but unless the user explicitly says something else, use the current conversation
 * to create a summary that you use as [subject] and [body]. The [name] and [email] arguments is the name and email
 * of the user. The email will be sent to '[EMAIL_AS_SPECIFIED_BY_USER]'.
 */
```

You only need the email address to send to — not the recipient's name.
