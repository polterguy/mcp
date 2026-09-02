---
name: openid-connect-sso-authentication
when_to_use: When the user wants SSO or "log in with Google / Microsoft / Okta / Auth0 / Keycloak / LinkedIn / Slack / GitHub" - OpenID Connect (OIDC) or OAuth sign-in - in a generated app or full-stack application built on Magic.
---

Magic supports single sign-on through OpenID Connect and OAuth providers. Eight providers ship with the system: Google, Microsoft Entra ID, Okta, Auth0, Keycloak, LinkedIn, Slack and GitHub. A provider becomes available the moment its configuration is present - nothing else has to be enabled - and a frontend never hard-codes a provider: it asks the backend which ones are configured and renders one sign-in button per provider returned.

## Endpoints

* HTTP GET `/magic/system/auth/openid-providers?createNonce=true` - lists the configured providers, and begins a sign-in transaction for each one.
* HTTP GET `/magic/system/auth/openid-login?token=ID_TOKEN` - finishes a sign-in for an **implicit-flow** provider (Google).
* HTTP POST `/magic/system/auth/openid-exchange` with `{ "code", "state", "redirect_uri" }` - finishes a sign-in for a **code-flow** provider (all the others).

Both finishing endpoints answer the same way:

```json
{
   "ticket": "JWT.TOKEN.HERE",
   "extra": {
      "email": "thomas@ainiro.io",
      "name": "Thomas Hansen"
   }
}
```

`ticket` is a normal Magic JWT token. Use it as `Authorization: Bearer TOKEN` on every later request to the backend.

## Two flows, told apart by `response_type`

Every entry the providers endpoint returns carries `name`, `issuer`, `url`, `client_id`, `response_type` and `scope`. The remaining fields depend on the flow, and `response_type` tells you which flow you are in.

An implicit-flow entry (`response_type` is `id_token`) carries a `nonce`:

```json
{
  "name": "Google",
  "issuer": "https://accounts.google.com",
  "url": "https://accounts.google.com/o/oauth2/v2/auth",
  "client_id": "123456789-efqwevdgfh.apps.googleusercontent.com",
  "response_type": "id_token",
  "scope": "openid email profile",
  "nonce": "SOME_NONCE_HERE"
}
```

A code-flow entry (`response_type` is `code`) carries a `state` and, for providers using PKCE, a `code_challenge` and `code_challenge_method`:

```json
{
  "name": "Microsoft",
  "issuer": "https://login.microsoftonline.com/TENANT_ID/v2.0",
  "url": "https://login.microsoftonline.com/TENANT_ID/oauth2/v2.0/authorize",
  "client_id": "0f1e2d3c-...",
  "response_type": "code",
  "scope": "openid email profile",
  "state": "SOME_STATE_HERE",
  "code_challenge": "SOME_CHALLENGE_HERE",
  "code_challenge_method": "S256"
}
```

GitHub is plain OAuth rather than OIDC: its entry has `response_type` `code` and a `state`, but no challenge, and its scope is `read:user user:email`. Treat it exactly like the other code-flow providers.

The nonce, state and challenge are created by the backend when the providers endpoint is called with `createNonce=true`, and they are valid for 60 minutes. A code-flow transaction can be finished once; fetch the providers again for every new sign-in attempt.

## The frontend sign-in sequence

1. Fetch `/magic/system/auth/openid-providers?createNonce=true` and render one button per entry, labelled with `name`.
2. When a button is clicked, redirect the browser to the entry's `url` with these query parameters:
   * `client_id`, `response_type` and `scope`, copied from the entry
   * `redirect_uri` - the page's origin plus one fixed callback path, for example `https://example.com/oidc-callback`, identical for every provider
   * `nonce`, when the entry has one
   * `state`, when the entry has one
   * `code_challenge` and `code_challenge_method`, when the entry has them
   Copy each field exactly as returned; do not invent, rename or drop any of them.
3. When the provider redirects back to the callback path, read the URL:
   * If the fragment (`#`) contains `id_token`, call `GET /magic/system/auth/openid-login?token=` with that value. This is the implicit flow.
   * If the query string contains `code` and `state`, call `POST /magic/system/auth/openid-exchange` with `code`, `state` and the very same `redirect_uri` string used in step 2. This is the code flow. The code, the PKCE verifier and any client secret are exchanged server-side; the frontend never sees the provider's tokens.
   * If the query string contains `error`, show `error_description` to the user.
