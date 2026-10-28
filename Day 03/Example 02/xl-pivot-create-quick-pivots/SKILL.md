---
name: "xl-pivot-create-quick-pivots"
description: Creates a number of PivotTables in Excel from a table, placed side by side on one new sheet with five narrow spacer columns between them. Offers two modes - blank pivots (the user drags fields in themselves) or pre-filled pivots (one row field and one KPI each). Pivots always keep Excel's default names. Use this skill whenever the user asks to create, build, or make pivot tables from an Excel table, for example "create 5 pivots", "make blank pivot tables", "make pivot tables from this table", "build pivots with total sales", even if they never say the word skill. Keeps it simple and adds nothing else; renaming and formatting are left to later skills.
---

# xl-pivot-create-quick-pivots

Create the requested number of PivotTables from an Excel table, in one of two modes:

- **Blank:** empty pivots. The user drags fields into Rows, Columns, Values and Filters themselves.
- **Pre-filled:** each pivot has **one row field** and **one KPI** (one value). Nothing else.

Either way, **leave every name at Excel's default**. Do not rename pivots, value fields, or anything else. A later skill handles naming, formatting, and sorting.

Use real PivotTables only. In Claude in Excel use Office.js (`execute_office_js`). With a workbook file on the desktop use Excel COM (Python/xlwings on Windows). `openpyxl` and `pandas` cannot create real pivots. If real pivots cannot be created here, say so and stop.

## 1. Ask what is missing

Ask only for what the user has not already said, in one short numbered message:

1. **Which table** to use. If not named, list the Excel Tables (and sheets with data) you find and ask which one.
2. **How many pivots** to create.
3. **Blank or pre-filled?** Ask: "Do you want blank pivots that you fill by dragging fields, or pre-filled pivots (one row field and one KPI each)?"
4. **Only if pre-filled: which KPI** (one value), for example Sum of Sales, Average of Price, Count of Orders.

Then wait. If the user names the row fields too, use them. If the user said "blank" or "empty" up front, skip questions 3 and 4.

The KPI is a sum, average, count, min, or max of one column. Unique counts and calculations are not supported by this skill; say so in one line if asked.

## 2. Build

**Both modes**
- **Default names only.** Pivots are named by Excel's own sequence: `PivotTable1`, `PivotTable2`, and so on. Never pass a custom pivot name, never set a custom value-field name. Value fields keep Excel's default caption, such as `Sum of Sales`.
- Create **one new sheet** named `Pivot Tables` (add a number if the name is taken). Never touch existing sheets.
- Start every pivot on **row 4**, side by side, the first in column A.
- Each pivot **starts 7 columns after the previous one**, leaving **five empty spacer columns** between them:
  - Pivot 1 starts in column A, spacer columns C:G
  - Pivot 2 starts in column H, spacer columns J:N
  - Pivot 3 starts in column O, and so on
- Make each spacer column **very narrow, about 4 to 5 points wide**.
- Keep **"Autofit column widths on update" turned ON** for every pivot.
- Use one shared source for all pivots (the same table or range). Never add helper columns to the source.

**Blank mode**
- Create each pivot at its start cell with **no fields added**. Do not add rows, columns, values, or filters.
- An empty pivot shows Excel's placeholder, which is a little wider than 2 columns. The 7-column stride still keeps pivots apart, so no extra adjustment is needed.

**Pre-filled mode**
- Each pivot: **one row field**, **the KPI as its only value**, no column field, no filters, no grand-total changes. Each pivot is 2 columns wide.
- Choose a **different row field for each pivot** (a category or date column with a small number of distinct values, about 2 to 30). Do not use a near-unique ID column. If there are fewer suitable columns than pivots, build as many as make sense and say so.
- For a date row field, use the date column itself and leave Excel's automatic grouping.

## 3. Quick code reference

Office.js (a name argument is required by the API, so pass Excel's default sequence `PivotTable1`, `PivotTable2`, ... in order):
```js
const ws = context.workbook.worksheets.add("Pivot Tables");
const src = context.workbook.tables.getItem("SalesData");

// Blank pivot
const pt = ws.pivotTables.add("PivotTable1", src, ws.getRange("A4"));
pt.layout.autoFormat = true;                       // autofit on update: ON
await context.sync();

// Pre-filled pivot: add these lines after creating the pivot
pt.rowHierarchies.add(pt.hierarchies.getItem("Region"));
const dh = pt.dataHierarchies.add(pt.hierarchies.getItem("Sales"));
await context.sync();
// Sum is the default. For another KPI set summarizeBy, then read dh.name back;
// if it still says "Sum of ...", set it to Excel's default form, e.g. "Average of Price".
// Do NOT use a custom caption such as "Total Sales".
// dh.summarizeBy = Excel.AggregationFunction.average;

ws.getRange("C:G").format.columnWidth = 6;         // spacer, see the width note below
```

COM (xlwings), no `TableName` and no caption, so Excel assigns its defaults:
```python
api = wb.api
ws = api.Worksheets.Add(After=api.Worksheets(api.Worksheets.Count)); ws.Name = "Pivot Tables"
pc = api.PivotCaches().Create(SourceType=1, SourceData="SalesData")   # one cache for all pivots

# Blank pivot
pt = pc.CreatePivotTable(TableDestination=ws.Range("A4"))
pt.HasAutoFormat = True                                               # autofit on update: ON

# Pre-filled pivot: add these lines after creating the pivot
pt.PivotFields("Region").Orientation = 1                              # row field
df = pt.AddDataField(pt.PivotFields("Sales"))                         # default caption, e.g. "Sum of Sales"
# For another KPI set df.Function (-4106 avg, -4112 count, -4136 max, -4139 min),
# then read df.Caption back and make sure it is Excel's default form ("Average of Sales").

ws.Range("C:G").ColumnWidth = 0.6                                     # then check ws.Range("C1").Width (points)
```

**Spacer width note:** the target is about 4 to 5 points per spacer column. In COM, read back `ws.Range("C1").Width` (points) and adjust the character width until it is 4 to 5. In Office.js, `columnWidth` units can differ by host (a default column reads about 64 in pixels or 48 in points), so scale the value so a spacer is the equivalent of 4 to 5 points (about 6 pixels).

## 4. Check and report

Refresh the pivots, then read back and confirm: the number of pivots matches the request, each starts on row 4 at the expected column, the five spacer columns between them are narrow, autofit on update is ON for every pivot, names are Excel's defaults (`PivotTable1`, `PivotTable2`, ...), value fields (pre-filled mode) carry default captions, and there are no errors.

Reply briefly:
- **Blank mode:** the table used, the sheet name, and a short list of pivot name and top-left cell. Tell the user the pivots are empty and ready for them to drag fields into Rows, Columns, Values, and Filters.
- **Pre-filled mode:** the table used, the sheet name, and a short list of pivot name, row field, KPI, and top-left cell.

Do nothing else (no renaming, formatting, sorting, charts, or extra pivots).
