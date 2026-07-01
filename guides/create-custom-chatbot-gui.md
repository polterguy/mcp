---
name: create-custom-chatbot-gui
when_to_use: Hand-building a custom frontend chat GUI that talks to Magic's chat endpoint (streaming via SignalR, sessions, references, history, file uploads) — instead of embedding the standard chatbot.
---

# Create a custom chatbot GUI

Use this when the user wants a fully custom frontend chat GUI for an existing AI chatbot type,
wiring everything up themselves rather than using Magic's standard embed script. This guide is
about the **HTTP/SignalR contract** of the chat endpoint; you write the HTML/JS/CSS.

> If the user just wants to drop a chatbot onto a website, the `create-embed-script` guide is
> simpler. Confirm which they want before following this one.

A chatbot type is a machine-learning type in the backend. If the user doesn't know which type to
use, list types with `list-types` and show them.

The GUI sends prompts to `[BACKEND_URL]/magic/system/openai/chat`, optionally streams tokens
back via SignalR, renders references, supports persisted sessions, and optionally supports chat
history and file uploads.

Gather from the user:

1. The AI chatbot type to use (becomes the `type` argument).
2. Whether to stream. If yes, the frontend must connect SignalR and subscribe to the session
   channel **before** invoking the chat endpoint.
3. Whether to persist conversations. If yes, send `permanent = true`.
4. Whether to show references. If yes, send `references = true` and render the returned
   `references` array.
5. Whether to support file uploads. If yes, submit files as `file` arguments using
   `multipart/form-data`.
6. Whether to show previous sessions (use the history endpoints).
7. Whether to handle follow-up questions (see below).

## Chat endpoint

POST endpoint accepting: `prompt`, `type`, `session`, `stream`, `permanent`, `references`,
`user_id`, `recaptcha_response`, `data`, `meta`, `extra`, `system_message_override`, `file`.

- `prompt` and `type` are mandatory.
- `session` drives the server-side session cache, persisted history, and the SignalR channel
  name when streaming. Generate it as a random string for a new conversation and reuse it for
  the whole conversation.
- `stream = true` → backend starts a background request and pushes incremental updates over
  SignalR; subscribe **first**, then invoke. `stream = false` → final result returned directly
  in the HTTP response.
- `permanent = true` → conversation stored in persisted history.
- `references = true` (with an embeddings-backed type) → may return a `references` collection to
  render below the assistant message.
- `file` → backend can send images/PDFs to the model. With files use `multipart/form-data`;
  without files the same endpoint accepts JSON. The endpoint accepts both.

## Required frontend flow

1. Create or reuse a `session` value (random string for new conversations; reuse for the whole
   conversation).
2. If streaming, connect SignalR and subscribe to the current `session` first; ensure it's ready
   before invoking the endpoint.
3. POST the chat endpoint with `prompt`, `type`, `session`, `stream` (and the optional fields
   only if required). Use `multipart/form-data` if files attached, JSON otherwise.
4. Immediately render the user's message.
5. Render the assistant response — non-streaming: render the `result` field; streaming:
   progressively append fragments from SignalR.
6. Render any `references` below the assistant message.
7. On completion, stop the typing state and finalize the message.

## SignalR streaming

When streaming, listen on the current `session` for incremental payloads. The backend sends each
payload as a JSON **string** — parse it before reading fields. Each typically has only one or a
few properties; inspect them to determine meaning. Handle:

1. `message` — append this text fragment to the current assistant message.
2. `finish_reason` — store on the current message if needed.
3. `finished` — mark response done and stop the loading indicator (finalize even if this final
   payload has no `message`).
4. `error` — show the error and stop the loading indicator.
5. `function_waiting` — optionally show "Working..." while a backend AI function executes.
6. `function_result` — NOT assistant text; metadata that a backend AI function succeeded.
   `invocation` holds the invocation payload, `file` the executed Hyperlambda function name.
7. `function_error` — NOT assistant text; an execution status message.

Do NOT assume every payload has a `message` field. Do NOT wait for streamed HTTP body chunks —
streaming comes through SignalR, not the POST response body.

### Example SignalR payloads

