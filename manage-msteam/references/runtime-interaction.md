# Runtime Teams interaction

The Teams Developer CLI manages developer resources. It does not provide general commands such as `teams chat send` or control a signed-in Teams desktop/web client. Implement runtime collaboration through Teams SDK and Microsoft Graph.

## Select the surface

| Capability | Preferred surface |
| --- | --- |
| Receive and reply to bot messages | Teams SDK activity handlers |
| Mentions, Adaptive Cards, reactions, quoted/threaded replies | Teams SDK |
| Proactive bot message | Teams SDK with a stored conversation reference |
| Current user's profile, calendar, OneDrive/SharePoint files | Delegated Microsoft Graph |
| User chats, channel messages, teams and members | Microsoft Graph, subject to endpoint permissions and tenant policy |
| Feature unavailable from supported APIs | Teams web/desktop automation only as a confirmed fallback |

## Build a user-authenticated assistant

1. Scaffold or integrate a Teams SDK application.
2. Create or select its Teams app and Azure-managed bot.
3. Configure Entra OAuth/SSO and manifest `webApplicationInfo`.
4. Request delegated Graph scopes tied to concrete features.
5. In the message handler, check the user sign-in state and initiate sign-in when missing.
6. Use the resulting user Graph context for on-behalf-of operations.
7. Use the bot send context for replies, cards, and Teams-native conversation features.
8. Persist only the minimum conversation references needed for proactive messaging.
9. Verify in a development tenant before production installation.

For concrete group-chat/channel reading, traceable summarization, and thread replies, follow [Message operations](message-operations.md).

Conceptual TypeScript pattern:

```typescript
app.on('message', async ({ userGraph, isSignedIn, signin, send }) => {
  if (!isSignedIn) {
    await signin();
    return;
  }

  // Call a typed Microsoft Graph endpoint with userGraph.
  // Confirm consequential operations before invoking write endpoints.
  await send('Microsoft 365 sign-in is active.');
});
```

Use language-specific Teams SDK documentation and typed Graph endpoints for the actual implementation. Do not invent endpoint paths or permission names; verify them against current Microsoft documentation.

## UI fallback

Prefer Teams web over desktop automation because browser state is easier to inspect. Before using either:

1. Confirm that CLI, Teams SDK, and Graph do not support the required operation.
2. Let the user complete login, MFA, CAPTCHA, and consent.
3. Resolve and display the intended chat, channel, meeting, or recipient.
4. Ask for confirmation before clicking Send, Save, Invite, Delete, or similar controls.
5. Verify the visible result and stop on ambiguous UI state.

Do not describe UI automation as API-grade reliability. UI layout, localization, tenant policy, and client updates can break it.
