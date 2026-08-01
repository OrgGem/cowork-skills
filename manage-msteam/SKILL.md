---
name: manage-msteam
description: Set up and operate Microsoft Teams through the globally installed Microsoft Teams Developer CLI, Teams SDK, and delegated Microsoft Graph access. Use when an agent must install or update @microsoft/teams.cli, authenticate a user through browser or device code, inspect or manage Teams apps and bots, or read and summarize group-chat or channel messages, find relevant discussion, and reply to a message or thread. Also covers manifests, packages, permissions, credentials, SSO, projects, meetings, files, reactions, proactive messaging, and troubleshooting. Distinguish CLI infrastructure management from end-user Teams operations and route each request to the supported surface.
---

# Manage Microsoft Teams

Use the Teams Developer CLI for developer infrastructure and the Teams SDK or Microsoft Graph for runtime collaboration. Do not claim that `teams` CLI can operate a user's normal Teams client, chats, or meetings directly.

## Route the request

1. For installation, authentication, status, configuration, app registration, bot infrastructure, manifests, packages, RSC permissions, secrets, migration, diagnostics, or project scaffolding, read [CLI operations](references/cli-operations.md).
2. For messages, chats, channels, members, meetings, files, reactions, mentions, cards, or proactive messages, read [Runtime interaction](references/runtime-interaction.md).
3. For reading, summarizing, or replying in group chats and channels, read [Message operations](references/message-operations.md) and [Authentication and safety](references/authentication-and-safety.md).
4. For SSO or delegated access on behalf of a signed-in user, read [Authentication and safety](references/authentication-and-safety.md), then the relevant operation guide.
5. For failures, inspect command help and diagnostics before changing resources:

```powershell
teams --help --json
teams status -v
teams app doctor <teamsAppId> --json
```

## Core workflow

1. Detect the operating system, shell, Node.js version, CLI availability, and current authentication state.
2. Install the CLI only when missing, using the exact global package requested by this skill:

```powershell
npm install -g @microsoft/teams.cli
```

3. Verify with `teams --version` and discover current commands with `teams --help --json`. Treat live help as authoritative when it differs from bundled references.
4. Run `teams login`. Allow the user to complete browser authentication. Use `teams login --device-code` for remote, headless, or failed browser sessions.
5. Run `teams status -v` and verify the expected Microsoft 365 tenant. If Azure operations are needed, also verify `az account show` and tenant alignment.
6. Inspect existing state before creating or mutating resources. Prefer `--json` for reliable parsing.
7. Explain the proposed target and effect before consequential changes. Obtain confirmation before sending messages, uploading or replacing manifests, changing permissions, migrating bots, rotating secrets, or modifying production resources.
8. Execute the narrowest supported command or SDK/Graph operation, then verify the resulting state.

## Agent operating rules

- Never ask the user to paste passwords, MFA codes, access tokens, refresh tokens, or client secrets into chat.
- Assume the user has no client secret. Use public-client OAuth: browser authorization with PKCE where supported, Teams SSO, or device code for remote sessions. A configured client ID is not a secret and must not be requested from the user.
- Do not use client-credentials flow for user chat/channel operations; it does not represent the signed-in user or their interactive consent.
- Never automate password or MFA entry. Pause while the user completes Microsoft sign-in or consent in the browser/app.
- If backend infrastructure independently requires a credential, provision it outside the user flow and store it in a secret manager. Never ask the user for it or expose it in chat.
- Do not use `--yes` until the target and impact are understood. Do not use it for ambiguous destructive actions.
- Prefer explicit IDs over display names after resolving and showing the matching object.
- Prefer delegated least-privilege permissions for user-scoped Graph access. Explain when administrator consent is required.
- Do not use browser or desktop UI automation merely because Teams is open. Use it only for a capability unavailable through CLI, Teams SDK, or Graph, and obtain confirmation before visible actions.
- Treat reading and sending Teams content as access to potentially sensitive organizational data. Retrieve only what the task requires.
- Treat summarization as read-only. Do not post the summary back to Teams unless the user explicitly requests it.
- Before replying, show the destination, parent message or thread, and exact proposed text; require confirmation unless the user's current request already provides all three unambiguously.
- Preserve message IDs and web URLs while reading so claims in a summary can be traced to their source messages.

## Completion criteria

Report:

- CLI version and signed-in tenant/account (never tokens).
- The app, bot, team, channel, chat, or meeting IDs affected where appropriate.
- Commands or API surface used.
- Verification result and any tenant/admin prerequisites still outstanding.
- Any action that the user must complete in Teams, Entra, Azure, or a browser.
