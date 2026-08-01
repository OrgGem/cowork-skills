# Installation and safety

## Install

Use npm global installation only. Do not pipe remote scripts into a shell.

Pinned tested baseline:

```powershell
npm install -g @officecli/officecli@1.0.143
officecli --version
officecli --help
```

If download or postinstall fails, report the npm error, proxy/firewall possibility, platform, and architecture. Do not silently install another document tool. Retry only after checking connectivity and npm configuration.

Use installed command help as authoritative because OfficeCLI changes frequently:

```powershell
officecli --help
officecli help --json
officecli help docx --json
officecli help xlsx --json
officecli help pptx --json
```

Do not upgrade an already working version during a document task unless the requested operation requires a newer capability.

## Protect files

Before modification:

1. Resolve the exact absolute input and output paths.
2. Confirm the input extension is `.docx`, `.xlsx`, or `.pptx`.
3. Create a working copy using a normal filesystem copy. Do not alter the original.
4. Ensure the output does not unintentionally replace another file.
5. Inspect protection, macros, external links, embedded objects, and validation state through OfficeCLI.

Normal filesystem listing and copying are allowed safety operations. All content inspection, editing, merging, formula handling, rendering, conversion, and validation must use OfficeCLI.

## Resident mode

For many operations on one file:

```powershell
officecli open <file>
# OfficeCLI operations
officecli save <file>
officecli close <file>
```

`save` flushes while retaining the resident. `close` flushes and releases the file. Always close before delivery. If a command fails, inspect resident/file-lock state before retrying.

## Atomic changes

Use a JSON batch for related writes. OfficeCLI 1.0.137+ rolls back the whole batch when any item fails. Inspect the JSON result for failed items and `atomicRolledBack`.

Do not use `--best-effort` by default. Never retry a partially unknown batch without re-reading current document state.
