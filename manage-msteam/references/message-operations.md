# Read, summarize, and reply to Teams messages

## Capability boundary

Use delegated Microsoft Graph when acting on behalf of the signed-in user. Authenticate through Teams SSO, browser authorization with PKCE where supported, or device code; never require a client secret from the user. Use the bot activity context when responding as an installed bot. The Teams Developer CLI sets up the app, bot, manifest, SSO, and permissions; it does not itself read or send ordinary Teams messages.

Do not scrape Teams web or desktop when Graph or the bot context can perform the operation.

## Check runtime readiness

Before promising to read or reply, inspect whether the agent has a callable Microsoft Graph/Teams tool or is running inside the configured Teams bot. If neither exists, explain that a Markdown skill alone cannot access tenant messages. Use the Teams CLI to provision the app/bot and implement or connect a public-client Graph-backed tool first; then let the user authenticate and consent in the browser or Teams app. Never fabricate message access from a successful `teams login` alone, and never ask the user for a client secret.

## Resolve the destination

Before reading content:

1. Determine whether the target is a group chat or a team channel.
2. Resolve display names to stable IDs.
3. If multiple matches exist, show name, type, participants or team, and last activity without exposing message bodies; ask the user to select.
4. Record the resolved chat ID, or team ID and channel ID.
5. Apply a bounded time range or message count. Default to the smallest range that answers the request, such as messages since the previous business day or the latest 50 messages.

Never silently combine similarly named channels or chats.

## Read a group chat

Use current Microsoft Graph documentation and typed endpoints to:

1. List chats visible to the signed-in user.
2. Resolve the requested group chat.
3. List messages with pagination.
4. Include replies or hosted content only when needed.
5. Preserve for each item: message ID, creation/modification time, sender display name, body text, reply relationship, and web URL when returned.

Request the least-privileged delegated chat permissions supported by the chosen endpoints. A read-only workflow must not request write access merely for convenience.

## Read a channel

Use current Microsoft Graph documentation and typed endpoints to:

1. Resolve the team and channel.
2. List channel root messages within the requested window.
3. Fetch replies for relevant root messages so threaded decisions are not lost.
4. Preserve team ID, channel ID, root message ID, reply ID, timestamps, authors, and web URLs.

Channel history permissions are often more sensitive and can require administrator consent or be restricted by tenant policy. Report consent failures rather than attempting to bypass them.

## Summarize

Normalize HTML message bodies to readable text while preserving meaningful links, mentions, code, lists, and quoted context. Exclude automated join/leave noise unless relevant.

Produce a concise structure appropriate to the request:

- Executive summary.
- Decisions made.
- Action items with owner and deadline when explicitly stated.
- Open questions or blockers.
- Important links and files.
- Coverage window and number of messages processed.
- Source references using author, timestamp, and message web URL or stable message ID.

Do not infer an owner, deadline, decision, or consensus that the messages do not establish. Mark uncertain statements as uncertain. Do not send the summary to Teams unless requested.

## Reply to a message or thread

Resolve the exact target before writing:

- Group chat: reply or send through the supported chat-message endpoint and preserve any reply context supported by the API.
- Channel: post a reply beneath the selected root message rather than creating a new root post when the user says “reply”, “respond”, or “phản hồi”.
- Bot context: use the incoming activity's reply/send context so the response remains in the correct conversation or thread.

Before the write, show:

```text
Destination: <group chat, or team / channel>
Replying to: <author, timestamp, short excerpt, message ID>
Message: <exact rendered text>
Identity: <signed-in user or bot>
```

Require confirmation unless the current user instruction already specifies the unique destination, unique target message, exact response content, and clear intent to send. After sending, verify the returned message ID and destination. Never retry a timed-out write blindly: first check whether the message was created to avoid duplicates.

## Recommended delegated permissions

Determine exact permissions from current Microsoft Graph documentation during implementation. Typical capabilities may involve delegated chat read/read-write, team/channel discovery, channel message read, and channel message send permissions. Do not hard-code a broad permission bundle into every app. Request scopes incrementally according to the enabled features and state when administrator consent is required.

## Failure handling

- `401`: refresh/re-authenticate; do not expose the token.
- `403`: report the endpoint, requested operation, consent requirement, and tenant policy implication.
- `404`: re-resolve the chat/team/channel/message IDs and verify the user still has access.
- `429`: honor `Retry-After` and continue pagination without duplicate processing.
- Timeout on send: query for the expected message before retrying.
- Partial history: state the coverage gap explicitly in the summary.