```json
{ "message": "Hello" }
{ "finish_reason": "stop" }
{ "finished": true }
{ "error": true, "status": 500, "message": "Something went wrong" }
{ "function_waiting": true }
{ "function_result": "Success!", "invocation": "{ ...json payload for function... }", "file": "module.folder.function" }
{ "function_error": "Function failed because ..." }
```

### JavaScript example

```javascript
const builder = new signalR.HubConnectionBuilder()
  .withAutomaticReconnect();

const connection = builder
  .withUrl("[BACKEND_URL]/sockets", {
    transport: signalR.HttpTransportType.WebSockets
  })
  .build();

let currentAssistantMessage = "";

connection.on(sessionId, payload => {
  if (payload.message) {
    currentAssistantMessage += payload.message;
    renderAssistantMessage(currentAssistantMessage);
  }

  if (payload.function_waiting) {
    showStatus("Executing function ...");
  }

  if (payload.function_result) {
    showStatus(payload.function_result);
    console.log("Function invocation payload:", payload.invocation);
    console.log("Function file:", payload.file);
  }

  if (payload.function_error) {
    showError(payload.function_error);
  }

  if (payload.finish_reason) {
    console.log("Finish reason:", payload.finish_reason);
  }

  if (payload.error) {
    showError(payload.message || "Unknown backend error");
    stopLoading();
  }

  if (payload.finished) {
    stopLoading();
    finalizeAssistantMessage(currentAssistantMessage);
  }
});

await connection.start();

await fetch("[BACKEND_URL]/magic/system/openai/chat", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    prompt: userPrompt,
    type: chatbotType,
    session: sessionId,
    stream: true,
    permanent: true,
    references: true
  })
});
```

Subscribe using the `session` ID as the SignalR event/channel name — listen on the same
`session` value you send to the chat endpoint.

## Suggested request payload (no files)

```json
{
  "prompt": "How do I reset my password?",
  "type": "[CHATBOT_TYPE]",
  "session": "[SESSION_ID]",
  "stream": true,
  "permanent": true,
  "references": true,
  "user_id": "[OPTIONAL_USER_ID]"
}
```

With file uploads, submit files as `file` arguments alongside this payload using
`multipart/form-data`.

## Response handling

- `stream = false` → response contains `result`, `finish_reason`, `db_time`, `stream`, and
  `references` (if requested/available).
- `stream = true` → immediate HTTP response mainly confirms the invocation and may include
  `db_time`, `stream`, `references`; the generated text arrives over SignalR.

## History support

If persisting history, use the history list endpoint (list persisted sessions for the user) and
the history get endpoint (load all messages for one session). Let the frontend switch between
sessions and update the active `session` before sending a new prompt.

## UI requirements

1. Message list (user + assistant messages).
2. Multiline prompt textbox.
3. Send button.
4. Typing/loading indicator.
5. Optional file upload control.
6. Optional references area below assistant messages.
7. Optional session history sidebar (if persisted history enabled).

## Follow-up questions

If the type returns follow-up questions, it appends them at the end of its response like:

```markdown
---
- Question 1
- Question 2
- Question 3
```

It may use `*` instead of `-`, so check for both. These are NOT part of the answer — render a
button for each, and clicking a button transmits its text to the backend as a new user request.

## Implementation rules

1. Always connect SignalR before invoking the chat endpoint when streaming.
2. Always send the same `session` value for the whole conversation.
3. New sessions can be created by the frontend as random strings.
4. Always append streamed fragments to the same in-progress assistant message.
5. Always handle `error` and `finished` so the UI doesn't get stuck loading.
6. Don't assume references are always returned.
7. Don't assume persisted history exists unless `permanent = true` is used.
8. If the type requires CAPTCHA, collect and send `recaptcha_response` (see below).
9. If files are attached, always use `multipart/form-data`.
10. If files are not attached, the same endpoint accepts JSON.

## Magic CAPTCHA

Some types require CAPTCHA. Include the frontend script from
`[BACKEND_URL]/magic/system/misc/magic-captcha-challenge.js`, then generate a token:

```javascript
mcaptcha.token(function (token) {
  // Pass token into the server here as `recaptcha_response`.
}.bind(this), 3);
```
