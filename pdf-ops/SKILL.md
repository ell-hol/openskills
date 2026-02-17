---
name: pdf-ops
description: "Use this skill whenever the user wants to do anything with PDF files. This includes reading or extracting text/tables from PDFs, combining or merging multiple PDFs into one, splitting PDFs apart, rotating pages, adding watermarks, creating new PDFs, filling PDF forms, encrypting/decrypting PDFs, extracting images, and OCR on scanned PDFs to make them searchable. If the user mentions a .pdf file or asks to produce one, use this skill."
license: MIT
---

# PDF Processing Guide

## Quick Reference

| Task | Best Tool | Example |
|------|-----------|---------|
| Merge PDFs | pypdf | `writer.add_page(page)` |
| Split PDFs | pypdf | One page per file |
| Extract text | pdfplumber | `page.extract_text()` |
| Extract tables | pdfplumber | `page.extract_tables()` |
| Create PDFs | reportlab | Canvas or Platypus |
| Command line merge | qpdf | `qpdf --empty --pages ...` |
| OCR scanned PDFs | pytesseract | Convert to image first |

---

## Python Libraries

### pypdf - Basic Operations

#### Read PDF and Extract Text
```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("document.pdf")
print(f"Pages: {len(reader.pages)}")

text = ""
for page in reader.pages:
    text += page.extract_text()
```

#### Merge PDFs
```python
from pypdf import PdfWriter, PdfReader

writer = PdfWriter()
for pdf_file in ["doc1.pdf", "doc2.pdf", "doc3.pdf"]:
    reader = PdfReader(pdf_file)
    for page in reader.pages:
        writer.add_page(page)

with open("merged.pdf", "wb") as output:
    writer.write(output)
```

#### Split PDF
```python
reader = PdfReader("input.pdf")
for i, page in enumerate(reader.pages):
    writer = PdfWriter()
    writer.add_page(page)
    with open(f"page_{i+1}.pdf", "wb") as output:
        writer.write(output)
```

#### Rotate Pages
```python
reader = PdfReader("input.pdf")
writer = PdfWriter()

page = reader.pages[0]
page.rotate(90)  # Rotate 90 degrees clockwise
writer.add_page(page)

with open("rotated.pdf", "wb") as output:
    writer.write(output)
```

#### Password Protection
```python
reader = PdfReader("input.pdf")
writer = PdfWriter()

for page in reader.pages:
    writer.add_page(page)

writer.encrypt("userpassword", "ownerpassword")

with open("encrypted.pdf", "wb") as output:
    writer.write(output)
```

---

### pdfplumber - Text and Table Extraction

#### Extract Text with Layout
```python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    for page in pdf.pages:
        text = page.extract_text()
        print(text)
```

#### Extract Tables
```python
import pandas as pd

with pdfplumber.open("document.pdf") as pdf:
    all_tables = []
    for page in pdf.pages:
        tables = page.extract_tables()
        for table in tables:
            if table:
                df = pd.DataFrame(table[1:], columns=table[0])
                all_tables.append(df)

if all_tables:
    combined_df = pd.concat(all_tables, ignore_index=True)
    combined_df.to_excel("extracted_tables.xlsx", index=False)
```

---

### reportlab - Create PDFs

#### Basic PDF Creation
```python
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

c = canvas.Canvas("hello.pdf", pagesize=letter)
width, height = letter

c.drawString(100, height - 100, "Hello World!")
c.drawString(100, height - 120, "This is a PDF created with reportlab")
c.line(100, height - 140, 400, height - 140)
c.save()
```

#### Multi-Page Reports with Platypus
```python
from reportlab.lib.pagesizes import letter
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, PageBreak, Table, TableStyle
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.lib import colors
from reportlab.lib.units import inch

doc = SimpleDocTemplate("report.pdf", pagesize=letter,
                        topMargin=1*inch, bottomMargin=1*inch)
styles = getSampleStyleSheet()
story = []

# Title
story.append(Paragraph("Report Title", styles['Title']))
story.append(Spacer(1, 0.5*inch))

# Body text
story.append(Paragraph("This is the body of the report.", styles['Normal']))
story.append(PageBreak())

# Table
data = [
    ['Product', 'Q1', 'Q2', 'Q3'],
    ['Widgets', '120', '135', '142'],
    ['Gadgets', '85', '92', '98']
]
table = Table(data)
table.setStyle(TableStyle([
    ('BACKGROUND', (0, 0), (-1, 0), colors.grey),
    ('TEXTCOLOR', (0, 0), (-1, 0), colors.whitesmoke),
    ('ALIGN', (0, 0), (-1, -1), 'CENTER'),
    ('GRID', (0, 0), (-1, -1), 1, colors.black)
]))
story.append(table)

doc.build(story)
```

#### Subscripts and Superscripts

**CRITICAL**: Never use Unicode subscript/superscript characters (₀₁₂₃₄₅₆₇₈₉, ⁰¹²³⁴⁵⁶⁷⁸⁹) in ReportLab PDFs. The built-in fonts do not include these glyphs, causing them to render as solid black boxes.

Use ReportLab's XML markup tags instead:
```python
from reportlab.platypus import Paragraph
from reportlab.lib.styles import getSampleStyleSheet

styles = getSampleStyleSheet()

# Subscripts: use <sub> tag
chemical = Paragraph("H<sub>2</sub>O", styles['Normal'])

# Superscripts: use <super> tag
squared = Paragraph("x<super>2</super> + y<super>2</super>", styles['Normal'])
```

