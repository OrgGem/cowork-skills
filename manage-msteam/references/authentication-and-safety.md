# Authentication and safety

## Choose the identity model

Use the correct identity for the requested effect:

| Need | Identity and surface |
| --- | --- |
| Manage Teams developer apps and bot registration | `teams login` and Teams Developer CLI |
| Create or manage Azure bot resources and OAuth connections | `az login`, same tenant as `teams login` |
| Read user profile, chats, calendar, or files on behalf of a user | Delegated Entra OAuth and Microsoft Graph |
| Run unattended organization-wide processing | Application permissions, administrator consent, and a separately secured service identity |
| Reply inside conversations where the bot is installed | Bot identity through Teams SDK |

Do not substitute CLI login tokens for a custom application's delegated Graph tokens. Register and configure the application's own Entra identity and request only the scopes needed by its features.

The end-user is not expected to own or provide a client secret. Configure the runtime as a public client using browser authorization with PKCE where supported, Teams SSO, or device-code flow. Bundle the application's client ID in configuration; a client ID identifies the app but is not a secret. Store access and refresh tokens only in the operating system's protected per-user cache.

## Interactive user authentication

For CLI management, use its existing public-client flow:

```powershell
teams login
```

The command opens a browser in a local interactive session. The user completes Microsoft sign-in, MFA, tenant selection, and consent without supplying a client secret. For remote/headless sessions use:

```powershell
teams login --device-code
```

Wait for the user to complete sign-in, MFA, tenant selection, and consent. Verify account and tenant with `teams status -v` without displaying tokens.

For a Teams app that accesses Graph on behalf of the user, configure OAuth/SSO with an Azure-managed bot. The expected flow is Teams client sign-in, Entra token exchange, then delegated Graph calls through the user context. The user accepts in Teams web/desktop or an Entra consent page. Do not ask them for a secret or store bearer tokens in source files.

Do not use client-credentials flow for reading or replying as the user. If an administrator has not granted a required delegated permission, report that requirement and stop; do not fall back to an application identity silently.

## Consent and least privilege

Before requesting a permission:

1. Map it to a concrete feature.
2. Prefer delegated permission over application permission for interactive assistants.
3. State whether user or administrator consent is expected.
4. Avoid broad permissions when a resource-specific consent permission is sufficient.
5. Re-check tenant policy and manifest permission declarations.

Never promise that consent will succeed: organizational policy can block permissions, app installation, sideloading, or external users.

## Consequential action policy

Require explicit confirmation immediately before:

- Sending, editing, or deleting a message as a user or bot when not already unambiguously requested.
- Adding or removing members, permissions, apps, tabs, or subscriptions.
- Uploading a manifest or changing bot endpoints in a production app.
- Migrating a bot, rotating a secret, or replacing authentication configuration.
- Creating meetings or modifying calendar events.

Show the resolved target, tenant, action, and relevant content. For bulk actions, show the count and a representative preview.
