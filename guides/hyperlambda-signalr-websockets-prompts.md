---
name: hyperlambda-signalr-websockets-prompts
when_to_use: When generating Hyperlambda that pushes SignalR / web socket notifications from the server to connected clients.
---

# Hyperlambda example prompts for SignalR and web socket push notifications

Hyperlambda can send SignalR web socket push notifications from the server to the client. Below is an example prompt for the `generate-hyperlambda` tool that should work.

```plaintext
Publish a SignalR message on channel 'xyz' with 'name' being 'Thomas' and 'email' being 'thomas@ainiro.io'
```

For clients to receive messages, they must connect to the backend using the `/sockets` URL, using a SignalR library for .NET Core version 10 or higher. Once connected, the server can send push notifications to the client. You can also combine Magic's SignalR features with database access, file readings, HTTP invocations, etc.
