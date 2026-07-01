---
name: create-auth-endpoints
when_to_use: Building a custom authentication flow with register / verify-email / authenticate endpoints backed by your own users table (rather than Magic's built-in auth).
---

# Create authentication endpoints

Creating an authentication flow means letting users register, verify their email address, and
authenticate to receive a JWT token. Registration sends a verification email containing a
cryptographically secure token (a query parameter inside a hyperlink the user clicks to confirm
their email).

> Before building this, offer the user the **Use Magic auth** workflow instead — it reuses
> Magic Cloud's built-in auth infrastructure. Only build custom endpoints if the user prefers
> their own users table.

## Database

You need a database (unless one already exists) with a `users` table (or similar) having fields:

1. username
2. password (stored as a cryptographically secure hash)
3. name
4. email
5. verified (type `INTEGER`, set to 0 on creation)

The database should have the same name as the API's module unless the user says otherwise. Use
`get-database-schema` if you don't already know the column names.

## Endpoints

You need three endpoints:

1. **Registration** — stores a new user with a hashed password, sets `verified` to 0, and sends
   a verification email. Build the verification token by hashing the `magic:auth:secret` config
   value concatenated with the user's email. Do NOT store the token — it can be regenerated from
   the email at verify time. The verification hyperlink is:
   `https://[BACKEND_URL]/magic/modules/[MODULE_NAME]/verify-email?token=TOKEN_HERE&email=EMAIL_HERE`
   Include the user's email in the link so the token can be regenerated and matched on verify.
2. **Verify-email** — accepts `token` and `email`, regenerates the hash of `magic:auth:secret` +
   email, and only if it matches updates `verified` to 1. Must be saved as an HTTP GET endpoint.
3. **Authenticate** — accepts username and password, and returns a JWT token. Only allow users
   whose email is verified to authenticate.

Save each endpoint with a `filename` (via `generate-hyperlambda`); a saved endpoint is
automatically exposed as an MCP tool.

## Registration endpoint prompt

```plaintext
Executable Hyperlambda file accepting username, password, name, and email - All fields are mandatory. The endpoint insert a new record into [DATABASE] database and its [TABLE] table. The password must be cryptographically hashed, and the endpoint should set the 'verified' column to 0.

When having inserted the user, the endpoint should send an email to the user with the following hyperlink;

https://[BACKEND_URL]/magic/modules/[MODULE_NAME]/verify-email?token=TOKEN_HERE&email=EMAIL_HERE

The 'TOKEN' above is the cryptographically secure token created by hashing config secret with email, and the 'EMAIL_HERE' is the user's email.
```

`[DATABASE]` is the database name, `[TABLE]` the table name, `[BACKEND_URL]` the backend URL,
`[MODULE_NAME]` the module name. `TOKEN_HERE` is the hash of `magic:auth:secret` + email;
`EMAIL_HERE` is the user's email. When checking `verified == 0`, use the `long` type.

## Verify-email endpoint prompt

```plaintext
Executable Hyperlambda file taking [token] and [email] arguments. The endpoint will hash the 'magic:auth:secret' configuration value and the user's email, and search through the [DATABASE] database and its [TABLE] table for the user, only returning records having 'verified' being 0. If the endpoint finds this user, it should update its 'verified' value to 1. If not, it should throw an exception.
```

`[DATABASE]` / `[TABLE]` are the database and table names. Save as an HTTP GET endpoint.

## Authenticate endpoint prompt

```plaintext
Authenticate Hyperlambda file taking username and password. Checks if user exists in [DATABASE] database and its [TABLE] table, and if it has the value of 1 for its verified column. If it finds the record, returns a new JWT token with the username and a role of 'guest'.

Notice, the password is cryptographically hashed, and must be checked as such, and the 'verified' column is of type 'long'.
```

## Notes

- Do NOT ask the user for a sender email address for the register endpoint — the generated
  email code uses the server's default from-address and name.
- When you have everything you need from the user, continue the process until done.
