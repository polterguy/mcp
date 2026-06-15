# MCP for Magic

A [Model Context Protocol](https://modelcontextprotocol.io) server, implemented entirely in
Hyperlambda. It exposes your Magic backend's dynamic endpoints as MCP **tools**, so MCP
clients (Claude Desktop, Cursor, the Claude API, etc.) can discover and invoke them.

No C# code and no recompile — these are interpreted `.hl` files. Drop the folder in,
authenticate, and the server is live.

- **Endpoint:** `POST /magic/modules/mcp/mcp`
- **Protocol:** JSON-RPC 2.0 over HTTP (plain-JSON responses; a spec-compliant subset of the
  Streamable HTTP transport — no SSE)

## How it works

The core idea: **every dynamic endpoint under `modules/` is an MCP tool.**

- Tool **discovery** reuses the `[endpoints.list]` slot — the same metadata (arguments,
  description, authorization) Magic already exposes for its own UI.
- Tool **execution** reuses the `[execute-file]` slot — the same in-process invocation Magic's
  own AI function-calling uses.

So an MCP client reaches the exact same endpoints, enforcing the exact same per-endpoint
authorization, as an HTTP client would. There is no separate tool registry to maintain.

## Files

| File | Role |
| --- | --- |
| `mcp.post.hl` | The JSON-RPC dispatcher (the endpoint). |
| `tools.hl` | Internal helper — has no HTTP verb in its name, so it is **not** an endpoint. Returns the canonical tool catalogue (`name`, `file`, `description`, `input`) consumed by both `tools/list` and `tools/call`. This single source of truth guarantees a client can only invoke a tool that was also listed. |

## Supported methods

| Method | Behaviour |
| --- | --- |
| `initialize` | Handshake. Echoes the client's `protocolVersion` when supplied, advertises the `tools` capability, returns `serverInfo`. |
| `ping` | Liveness probe. |
| `tools/list` | One descriptor per exposed endpoint, each with a JSON-Schema `inputSchema` derived from the endpoint's `[.arguments]`. |
| `tools/call` | Resolves the tool name, invokes the underlying endpoint with the supplied arguments, returns the result as a `text` content block (the endpoint's JSON result, stringified). |
| notifications | Any JSON-RPC notification (a message with no `id`, e.g. `notifications/initialized`) is acknowledged with HTTP `202` and an empty body. |
| unknown method / tool | JSON-RPC error `-32601` (method not found) / `-32602` (unknown tool). |

## Authentication

The endpoint requires an **authenticated Magic user** — the client sends its Magic JWT as a
bearer token (`Authorization: Bearer <token>`).

`tools/call` runs the target endpoint through that same ticket, so the endpoint's own
`[auth.ticket.verify]` still applies: a non-`root` user calling a `root`-only tool is rejected
by the tool itself.

To make the server anonymous, remove the `auth.ticket.verify` line from `mcp.post.hl`.

## What is exposed

By default, **every endpoint under `modules/` except this MCP module itself.** The filter lives
in `tools.hl`:

```
strings.starts-with:x:@.dp/#/*/path
   .:magic/modules/
```

To expose endpoints on an explicit opt-in basis instead, add a marker (e.g. a `.mcp:bool:true`
node) to the endpoints you want exposed and tighten this filter accordingly.

### Tool naming

A tool name is `"<verb>_" + <path below modules/ with "/" replaced by "_">`, e.g.
`GET /magic/modules/crm/customers` becomes `get_crm_customers`. Resolution is done by matching
this name against the catalogue, so the mapping never has to be reversed.

### Argument type mapping

`[.arguments]` types are mapped to JSON-Schema types as follows:

| Hyperlambda | JSON Schema |
| --- | --- |
| `short`, `int`, `long` | `integer` |
| `float`, `double`, `decimal` | `number` |
| `bool` | `boolean` |
| everything else (`string`, `date`, `guid`, …) | `string` |

## Testing

```bash
TOKEN=...   # a Magic JWT
URL=localhost:5000/magic/modules/mcp/mcp

# Handshake
curl -s $URL -H "Authorization: Bearer $TOKEN" -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05"}}' | jq .

# List tools
curl -s $URL -H "Authorization: Bearer $TOKEN" -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/list"}' | jq .

# Call a tool
curl -s $URL -H "Authorization: Bearer $TOKEN" -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{"name":"get_crm_customers","arguments":{}}}' | jq .
```

## Connecting a client

Point any HTTP/Streamable-HTTP MCP client at the endpoint and supply the bearer token, e.g.:

```json
{
  "mcpServers": {
    "magic": {
      "url": "https://your-host/magic/modules/mcp/mcp",
      "headers": { "Authorization": "Bearer YOUR_MAGIC_JWT" }
    }
  }
}
```

Clients that only speak stdio can bridge to this HTTP endpoint with a tool such as
[`mcp-remote`](https://www.npmjs.com/package/mcp-remote).

## Known limitations (v1)

- A tool that throws propagates as a normal Magic error response rather than an MCP
  `isError` content block, so a client won't see the failure in-band.
- `ping` returns `"result":null` and a description-less tool returns `"description":null`
  (the lambda-to-JSON serializer renders childless nodes as scalars). Clients tolerate this.
- An install with **no** `modules/` endpoints would serialize `tools` as `null` rather than `[]`.
- Tool names are not truncated to the 64-character limit some clients enforce, which could
  matter for very deeply nested module paths.
- Transport is request/response JSON only; there is no SSE stream or server-initiated message.
