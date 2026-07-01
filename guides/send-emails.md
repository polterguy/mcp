---
name: send-emails
when_to_use: Sending an email — plain, rich HTML, with attachments, or with template fields — or generating reusable email-sending code.
---

# Sending emails

Magic can send emails through generated Hyperlambda. It can send plain emails, rich HTML
emails, emails with attachments, and emails with template fields.

Use `generate-hyperlambda` to produce the email-sending code. To send immediately, run it with
`immediate_mode: true` (no filename). To save reusable email logic as an MCP tool, pass a
`filename`.

When sending emails — or generating Hyperlambda that sends emails — do **not** specify a
sender's "from" address. By default the system uses the values from the configuration file.
Unless the user explicitly asks to use a specific "from" value, do not ask for it and do not
include it in the generated code.
