# PowerPoint PPTX workflow

## Inspect

```powershell
officecli view <file.pptx> outline --json
officecli view <file.pptx> stats --json
officecli get <file.pptx> / --depth 2 --json
officecli get <file.pptx> '/slide[1]' --depth 2 --json
officecli query <file.pptx> 'shape:no-alt' --json
```

Inspect slide size, theme, masters, layouts, placeholders, notes, hidden slides, charts, tables, media, animations, transitions, links, and accessibility metadata. Prefer stable shape paths with `@id=` or `@name=`.

## Create and edit

```powershell
officecli create <output.pptx>
officecli add <output.pptx> / --type slide --prop title="Title" --json
officecli add <output.pptx> '/slide[1]' --type shape --prop text="Content" --prop x=2cm --prop y=4cm --json
officecli set <file.pptx> '/slide[1]/shape[@name=Title 1]' --prop font.size=28pt --json
```

Use `officecli help pptx <element> --json` before adding or modifying layouts, placeholders, charts, tables, connectors, diagrams, animations, transitions, video, audio, OLE, SmartArt, or 3D models.

Reuse the existing theme, layouts, and placeholders when editing a user deck. Clone an existing slide or element with OfficeCLI `add --from` when it is safer than recreating relationships.

## Verify PowerPoint output

Check every slide for:

- Text overflow, clipping, weak contrast, and unintended overlap.
- Alignment, spacing, visual hierarchy, and slide-to-slide consistency.
- Image crop, aspect ratio, resolution, and alt text.
- Table readability and chart labels/legends.
- Speaker notes, links, transitions, and animations requested by the user.

Render all slides with OfficeCLI screenshot or HTML view and inspect them visually. Do not rely only on the document tree.
