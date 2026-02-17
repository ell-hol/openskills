---
name: xlsx
description: "Create, read, and modify Excel spreadsheets (.xlsx) with openpyxl and pandas. Triggers on Excel, spreadsheet, .xlsx, data analysis, formulas, or charts."
license: MIT
---

# Excel Tools

Work with Excel files using openpyxl for formatting and formulas, pandas for data analysis.

## Creating Workbooks

```python
from openpyxl import Workbook

wb = Workbook()
ws = wb.active  # Get default sheet
ws.title = "Data"

# Create additional sheets
ws2 = wb.create_sheet("Summary")
ws3 = wb.create_sheet("Charts", 0)  # Insert at position

wb.save("workbook.xlsx")
```

## Loading Existing Files

```python
from openpyxl import load_workbook

wb = load_workbook("existing.xlsx")
ws = wb.active

# Access sheet by name
ws = wb["Sheet1"]

# List all sheets
print(wb.sheetnames)
```

### Load Options

```python
# Read values only (ignores formulas)
wb = load_workbook("file.xlsx", data_only=True)

# Read-only mode (memory efficient for large files)
wb = load_workbook("large.xlsx", read_only=True)
```

## Working with Cells

```python
# Direct assignment
ws["A1"] = "Header"
ws["B1"] = 42
ws["C1"] = 3.14

# Using cell method
ws.cell(row=2, column=1, value="Row 2")

# Reading values
value = ws["A1"].value
```

### Accessing Ranges

```python
# Iterate rows
for row in ws.iter_rows(min_row=1, max_row=10, min_col=1, max_col=3):
    for cell in row:
        print(cell.value)

# Get values only
for row in ws.iter_rows(values_only=True):
    print(row)

# Append rows
ws.append(["Name", "Score", "Grade"])
ws.append(["Alice", 95, "A"])
ws.append(["Bob", 87, "B"])
```

## Formulas

Use Excel formulas directly - they calculate when opened in Excel.

```python
ws["A1"] = 100
ws["A2"] = 200
ws["A3"] = "=SUM(A1:A2)"

ws["B1"] = "=AVERAGE(A1:A2)"
ws["C1"] = '=IF(A1>50, "Pass", "Fail")'
ws["D1"] = "=VLOOKUP(A1, Sheet2!A:B, 2, FALSE)"
```

## Formatting

### Fonts

```python
from openpyxl.styles import Font

ws["A1"].font = Font(
    name="Arial",
    size=14,
    bold=True,
    italic=False,
    color="FF0000"  # Red
)
```

### Fill (Background Color)

```python
from openpyxl.styles import PatternFill

ws["A1"].fill = PatternFill(
    start_color="FFFF00",  # Yellow
    end_color="FFFF00",
    fill_type="solid"
)
```

### Alignment

```python
from openpyxl.styles import Alignment

ws["A1"].alignment = Alignment(
    horizontal="center",
    vertical="center",
    wrap_text=True
)
```

### Borders

```python
from openpyxl.styles import Border, Side

thin = Side(style="thin", color="000000")
ws["A1"].border = Border(
    left=thin,
    right=thin,
    top=thin,
    bottom=thin
)
```

### Number Formats

```python
ws["B1"].number_format = "$#,##0.00"      # Currency
ws["C1"].number_format = "0.00%"          # Percentage
ws["D1"].number_format = "YYYY-MM-DD"     # Date
```

## Column & Row Dimensions

```python
# Column width
ws.column_dimensions["A"].width = 20

# Row height
ws.row_dimensions[1].height = 30

# Auto-fit (approximate)
from openpyxl.utils import get_column_letter
for col in range(1, ws.max_column + 1):
    ws.column_dimensions[get_column_letter(col)].width = 15
```

## Merging Cells

```python
ws.merge_cells("A1:C1")
ws["A1"] = "Merged Header"

ws.unmerge_cells("A1:C1")
```

## Charts

```python
from openpyxl.chart import BarChart, Reference

# Data must exist in cells first
data = Reference(ws, min_col=2, min_row=1, max_row=5, max_col=2)
categories = Reference(ws, min_col=1, min_row=2, max_row=5)

chart = BarChart()
chart.title = "Sales Report"
chart.x_axis.title = "Category"
chart.y_axis.title = "Amount"

chart.add_data(data, titles_from_data=True)
chart.set_categories(categories)

ws.add_chart(chart, "E2")  # Position
```

Chart types: `BarChart`, `LineChart`, `PieChart`, `AreaChart`, `ScatterChart`

### Chart Customization

