# Teams Developer CLI operations

## Install and authenticate

Require Node.js 20 or later. Inspect before installing:

```powershell
node --version
npm --version
teams --version
```

If `teams` is unavailable, install it globally:

```powershell
npm install -g @microsoft/teams.cli
teams --version
```

Authenticate interactively:

```powershell
teams login
teams status -v
```

Use device code when the browser cannot open on the same machine:

```powershell
teams login --device-code
```

The user must complete sign-in and consent. Do not collect credentials. Use `teams logout` only when requested or when switching accounts is necessary and confirmed.

## Discover capabilities

Always consult installed CLI help because commands can change:

```powershell
teams --help --json
teams app --help --json
teams project --help --json
```

Use interactive menus for exploration. For agent automation, pass explicit IDs and flags and request JSON output where supported. Set `TEAMS_NO_INTERACTIVE=1` only after all required values are known.

## App and bot lifecycle

Common operations:

```powershell
teams app list --json
teams app get <teamsAppId> --json
teams app create --help --json
teams app update <teamsAppId> --help --json
teams app doctor <teamsAppId> --json
teams app bot get <teamsAppId> --json
```

Use an Azure-managed bot when OAuth or SSO is required. Before migration, ensure Azure CLI is authenticated to the same tenant and ask for confirmation:

```powershell
az login
az account show
teams status -v
teams app bot migrate <teamsAppId> --subscription <subscriptionId> --resource-group <resourceGroup>
```

## Manifests and packages

Download before modifying so changes are reviewable:

```powershell
teams app manifest download <teamsAppId> <path>
teams app package download <teamsAppId> --output <path>
teams app manifest update <teamsAppId> --help --json
teams app manifest upload <manifestPath> <teamsAppId>
```

Inspect exact installed help for positional parameters. Validate the manifest and retain the downloaded original before upload. Explain that users may need to reinstall or update the app in Teams after manifest or valid-domain changes.

## Resource-specific consent permissions

Inspect the catalog and current state before writes:

```powershell
teams app rsc list <teamsAppId> --json
teams app rsc add <teamsAppId> --help --json
teams app rsc remove <teamsAppId> --help --json
teams app rsc set <teamsAppId> --help --json
```

Use `set` for declarative convergence only after displaying additions and removals. Explain the scope and whether consent occurs during app installation.

## Credentials

Do not generate or request a client secret for the interactive user authentication used to read, summarize, or reply to messages. Those features use public-client delegated OAuth, Teams SSO, browser consent, or device code.

Generate a backend bot secret only when separately required by application infrastructure and after confirming its protected storage location:

```powershell
teams app auth secret create <teamsAppId> --help --json
```

Treat the returned value as sensitive application infrastructure. It is not supplied by the end-user and must not be included in normal logs, summaries, commits, or chat. Prefer a secret manager.

## Project scaffolding

Select a supported language and inspect templates:

```powershell
teams project new typescript <name> --help --json
teams project new python <name> --help --json
teams project new csharp <name> --help --json
```

Do not overwrite a non-empty target directory. After scaffolding, install dependencies, configure credentials, start the application, expose its messaging endpoint through an HTTPS development tunnel, update the app endpoint, and run diagnostics.

## Update and troubleshoot

```powershell
teams self-update
teams status -v
teams app doctor <teamsAppId> --json
teams app get <teamsAppId> --json
```

Check tenant mismatch, sideloading policy, endpoint HTTPS reachability, manifest `validDomains`, bot location, OAuth connection, and required consent before recreating resources.
