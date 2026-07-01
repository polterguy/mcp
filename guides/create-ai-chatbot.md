---
name: create-ai-chatbot
when_to_use: Creating an AI chatbot — i.e. a machine-learning type trained on a website's content via crawling, optionally with a send-email/lead-gen tool.
---

# Create an AI chatbot

An AI chatbot is a machine-learning type in the backend, trained on RAG data (typically scraped
from a website). Before you begin, gather the following from the user:

1. A name for the chatbot. This becomes the name of the machine-learning type. It may contain
   only lower-case characters, numbers, `-`, and `_`.
   - Do NOT apply any auth requirements when creating this type.
2. Offer to crawl a website to generate training data.
   - Ask for the URL to crawl, and how many pages to import.
   - If a URL is provided, scrape the landing page and use its content to write a high-quality
     system instruction for the type (for a sales/customer-service chatbot).
   - Use `crawl-website` to crawl and scrape pages.
3. Suggest creating a send-email AI function that emails the client's lead-generation address.
   - Ask which email address and name to send to. Substitute these into the template below;
     offer the user's own name/email as defaults.
4. Inform the user that the type must be vectorized before the AI function/RAG data takes effect,
   and offer to vectorize it (`vectorize-type`).

Once you have the above, execute these steps in order:

1. Scrape the base URL (`invoke-http` GET, or generate a scrape tool) and use its content to
   write a high-quality system instruction for selling services and providing customer service.
2. Create the machine-learning type with that system instruction using `create-type`.
3. Start crawling/scraping for RAG data using `crawl-website`.

## Send-email function

If the user wants a send-email function:

1. Generate the Hyperlambda with `generate-hyperlambda`.
2. Check whether the target module exists; if not, create it with `create-module`. Use the same
   module name as the type.
3. Save the file by passing a `filename` to `generate-hyperlambda` (e.g. inside that module).
   Once saved, the endpoint is AUTOMATICALLY exposed as an MCP tool — there is no separate
   registration step.

### Example generator prompt for a send-email function

```markdown
Executable Hyperlambda file that sends an email. It takes the following arguments.
- name
- email
- subject
- body

The function will send an email to '[NAME_AS_SPECIFIED_BY_USER]' / '[EMAIL_AS_SPECIFIED_BY_USER]'
using the [name] and [email] arguments, and add the [name] and [email] arguments as [reply-to].
```

## After finishing

- Remind the user the machine-learning type is NOT vectorized, and must be vectorized before the
  RAG data takes effect.
- Then offer to create an embed script so the chatbot can be embedded on a website — see the
  `create-embed-script` guide.