#### Page Numbers and Headers
```python
from reportlab.lib.pagesizes import letter

def add_page_number(canvas, doc):
    page_num = canvas.getPageNumber()
    canvas.saveState()
    canvas.setFont('Helvetica', 9)
    canvas.drawRightString(letter[0] - 50, 30, f"Page {page_num}")
    canvas.restoreState()

def add_header(canvas, doc):
    canvas.saveState()
    canvas.setFont('Helvetica', 9)
    canvas.drawString(50, letter[1] - 30, "Document Title")
    canvas.line(50, letter[1] - 35, letter[0] - 50, letter[1] - 35)
    canvas.restoreState()

doc = SimpleDocTemplate("output.pdf", pagesize=letter)
doc.build(story, onFirstPage=add_header, onLaterPages=add_page_number)
```

---

## Command-Line Tools

### pdftotext (poppler-utils)
```bash
# Extract text
pdftotext input.pdf output.txt

# Preserve layout
pdftotext -layout input.pdf output.txt

# Extract specific pages
pdftotext -f 1 -l 5 input.pdf output.txt
```

### qpdf
```bash
# Merge PDFs
qpdf --empty --pages file1.pdf file2.pdf -- merged.pdf

# Split pages
qpdf input.pdf --pages . 1-5 -- pages1-5.pdf

# Rotate pages
qpdf input.pdf output.pdf --rotate=+90:1

# Remove password
qpdf --password=mypassword --decrypt encrypted.pdf decrypted.pdf

# Check/repair PDF
qpdf --check input.pdf
```

### Extract Images
```bash
# Using pdfimages (poppler-utils)
pdfimages -j input.pdf output_prefix
# Creates output_prefix-000.jpg, output_prefix-001.jpg, etc.
```

---

## Common Tasks

### OCR Scanned PDFs
```python
import pytesseract
from pdf2image import convert_from_path

images = convert_from_path('scanned.pdf')

text = ""
for i, image in enumerate(images):
    text += f"Page {i+1}:\n"
    text += pytesseract.image_to_string(image)
    text += "\n\n"

print(text)
```

### Add Watermark
```python
from pypdf import PdfReader, PdfWriter

watermark = PdfReader("watermark.pdf").pages[0]
reader = PdfReader("document.pdf")
writer = PdfWriter()

for page in reader.pages:
    page.merge_page(watermark)
    writer.add_page(page)

with open("watermarked.pdf", "wb") as output:
    writer.write(output)
```

---

## QA (Required)

**Assume there are problems. Your job is to find them.**

Your first render is almost never correct. Approach QA as a bug hunt, not a confirmation step.

### Content QA

Verify PDF was created correctly:
```python
from pypdf import PdfReader

reader = PdfReader("output.pdf")
print(f"Pages: {len(reader.pages)}")

# Extract and check text
for i, page in enumerate(reader.pages):
    text = page.extract_text()
    print(f"Page {i+1}: {len(text)} chars")
    # Check for expected content
    if "expected_text" not in text:
        print(f"WARNING: Missing expected content on page {i+1}")
```

### Visual QA

Convert PDF to images for visual inspection:
```bash
# Using poppler-utils
pdftoppm -jpeg -r 150 output.pdf page
# Creates page-01.jpg, page-02.jpg, etc.
```

Then inspect the images for:
- Text cut off at edges or overflowing boxes
- Overlapping elements
- Missing content or blank pages
- Incorrect fonts or garbled characters (black boxes = Unicode issue)
- Tables with misaligned columns
- Images not rendering correctly

### Verification Loop

1. Generate PDF
2. Extract text and verify content
3. Convert to images and inspect visually
4. **List issues found** (if none, look again more critically)
5. Fix issues
6. **Re-verify** - one fix often creates another problem
7. Repeat until clean

**Do not declare success until you've completed at least one verify cycle.**

---

## Troubleshooting

### Encrypted PDFs
```python
from pypdf import PdfReader

reader = PdfReader("encrypted.pdf")
if reader.is_encrypted:
    reader.decrypt("password")
```

### Text Extraction Returns Empty
```python
# Fallback to OCR for scanned PDFs
import pytesseract
from pdf2image import convert_from_path

def extract_text_with_ocr(pdf_path):
    images = convert_from_path(pdf_path)
    text = ""
    for image in images:
        text += pytesseract.image_to_string(image)
    return text
```

### Corrupted PDFs
```bash
# Check PDF structure
qpdf --check corrupted.pdf

# Attempt repair
qpdf --replace-input corrupted.pdf
```

### Black Boxes Instead of Text
- **Cause**: Unicode subscripts/superscripts (₀₁₂, ⁰¹²)
- **Fix**: Use `<sub>` and `<super>` tags in Paragraph objects
- Also check font embedding and glyph support

### Large PDFs Cause Errors
```python
import sys
sys.setrecursionlimit(5000)  # Increase if needed
```

---

## Dependencies

**Python:**
- `pypdf` - read, write, merge, split
- `pdfplumber` - text and table extraction
- `reportlab` - create PDFs
- `pytesseract` + `pdf2image` - OCR

**System:**
- `poppler-utils` - pdftotext, pdftoppm, pdfimages
- `qpdf` - manipulation and repair
- `tesseract-ocr` - OCR engine

Install:
```bash
pip install pypdf pdfplumber reportlab pdf2image pytesseract
# System packages (Ubuntu/Debian)
apt install poppler-utils qpdf tesseract-ocr
```

## Notes

- Use reportlab for creating new PDFs, pypdf for manipulating existing ones
- Page sizes: `letter` (8.5x11"), `A4` (210x297mm), measured in points (72 per inch)
- Text extraction results depend on PDF structure - some files store text as images
