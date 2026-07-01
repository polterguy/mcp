---
name: create-send-email-hyperlambda
when_to_use: When the user wants to create an HTTP endpoint or executable Hyperlambda file that sends emails.
---

# Create a send-email Hyperlambda function

Use this when the user wants an HTTP endpoint or executable Hyperlambda file that sends emails.

By default, Magic Cloud uses SendGrid as its SMTP service. SendGrid does **not** allow an arbitrary `from` email address, but it does allow overriding the `reply-to` address. So when you generate Hyperlambda meant to send an email, use a prompt resembling the following with `generate-hyperlambda`:

```plaintext
Executable Hyperlambda file that sends an email. The endpoint takes the following arguments;
- name
- email
- subject
- body

When invoked, send an email to 'recipient@somewhere.com', and use the `name` and `email` arguments as your [reply-to] values.
```

Save the file by passing a `filename` to `generate-hyperlambda`; once saved it is automatically exposed as an MCP tool. To generate and send once without saving, call `generate-hyperlambda` with `immediate_mode: true` and no filename.
