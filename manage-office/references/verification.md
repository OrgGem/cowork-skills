# Verification and delivery

Complete every applicable step using OfficeCLI only.

## Structural validation

```powershell
officecli save <file>
officecli validate <file> --json
officecli view <file> issues --json
officecli view <file> stats --json
```

Treat validation errors as blocking. Address issue findings that affect correctness, readability, layout, formulas, links, or accessibility. Explain intentionally accepted warnings.

## Structured readback

Use `get` and `query` to confirm the exact changed nodes, values, formulas, styles, and relationships. Do not infer success solely from a zero exit code.

For multi-step changes, compare requested edits with the atomic batch result and confirm that rollback did not occur.

## Visual verification

Render using OfficeCLI:

```powershell
officecli view <file> html --out <preview.html> --json
officecli view <file> screenshot --out <preview-path> --json
```

Consult `officecli view --help` for exact output rules for the installed version. Inspect every relevant page, worksheet region/chart, or slide. Iterate through edit → validate → render until no material issue remains.

If OfficeCLI cannot render a feature, report it as unverified. Do not invoke another renderer.

## Close and deliver

```powershell
officecli close <file>
```

Confirm the output exists and is non-empty using normal filesystem metadata. Deliver the working output and preserve the original. Report:

- Input and output paths.
- OfficeCLI version.
- Validation result.
- Issue scan result.
- Visual coverage inspected.
- Any unsupported or unverified feature.
