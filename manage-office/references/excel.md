# Excel XLSX workflow

## Inspect

```powershell
officecli view <file.xlsx> outline --json
officecli view <file.xlsx> stats --json
officecli get <file.xlsx> / --depth 2 --json
officecli get <file.xlsx> /Sheet1/A1 --json
officecli query <file.xlsx> 'cell:has(formula)' --json
```

Inspect sheets, used ranges, tables, named ranges, formulas, pivots, charts, conditional formatting, validations, hidden content, external links, and workbook protection.

## Create and edit

```powershell
officecli create <output.xlsx>
officecli set <output.xlsx> /Sheet1/A1 --prop value="Header" --prop bold=true --json
officecli set <output.xlsx> /Sheet1/B2 --prop formula="=SUM(B3:B10)" --json
```

Use `officecli help xlsx <element> --json` for tables, formulas, charts, pivots, slicers, filters, sparklines, named ranges, validation, and conditional formatting. Never guess formula separators, cell types, date serials, range paths, or chart properties.

Prefer formulas over hard-coded derived values. Preserve existing formulas and named ranges unless replacement is requested. Use atomic batches for row/column insertion, formulas, totals, formatting, and charts that must remain consistent.

## Verify Excel output

Check:

- Formula errors, references, cached results, circular references, and unsupported functions.
- Number/date/currency/percentage formats.
- Table and named-range extents.
- Pivot source ranges and chart series.
- Hidden or protected sheets and external connections.
- Column widths, row heights, clipped headers, and chart overlap in the OfficeCLI render.

Do not claim equivalence with Excel calculation for unsupported functions or external data. Report the exact limitation, then follow common verification.
