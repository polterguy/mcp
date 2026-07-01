---
name: use-magic-auth
when_to_use: When the user wants to authenticate users, log in, manage JWT tokens, change passwords, or decide whether to reuse Magic's built-in RBAC auth versus building custom auth endpoints.
---

Magic Cloud contains integrated authentication and authorization built on RBAC (Role Based Access Control). This is a commonly used, robust standard, built on top of JWT transmitted as Bearer tokens in the Authorization HTTP header. The following HTTP API exists for authentication and logging in users.

* GET `magic/system/auth/authenticate`
  - Requires a username and a password QUERY parameter, and returns a valid JWT token as `ticket` in JSON format if successful.
* PUT `magic/system/auth/change-password`
  - Requires a password argument that must be minimum 12 characters in length.
  - Can only change the password of the currently authenticated user, so it requires a valid JWT to function.
* GET `magic/system/auth/refresh-ticket`
  - Creates a new JWT token from an existing one, with an expanded expiration, as a `ticket` JSON field. This allows you to poll the server (e.g. every 10 minutes) for a new token and replace your client's existing token, to avoid being thrown out as your JWT expires.

If a token generated on the server is used in any Hyperlambda code and sent to the server as a JWT Bearer token in the Authorization HTTP header, the internal Hyperlambda slots will correctly pick up on it. This lets you reuse Magic's existing authentication and authorization system instead of rolling your own, which could be hazardous since it might accidentally result in insecure code.

**NOTICE!** - Magic Auth authentication and authorization is 100% compatible with any JWT/token-based authentication and authorization schemes you can apply using pure Hyperlambda. So the question isn't whether the user wants to use "two different systems" — it's whether the user wants to build his own endpoints and logic, which is sometimes required (for verifying emails, automatically registering, uploading files during registrations, etc.).
