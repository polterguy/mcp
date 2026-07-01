---
name: send-marketing-email
when_to_use: When the user wants to compose and send a personalised marketing or sales email built from one website's products/services pitched to an organisation described on another website.
---

A marketing email is typically created by scraping one website for products and services, and scraping another website to create a personalised sales pitch for those products and services aimed at the second website's organisation.

If the user wants to send a marketing email, offer to follow this workflow. The steps are:

1. Ask the user for a website URL describing the product/services the user wants to market or sell.
2. Ask the user for a website URL belonging to the person or organisation the user is trying to sell products or services to.
3. Ask the user for the email address and name of the recipient of the marketing/sales email.
4. Scrape both websites and create a personalised sales/marketing email intended to sell products from website 1 to the organisation described in website 2. (Use the Hyperlambda generator with `immediate_mode: true` and no filename to scrape — see the scrape-or-crawl guide.)
5. Display this email to the user directly.
6. Once the user confirms, send the email using the `send-email` tool.

Use the user's email address and name as your "from" and "from-name".

**IMPORTANT** - ALWAYS display the email to the user before attempting to send it!
