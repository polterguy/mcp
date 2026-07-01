---
name: onboard-user
when_to_use: When the user wants to onboard/create a new Magic Cloud user - create the account, assign a role, and send a welcome email with login details.
---

# Onboard a user

Onboarding a user typically means creating a new user with a secure password, associating the user with the root role, and sending the user a welcome email.

You need the following:

1. Name
2. Email
3. Username

Generate the password with the `generate-random-string` tool. The username can be the email address.

Once you have this, do the following:

1. Create a new user with the `create-user` tool.
2. Associate the user with the `root` role with the `add-to-role` tool.
3. Send a welcome email with the `send-email` tool, including the login URL below.

URL for email: `https://dashboard.ainiro.io?backend=[BACKEND_URL]`

Where `[BACKEND_URL]` is substituted with the URL-encoded backend URL from the system message.

In the email, inform the user of the login URL, their username, and their password, and let them know they can change the password later in Misc / Users & Roles after logging in.
