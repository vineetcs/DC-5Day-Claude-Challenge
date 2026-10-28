---
name: xl-pivot-format
description: Standardizes all pivot tables on a sheet: renames the sheet, renames each pivot as PT_FieldName, formats large sale/value numbers as millions with 1 decimal (no currency), and sorts descending (except time-based pivots).
---

## When to use
Trigger when the user says "format the pivots", "run xlFormat_Pivots", "/xl-pivot-format", or points at a sheet full of pivot tables and asks to clean it up / standardize it. Works on the active sheet unless the user names another sheet.

## Step 1 — Identify all pivot tables on the target sheet
Use execute_office_js:
```js
const sheet = context.workbook.worksheets.getItem(sheetName);
const pts = sheet.pivotTables;
pts.load("items/name");
await context.sync();
const info = [];
for (const pt of pts.items) {
  const loc = pt.layout.getRange();
  loc.load("address,values");
  info.push({pt, loc});
}
await context.sync();
return info.map(r => ({name: r.pt.name, address: r.loc.address, values: r.loc.values}));
```
From each pivot's top-left corner (e.g. "Row Labels" header row) and its row labels, determine:
- The row-field name (e.g. Country, Salesman, Customer_Type, Product) — read the pivot's rowHierarchies via pt.rowHierarchies.load("items/name") if the layout values alone are ambiguous.
- Whether it is time-based (row labels are years/quarters/months/dates, e.g. "2023", "Qtr1", month names) — these are exempt from sorting.
- Whether the value column contains "big numbers" — treat as big if the Grand Total (or max value) is at least 100,000. Only reformat columns that qualify; leave small-number pivots (e.g. counts, percentages, small averages) in their original format.

Done when: you have a list of pivotName, address, rowField, isTimeBased, isBigNumber for every pivot table on the sheet.

## Step 2 — Rename the sheet
Ask/confirm only if the user hasn't already named the target sheet in the request. Otherwise pick a descriptive name like "Pivot Analysis" or reuse what the user specified in this call. Use sheet.name = "..." via execute_office_js (rename fails if the name is taken — check context.workbook.worksheets first and disambiguate if needed).

## Step 3 — Rename each pivot table
For each pivot, rename to PT_ followed by the row-field name found in Step 1, in PascalCase with no spaces or underscores inside the field name itself (e.g. PT_CustomerType, PT_Salesman, PT_Country). For time-based pivots, use PT_TimeSeries or PT_Year / PT_Quarter depending on the finest grouping — ask the user if genuinely ambiguous, otherwise default to PT_TimeSeries.
```js
pt.name = "PT_Country";
```
Done when: pts.load("items/name") read back shows every pivot renamed, no two pivots share a name.

## Step 4 — Format big numbers as millions, 1 decimal, no currency
For each pivot flagged isBigNumber in Step 1, apply number format #,##0.0,,"M" (no dollar sign or currency symbol) to the pivot's value column, covering data rows AND the grand-total row (and any subtotal rows for multi-level pivots). Use execute_office_js range.numberFormat, e.g.:
```js
sheet.getRange("B4:B8").numberFormat = [["#,##0.0,,\"M\""], ["#,##0.0,,\"M\""], ["#,##0.0,,\"M\""], ["#,##0.0,,\"M\""], ["#,##0.0,,\"M\""]];
```
Prefer setting numberFormat on the whole value-column range (from first data row through grand total) in one call rather than cell-by-cell — build the 2D array to match the row count. Do NOT touch row-label columns or headers.
Done when: a read-back of the value cells' numberFormat/text shows values rendered like "37.8M", "178.5M" (not raw numbers, not currency-prefixed).

## Step 5 — Sort descending (skip time-based pivots)
For every pivot NOT flagged isTimeBased, sort its row field descending by the value field.

Try the native pivot sort first: pivotTable.rowHierarchies.getItem(rowField) paired with the corresponding PivotField's sortByValues/sortByLabels methods where available in the current Excel JS API. If that throws ApiNotFound or isn't available on this host:
- **Fallback**: freeze that pivot to static values with copy_paste_range (copyType: "values") into a clean block, then apply a normal range sort (range.sort.apply, descending on the value column) to that static block. Only do this after the user confirms they're OK converting that specific pivot to static values — otherwise leave the live pivot as-is and tell the user it needs a manual "Sort Largest to Smallest" click on the value field's dropdown in Excel.
Done when: either (a) the pivot's row order is verified descending by reading back the value column top-to-bottom, or (b) you've explicitly told the user which pivots still need a manual sort click because the API path wasn't available.

## Step 6 — Verify and report
Read back sheet name, all pivot names (pts.load("items/name")), the formatted value ranges (check displayed text, not just values), and the row order of each non-time pivot. Report exactly what changed and flag anything you could not verify (e.g. sort order if you had to leave a live pivot for manual sorting).

## Always confirm before executing
Per the standard workflow: list out the concrete rename/format/sort plan for the specific pivots found on the sheet, and wait for the user's go-ahead before making changes — unless they've already approved this exact plan in the same conversation.
