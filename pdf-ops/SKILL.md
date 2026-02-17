---
name: pdf-ops
description: "Create and manipulate PDF documents: generate reports, research papers, merge/split files, add watermarks, encrypt/decrypt. Triggers when user mentions PDF, .pdf files, creating documents, reports, or PDF operations."
license: MIT
---

# PDF Operations

Create PDFs with reportlab, manipulate with pypdf.

## Creating PDFs with ReportLab

### Basic Document

```python
from reportlab.lib.pagesizes import letter, A4
from reportlab.pdfgen import canvas

c = canvas.Canvas("output.pdf", pagesize=letter)
width, height = letter  # 612 x 792 points

c.setFont("Helvetica-Bold", 24)
c.drawString(100, height - 100, "Document Title")

c.setFont("Helvetica", 12)
c.drawString(100, height - 150, "This is body text.")

c.showPage()  # End page, start new one
c.save()
```

### Using Platypus (High-Level)

```python
from reportlab.lib.pagesizes import letter
from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
from reportlab.lib.units import inch
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, PageBreak
from reportlab.platypus import Table, TableStyle
from reportlab.lib import colors

doc = SimpleDocTemplate("report.pdf", pagesize=letter,
                        topMargin=1*inch, bottomMargin=1*inch)
styles = getSampleStyleSheet()
story = []

# Title
story.append(Paragraph("Research Paper Title", styles['Title']))
story.append(Spacer(1, 0.5*inch))

# Headings and paragraphs
story.append(Paragraph("1. Introduction", styles['Heading1']))
story.append(Paragraph("This is the introduction text...", styles['Normal']))
story.append(Spacer(1, 0.25*inch))

# Subheading
story.append(Paragraph("1.1 Background", styles['Heading2']))
story.append(Paragraph("Background information here...", styles['Normal']))

doc.build(story)
```

### Custom Styles

`getSampleStyleSheet()` includes pre-defined styles: `Title`, `Heading1`-`Heading6`, `Normal`, `BodyText`, `Italic`, `Code`, `Bullet`. Use unique names for custom styles to avoid conflicts.

```python
from reportlab.lib.styles import ParagraphStyle
from reportlab.lib.enums import TA_CENTER, TA_JUSTIFY

# Use unique names - don't reuse 'BodyText', 'Normal', etc.
custom_body = ParagraphStyle(
    'CustomBody',              # Unique name
    parent=styles['Normal'],
    fontSize=11,
    leading=14,                # Line height
    alignment=TA_JUSTIFY,
    spaceAfter=12,
    firstLineIndent=20
)
styles.add(custom_body)

custom_title = ParagraphStyle(
    'CustomTitle',             # Unique name
    parent=styles['Title'],
    alignment=TA_CENTER,
    fontSize=24,
    spaceAfter=30
)
styles.add(custom_title)
```

### Tables

```python
from reportlab.platypus import Table, TableStyle
from reportlab.lib import colors

data = [
    ['Header 1', 'Header 2', 'Header 3'],
    ['Row 1', 'Data', 'Data'],
    ['Row 2', 'Data', 'Data'],
]

table = Table(data, colWidths=[2*inch, 2*inch, 2*inch])
table.setStyle(TableStyle([
    ('BACKGROUND', (0, 0), (-1, 0), colors.grey),
    ('TEXTCOLOR', (0, 0), (-1, 0), colors.whitesmoke),
    ('ALIGN', (0, 0), (-1, -1), 'CENTER'),
    ('FONTNAME', (0, 0), (-1, 0), 'Helvetica-Bold'),
    ('FONTSIZE', (0, 0), (-1, 0), 12),
    ('BOTTOMPADDING', (0, 0), (-1, 0), 12),
    ('BACKGROUND', (0, 1), (-1, -1), colors.beige),
    ('GRID', (0, 0), (-1, -1), 1, colors.black),
]))
story.append(table)
```

### Images

```python
from reportlab.platypus import Image

img = Image("chart.png", width=4*inch, height=3*inch)
story.append(img)
```

### Page Numbers and Headers

```python
from reportlab.platypus import SimpleDocTemplate
from reportlab.lib.pagesizes import letter

def add_page_number(canvas, doc):
    page_num = canvas.getPageNumber()
    text = f"Page {page_num}"
    canvas.saveState()
    canvas.setFont('Helvetica', 9)
    canvas.drawRightString(letter[0] - 50, 30, text)
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

### Mathematical Notation

```python
# Use subscripts and superscripts
story.append(Paragraph("E = mc<super>2</super>", styles['Normal']))
story.append(Paragraph("H<sub>2</sub>O", styles['Normal']))