4. Store the returned `ticket` in localStorage and remove `id_token`, `code` and `state` from the URL so a reload cannot replay them.
5. Send the ticket as `Authorization: Bearer TOKEN` on every request to the backend.

Only the `redirect_uri` decides where the provider sends the user back, and every provider matches it character by character against what was registered with the provider, so build it from the origin plus the fixed path, never from the current page's route.

The UI flow around this is always the same:

1. Hide the main app and show the login UI
2. The user signs in with a provider
3. Hide the login UI and show the app UI
4. The user logs out - delete the ticket from localStorage
5. Hide the app UI and show the login UI

## Users, roles and endpoint access

When a user signs in through a provider for the first time, a Magic user is created with the email address as its username and added to the `guest` role. Such a user has no password and can only sign in through the provider. An administrator can grant it further roles afterwards, at which point it is an ordinary Magic user.

**IMPORTANT** - Because every provider sign-in produces a `guest` user, endpoints an app exposes to signed-in users should be restricted to the `guest` role unless the user explicitly asks otherwise: pass `auth` as `guest` to `crudify`, and say so in prompts to the Hyperlambda Generator.

## Configuration

Providers are configured under `magic:oidc` in the backend's configuration, which the user edits at `/configuration`. Each provider has its own key. A provider whose configuration is missing is simply not returned by the providers endpoint.

| Provider | Configuration keys | Flow | Notes |
|---|---|---|---|
| Google | `google:client-id` | implicit (`id_token`) | Create the client id in the Google Cloud console under APIs & Services > Credentials, and register the callback URL there. |
| Microsoft Entra ID | `microsoft:client-id`, `microsoft:tenant`, `microsoft:client-secret` | code + PKCE | `tenant` is the directory (tenant) id, and must be a concrete tenant rather than `common`. The secret is required for app registrations on the "Web" platform. |
| Okta | `okta:client-id`, `okta:issuer`, optional `okta:client-secret` | code + PKCE | `issuer` is the authorization server's issuer URL, e.g. `https://dev-123456.okta.com/oauth2/default`. |
| Auth0 | `auth0:client-id`, `auth0:issuer`, optional `auth0:client-secret` | code + PKCE | `issuer` is the tenant's issuer URL exactly as Auth0 reports it, including the trailing slash, e.g. `https://your-tenant.eu.auth0.com/`. |
| Keycloak | `keycloak:client-id`, `keycloak:issuer`, optional `keycloak:client-secret` | code + PKCE | `issuer` is the realm's issuer URL, e.g. `https://keycloak.example.com/realms/master`. Leave the secret out for public clients. |
| LinkedIn | `linkedin:client-id`, `linkedin:client-secret` | code, no PKCE | Requires the "Sign In with LinkedIn using OpenID Connect" product on the LinkedIn app. The secret is required. |
| Slack | `slack:client-id`, `slack:client-secret` | code | "Sign in with Slack". The secret is required. |
| GitHub | `github:client-id`, `github:client-secret` | code (OAuth, no id_token) | The secret is required. The user's identity is read from GitHub's user API after the exchange. |

The keys are nested, so Microsoft for instance is configured as:

```json
{
  "magic": {
    "oidc": {
      "microsoft": {
        "client-id": "0f1e2d3c-...",
        "tenant": "11111111-2222-3333-4444-555555555555",
        "client-secret": "SECRET"
      }
    }
  }
}
```

Several providers may be configured at the same time; every configured one is returned, and the frontend shows a button for each.

When helping a user set up a provider, tell them where the client id comes from - the provider's developer console - and give them the exact callback URL to register there, since a mismatched redirect URI is the most common reason a sign-in is refused. Client secrets belong in the backend configuration only, never in frontend code.

## Adding a provider

Any Hyperlambda slot named `magic.openid.providers.NAME` created at startup is picked up automatically by the providers endpoint. Model a new implicit-flow provider on the Google slot, and a new code-flow provider on the Okta slot, under `/system/auth/magic.startup/`.