```python
chart = BarChart()
chart.title = "Sales Report"
chart.width = 15  # inches
chart.height = 7.5

# Axis formatting
chart.y_axis.numFmt = '$#,##0'
chart.y_axis.title = "Revenue"
chart.x_axis.title = "Month"

# Multiple series
data1 = Reference(ws, min_col=2, min_row=1, max_row=13)
data2 = Reference(ws, min_col=3, min_row=1, max_row=13)
chart.add_data(data1, titles_from_data=True)
chart.add_data(data2, titles_from_data=True)
```

## Data Validation

```python
from openpyxl.worksheet.datavalidation import DataValidation

# Dropdown list
dv = DataValidation(
    type="list",
    formula1='"Yes,No,Maybe"',
    allow_blank=True
)
dv.add("A1:A100")
ws.add_data_validation(dv)

# Number range
dv = DataValidation(
    type="whole",
    operator="between",
    formula1=1,
    formula2=100
)
dv.add("B1:B100")
ws.add_data_validation(dv)
```

## Using pandas

For data analysis, pandas provides a simpler interface.

```python
import pandas as pd

# Read Excel
df = pd.read_excel("data.xlsx")
df = pd.read_excel("data.xlsx", sheet_name="Sheet2")

# Read all sheets
all_sheets = pd.read_excel("data.xlsx", sheet_name=None)

# Write Excel
df.to_excel("output.xlsx", index=False)

# Multiple sheets
with pd.ExcelWriter("output.xlsx") as writer:
    df1.to_excel(writer, sheet_name="Data", index=False)
    df2.to_excel(writer, sheet_name="Summary", index=False)
```

## Freeze Panes

```python
# Freeze rows above and columns to the left of cell
ws.freeze_panes = "B2"  # Freezes row 1 and column A
ws.freeze_panes = "A2"  # Freezes row 1 only
ws.freeze_panes = "B1"  # Freezes column A only
```

## Print Setup

```python
ws.print_title_rows = '1:1'  # Repeat row 1 on each page
ws.print_area = 'A1:G50'     # Set print area

ws.page_setup.orientation = 'landscape'
ws.page_setup.fitToPage = True
ws.page_setup.fitToWidth = 1
ws.page_setup.fitToHeight = 0  # Auto height
```

## Modifying Existing Files

```python
from openpyxl import load_workbook

wb = load_workbook("existing.xlsx")
ws = wb.active

# Modify cells
ws["A1"] = "Updated Value"

# Insert/delete rows
ws.insert_rows(2)       # Insert at row 2
ws.delete_rows(5, 3)    # Delete 3 rows starting at 5

# Insert/delete columns
ws.insert_cols(3)
ws.delete_cols(2)

wb.save("modified.xlsx")
```

## QA (Required)

**Assume there are problems. Your job is to find them.**

### Formula Verification

After creating or modifying a file with formulas:

1. **Open in Excel/LibreOffice** to verify formulas calculate correctly
2. **Check for formula errors**: `#REF!`, `#DIV/0!`, `#VALUE!`, `#N/A`, `#NAME?`
3. **Test with sample data**: Verify 2-3 references pull correct values

```python
from openpyxl import load_workbook

wb = load_workbook("output.xlsx", data_only=True)
ws = wb.active

# Check for common formula errors
error_types = ['#REF!', '#DIV/0!', '#VALUE!', '#N/A', '#NAME?', '#NULL!']
errors = []
for row in ws.iter_rows():
    for cell in row:
        if cell.value in error_types:
            errors.append(f"{cell.coordinate}: {cell.value}")

if errors:
    print("Formula errors found:")
    for e in errors:
        print(f"  {e}")
```

### Verification Checklist

- [ ] **Column mapping correct**: Excel column 64 = BL, not BK
- [ ] **Row offset correct**: DataFrame row 5 = Excel row 6 (1-indexed)
- [ ] **No NaN values**: Check with `pd.notna()` before writing
- [ ] **Division by zero**: Check denominators in formulas
- [ ] **Cross-sheet references**: Use correct format `Sheet1!A1`
- [ ] **Formatting applied**: Verify colors, fonts, borders render correctly

### Verification Loop

1. Create/modify file
2. Open in Excel and check formulas
3. **List issues found**
4. Fix issues
5. **Re-verify**
6. Repeat until clean

**Do not declare success until you've completed at least one verify cycle.**

---

## Dependencies

- `openpyxl` - Excel file manipulation with formatting
- `pandas` - data analysis and simple I/O

## Notes

- openpyxl uses 1-based indexing for rows and columns
- Formulas are stored as strings; use `data_only=True` to read calculated values
- Cells are created when first accessed
- File extension must match format for Excel to open correctly