# For complex math, use images or the reportlab.graphics module
```

### Multi-Column Layout

```python
from reportlab.platypus import Frame, PageTemplate, BaseDocTemplate

frame1 = Frame(doc.leftMargin, doc.bottomMargin,
               doc.width/2-6, doc.height, id='col1')
frame2 = Frame(doc.leftMargin+doc.width/2+6, doc.bottomMargin,
               doc.width/2-6, doc.height, id='col2')

doc.addPageTemplates([PageTemplate(id='TwoCol', frames=[frame1, frame2])])
```

---

## Manipulating PDFs with pypdf

### Core Classes

- `PdfReader` - read and extract from PDFs
- `PdfWriter` - modify and save PDFs

## Reading & Text Extraction

```python
from pypdf import PdfReader

reader = PdfReader("document.pdf")

# Basic text extraction
for page in reader.pages:
    text = page.extract_text()

# Layout-preserving extraction
text = page.extract_text(extraction_mode="layout")

# Remove excess vertical whitespace
text = page.extract_text(
    extraction_mode="layout",
    layout_mode_space_vertically=False
)
```

## Merging PDFs

```python
from pypdf import PdfWriter

writer = PdfWriter()

# Append entire documents
for pdf_path in ["doc1.pdf", "doc2.pdf", "doc3.pdf"]:
    writer.append(pdf_path)

writer.write("merged.pdf")
```

### Selective Merging

```python
from pypdf import PdfWriter, PdfReader

writer = PdfWriter()

# Add specific page ranges (0-indexed)
writer.append("document.pdf", pages=(0, 5))  # First 5 pages

# Insert at specific position
writer.merge(position=2, fileobj="insert.pdf", pages=(0, 1))

writer.write("result.pdf")
```

## Splitting PDFs

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("input.pdf")

for i, page in enumerate(reader.pages):
    writer = PdfWriter()
    writer.add_page(page)
    writer.write(f"page_{i + 1}.pdf")
```

## Rotating Pages

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("input.pdf")
writer = PdfWriter()

for page in reader.pages:
    page.rotate(90)  # Clockwise degrees: 90, 180, 270
    writer.add_page(page)

writer.write("rotated.pdf")
```

## Watermarks & Stamps

The difference: watermarks go behind content (`over=False`), stamps go on top (`over=True`).

```python
from pypdf import PdfReader, PdfWriter

stamp = PdfReader("watermark.pdf").pages[0]
writer = PdfWriter(clone_from="content.pdf")

for page in writer.pages:
    page.merge_page(stamp, over=False)  # Watermark (behind)
    # page.merge_page(stamp, over=True)   # Stamp (on top)

writer.write("watermarked.pdf")
```

### With Transformations

```python
from pypdf import PdfReader, PdfWriter, Transformation

stamp = PdfReader("stamp.pdf").pages[0]
writer = PdfWriter(clone_from="content.pdf")

for page in writer.pages:
    page.merge_transformed_page(
        stamp,
        Transformation().scale(0.5).translate(tx=100, ty=100)
    )

writer.write("stamped.pdf")
```

## Encryption & Decryption

```python
from pypdf import PdfReader, PdfWriter

# Encrypt
reader = PdfReader("input.pdf")
writer = PdfWriter(clone_from=reader)
writer.encrypt("password", algorithm="AES-256")
writer.write("encrypted.pdf")

# Decrypt
reader = PdfReader("encrypted.pdf")
if reader.is_encrypted:
    reader.decrypt("password")
```

Supported algorithms: `RC4-40`, `RC4-128`, `AES-128`, `AES-256-R5`, `AES-256`

## Metadata

```python
from pypdf import PdfReader

reader = PdfReader("document.pdf")
info = reader.metadata

if info:
    print(info.title)
    print(info.author)
    print(info.creation_date)
```

## Image Extraction

```python
from pypdf import PdfReader

reader = PdfReader("document.pdf")

for page in reader.pages:
    for image in page.images:
        with open(image.name, "wb") as f:
            f.write(image.data)
```

## Dependencies

- `reportlab` - PDF creation from scratch
- `pypdf` - PDF manipulation (merge, split, encrypt)
- `cryptography` or `pycryptodome` - required for AES encryption

## Notes

- Use reportlab for creating new PDFs, pypdf for manipulating existing ones
- pypdf cannot perform OCR - use `pytesseract` for scanned documents
- Large PDFs may require increased recursion limit: `sys.setrecursionlimit(n)`
- Text extraction results depend on PDF structure - some files store text as images
- Page sizes: `letter` (8.5x11"), `A4` (210x297mm), measured in points (72 per inch)
