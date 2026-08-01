# Word DOCX workflow

## Inspect

```powershell
officecli view <file.docx> outline --json
officecli view <file.docx> stats --json
officecli view <file.docx> annotated --json
officecli get <file.docx> / --depth 3 --json
officecli query <file.docx> 'paragraph[style=Heading1]' --json
```

Inspect styles, sections, headers, footers, tables, comments, revisions, content controls, fields, and links relevant to the request. Prefer stable paragraph paths containing `@paraId=`.

## Create and edit

```powershell
officecli create <output.docx>
officecli add <output.docx> /body --type paragraph --prop text="Title" --prop style=Heading1 --json
officecli set <file.docx> '<stable-path>' --prop font.bold=true --json
officecli set <file.docx> / --find "old text" --replace "new text" --json
```

Run `officecli help docx <element> --json` before using complex tables, sections, numbering, charts, equations, revisions, or content controls. Preserve section layout and style inheritance instead of applying direct formatting everywhere.

For template population, use OfficeCLI `merge` with JSON placeholder data. For repeatable structures, use `dump` and atomic `batch` rather than rebuilding complex OOXML manually.

## Verify Word output

Check:

- Heading hierarchy and TOC behavior.
- Page/section breaks, margins, orientation, headers, and footers.
- Table width, row splitting, merged cells, and overflow.
- Missing fonts, images, alt text, broken links, and unresolved fields.
- Comments and tracked changes preservation.

Use OfficeCLI `refresh` only when its current help confirms the environment supports the required fields. Then follow the common verification workflow.
