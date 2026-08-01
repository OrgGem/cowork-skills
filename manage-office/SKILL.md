---
name: manage-office
description: Create, inspect, analyze, edit, merge, validate, render, and repair Microsoft Office documents using only the OfficeCLI command-line engine. Use for Word DOCX, Excel XLSX, and PowerPoint PPTX tasks including document generation, formatting, tables, formulas, charts, pivots, comments, revisions, slides, layouts, templates, batch edits, visual QA, and issue detection. Install @officecli/officecli through npm when needed. Do not use LibreOffice, Microsoft Office automation, Python document libraries, Node document libraries, or alternate document converters.
---

# Manage Office documents with OfficeCLI

Use `officecli` as the only document-processing engine. Normal shell operations may inspect paths and create a safety copy, but must not parse, transform, render, or validate Office content.

## Route the task

- Read [installation and safety](references/installation-and-safety.md) before installing or changing a file.
- Read [Word workflow](references/word.md) for `.docx`.
- Read [Excel workflow](references/excel.md) for `.xlsx`.
- Read [PowerPoint workflow](references/powerpoint.md) for `.pptx`.
- Read [verification](references/verification.md) before delivering any created or modified file.

## Required workflow

1. Inspect the target path, extension, existing file, and requested output.
2. Verify `officecli --version`. Install the pinned version only if unavailable or incompatible.
3. Never edit the only copy of a user-provided file. Create a clearly named working copy with a normal filesystem copy operation unless the user explicitly requests in-place editing and a recoverable backup exists.
4. Inspect content and structure using `view`, `get`, and `query` with `--json`.
5. Ask OfficeCLI for live schema help before using an uncertain element, property, selector, or flag:

```powershell
officecli help <format> <element> --json
officecli <command> --help
```

6. Prefer the highest-level operation that works:

   - L1: read and inspect with `view`, `get`, and `query`.
   - L2: edit the document DOM with `set`, `add`, `remove`, `move`, `swap`, `merge`, or `batch`.
   - L3: use `raw` or `raw-set` only when OfficeCLI exposes no DOM operation and the exact OOXML effect is understood.

7. Use atomic `batch` for related multi-step edits. Do not enable `--best-effort` unless partial success is explicitly acceptable.
8. Save or close a resident session before verification or delivery.
9. Run OpenXML validation, issue detection, structured readback, and OfficeCLI rendering. Visually inspect rendered output and iterate until acceptable.
10. Deliver the new file without deleting the original. Report the OfficeCLI version and verification performed.

## Operating rules

- Do not invoke LibreOffice, `soffice`, COM automation, Word, Excel, PowerPoint, `python-docx`, `openpyxl`, `python-pptx`, `xlsxwriter`, Pandoc, or another Office engine.
- Prefer `--json` for machine-readable output; do not scrape human help text when JSON exists.
- Prefer stable `@id=`, `@name=`, or `@paraId=` paths over positional indices during multi-step edits.
- Treat macros, external links, OLE objects, embedded files, and remote images as untrusted. Do not execute or activate them.
- Preserve formulas, styles, themes, relationships, comments, revisions, notes, and accessibility metadata unless the request requires changing them.
- Do not claim pixel-perfect Microsoft Office fidelity from OfficeCLI's renderer. State any feature that could not be rendered or verified.
- Never deliver a file that fails `officecli validate` without clearly reporting the failure and obtaining user direction.
