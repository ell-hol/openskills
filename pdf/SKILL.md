---
name: pdf
description: "Work with PDF files: create professional documents with ReportLab or LaTeX, merge/split/rotate pages, extract text and tables, add watermarks, fill forms, run OCR on scans, and encrypt/decrypt. Trigger on any .pdf mention or PDF-related task."
license: MIT
---

# PDF Skill

Create, manipulate, and extract content from PDF files using Python and command-line tools.

## Tools Overview

| Operation | Library | Notes |
|-----------|---------|-------|
| Create documents | reportlab | Programmatic PDF generation |
| Create documents | LaTeX | Publication-quality typesetting |
| Manipulate pages | pypdf | Merge, split, rotate, encrypt |
| Extract text | pdfplumber | Layout-aware extraction |
| Extract tables | pdfplumber | Structured data extraction |
| OCR scans | pytesseract | Requires pdf2image |
| CLI operations | qpdf, poppler | System utilities |

---

## Design Ideas

**Don't create plain PDFs.** Black text on white background with no visual hierarchy looks unprofessional. Apply these principles for every document.

### Before Starting

- **Pick a color palette that matches the content**: A financial report needs different colors than a creative brief. Generic blue is lazy.
- **Establish visual hierarchy**: Readers should instantly see what's important. Use size, weight, and color to guide attention.
- **Commit to consistency**: Same fonts, same spacing, same color usage throughout. Inconsistency looks amateur.
- **Leave breathing room**: Dense walls of text are hard to read. Use margins, spacing, and whitespace deliberately.

### Color Palettes

Choose colors that match your document's purpose:

| Theme | Primary | Secondary | Accent | Use Case |
|-------|---------|-----------|--------|----------|
| **Corporate Navy** | `1a365d` (navy) | `2c5282` (blue) | `ed8936` (orange) | Business reports, proposals |
| **Legal Slate** | `2d3748` (charcoal) | `4a5568` (slate) | `c53030` (red) | Contracts, legal docs |
| **Finance Green** | `276749` (forest) | `38a169` (green) | `2b6cb0` (blue) | Financial reports |
| **Medical Teal** | `234e52` (teal) | `319795` (cyan) | `2c7a7b` (seafoam) | Healthcare, research |
| **Academic Burgundy** | `702459` (burgundy) | `97266d` (wine) | `2d3748` (charcoal) | Papers, dissertations |
| **Tech Indigo** | `3c366b` (indigo) | `5a67d8` (purple) | `48bb78` (green) | Tech docs, APIs |
| **Warm Executive** | `744210` (brown) | `975a16` (gold) | `2d3748` (charcoal) | Executive summaries |
| **Minimal Gray** | `1a202c` (black) | `718096` (gray) | `e53e3e` (red) | Clean, modern docs |

### Typography

**Choose fonts deliberately** — don't default to Times New Roman or Arial.

| Document Type | Header Font | Body Font |
|---------------|-------------|-----------|
| Business/Corporate | Helvetica Bold | Helvetica |
| Legal/Formal | Georgia Bold | Georgia |
| Technical/Code | Consolas Bold | Palatino |
| Academic | Palatino Bold | Palatino |
| Modern/Minimal | Helvetica Neue | Helvetica Neue Light |
| Creative | Futura | Garamond |

**Size hierarchy:**

| Element | Size | Weight |
|---------|------|--------|
| Document title | 24-28pt | Bold |
| Section headers | 16-18pt | Bold |
| Subsection headers | 12-14pt | Bold |
| Body text | 10-11pt | Regular |
| Captions/footnotes | 8-9pt | Regular or Italic |
| Table headers | 10pt | Bold |
| Table body | 9-10pt | Regular |

### Layout Principles

**Margins:**
- Minimum 0.75" on all sides (1" preferred for formal documents)
- Add extra inner margin (0.25") for bound documents

**Spacing:**
- 6-12pt space after paragraphs
- 18-24pt space before section headers
- 1.15-1.5 line spacing for body text (never single-spaced for long documents)

**Visual elements:**
- Use colored header bars or sidebar accents
- Add subtle horizontal rules between major sections
- Include page numbers and document title in headers/footers

### Table Styling

**Never use plain black grid tables.** Apply these patterns:

```python
from reportlab.lib import colors
from reportlab.platypus import Table, TableStyle

# Professional table style
professional_style = TableStyle([
    # Header row
    ('BACKGROUND', (0, 0), (-1, 0), colors.HexColor('#1a365d')),
    ('TEXTCOLOR', (0, 0), (-1, 0), colors.white),
    ('FONTNAME', (0, 0), (-1, 0), 'Helvetica-Bold'),
    ('FONTSIZE', (0, 0), (-1, 0), 10),
    ('BOTTOMPADDING', (0, 0), (-1, 0), 8),
    ('TOPPADDING', (0, 0), (-1, 0), 8),

    # Alternating row colors
    ('BACKGROUND', (0, 1), (-1, -1), colors.HexColor('#f7fafc')),
    ('ROWBACKGROUNDS', (0, 1), (-1, -1), [colors.HexColor('#f7fafc'), colors.white]),

    # Body formatting
    ('FONTNAME', (0, 1), (-1, -1), 'Helvetica'),
    ('FONTSIZE', (0, 1), (-1, -1), 9),
    ('BOTTOMPADDING', (0, 1), (-1, -1), 6),
    ('TOPPADDING', (0, 1), (-1, -1), 6),

    # Subtle grid
    ('LINEBELOW', (0, 0), (-1, 0), 1, colors.HexColor('#2c5282')),
    ('LINEBELOW', (0, 1), (-1, -2), 0.5, colors.HexColor('#e2e8f0')),
    ('LINEBELOW', (0, -1), (-1, -1), 1, colors.HexColor('#cbd5e0')),

    # Alignment
    ('ALIGN', (0, 0), (-1, -1), 'LEFT'),
    ('VALIGN', (0, 0), (-1, -1), 'MIDDLE'),
])
```

### Common Mistakes to Avoid

- **Don't use pure black (#000000)** — use dark gray (#1a202c or #2d3748) for softer appearance
- **Don't center body text** — left-align paragraphs; center only titles
- **Don't use full-width tables** — add margins; tables shouldn't touch page edges
- **Don't skip header rows** — tables need clear headers with distinct styling
- **Don't use thin fonts for headers** — headers need bold weight to establish hierarchy
- **Don't mix too many fonts** — maximum 2 font families per document
- **Don't ignore whitespace** — cramped documents are hard to read
- **Don't use bright colors for large areas** — bright colors for accents only

---

## LaTeX for Professional PDFs

LaTeX produces publication-quality PDFs. Use it for reports, papers, technical documentation, and any document requiring precise typography.

### Basic Document

```latex
\documentclass[11pt, a4paper]{article}
\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{geometry}
\usepackage{hyperref}

\geometry{margin=1in}

\title{Document Title}
\author{Author Name}
\date{\today}

\begin{document}

\maketitle

\section{Introduction}
Your content here.

\section{Methods}
More content.

\end{document}
```

Compile with:
```bash
pdflatex document.tex
```

### Professional Document Classes

```latex
% For reports with chapters
\documentclass[11pt]{report}

% For books
\documentclass[11pt]{book}

% KOMA-Script (modern, flexible)
\documentclass[11pt, a4paper, parskip=half]{scrartcl}

% Memoir (highly customizable)
\documentclass[11pt, oneside]{memoir}
```

### Typography and Fonts

```latex
\usepackage[T1]{fontenc}

% Professional font packages
\usepackage{palatino}           % Palatino (elegant, readable)
\usepackage{mathpazo}           % Palatino with matching math
\usepackage{libertine}          % Linux Libertine (open source)
\usepackage{charter}            % Charter (clean, modern)
\usepackage{helvet}             % Helvetica
\usepackage{lmodern}            % Latin Modern (default improvement)

% Or use system fonts with XeLaTeX/LuaLaTeX
\usepackage{fontspec}
\setmainfont{Helvetica Neue}
\setsansfont{Helvetica Neue}
\setmonofont{Menlo}
```

### Colors and Styling

```latex
\usepackage{xcolor}

% Define color palette
\definecolor{primary}{HTML}{1a365d}
\definecolor{secondary}{HTML}{2c5282}
\definecolor{accent}{HTML}{ed8936}
\definecolor{lightgray}{HTML}{f7fafc}

% Colored section headers
\usepackage{titlesec}
\titleformat{\section}
  {\normalfont\Large\bfseries\color{primary}}
  {\thesection}{1em}{}

% Colored hyperlinks
\usepackage{hyperref}
\hypersetup{
    colorlinks=true,
    linkcolor=secondary,
    urlcolor=accent,
    citecolor=primary
}
```

### Professional Tables

```latex
\usepackage{booktabs}
\usepackage{array}
\usepackage{colortbl}

\begin{table}[h]
\centering
\rowcolors{2}{lightgray}{white}
\begin{tabular}{@{} lrr @{}}
\toprule
\rowcolor{primary}
\textcolor{white}{\textbf{Product}} &
\textcolor{white}{\textbf{Q1}} &
\textcolor{white}{\textbf{Q2}} \\
\midrule
Widgets & 120 & 135 \\
Gadgets & 85 & 92 \\
Sprockets & 200 & 218 \\
\bottomrule
\end{tabular}
\caption{Quarterly Sales}
\end{table}
```

**Key packages:**
- `booktabs` — professional table rules (\toprule, \midrule, \bottomrule)
- `colortbl` — colored rows and cells
- `array` — enhanced column formatting

### Headers and Footers

```latex
\usepackage{fancyhdr}
\pagestyle{fancy}

\fancyhf{}  % Clear defaults
\fancyhead[L]{\textcolor{secondary}{\leftmark}}
\fancyhead[R]{\textcolor{secondary}{Company Name}}
\fancyfoot[C]{\thepage}
\renewcommand{\headrulewidth}{0.4pt}
\renewcommand{\headrule}{\hbox to\headwidth{\color{secondary}\leaders\hrule height \headrulewidth\hfill}}
```

### Title Page

```latex
\begin{titlepage}
\centering
\vspace*{2cm}
{\Huge\bfseries\color{primary} Document Title\par}
\vspace{1cm}
{\Large\color{secondary} Subtitle or Description\par}
\vspace{2cm}
{\large Author Name\par}
{\large\itshape Organization\par}
\vfill
{\large \today\par}
\end{titlepage}
```

### Code Listings

```latex
\usepackage{listings}
\usepackage{inconsolata}  % Nice monospace font

\lstset{
    basicstyle=\ttfamily\small,
    backgroundcolor=\color{lightgray},
    frame=single,
    framerule=0pt,
    numbers=left,
    numberstyle=\tiny\color{gray},
    keywordstyle=\color{primary}\bfseries,
    commentstyle=\color{secondary}\itshape,
    stringstyle=\color{accent},
    breaklines=true,
    tabsize=2
}

\begin{lstlisting}[language=Python]
def hello():
    print("Hello, World!")
\end{lstlisting}
```

### Compiling LaTeX

```bash
# Standard compilation
pdflatex document.tex

# With bibliography
pdflatex document.tex
bibtex document
pdflatex document.tex
pdflatex document.tex

# XeLaTeX (for system fonts)
xelatex document.tex

# LuaLaTeX (modern engine)
lualatex document.tex

# Automatic dependency handling
latexmk -pdf document.tex
```

### Quick LaTeX Templates

**Business Report:**
```latex
\documentclass[11pt]{scrartcl}
\usepackage[margin=1in]{geometry}
\usepackage{palatino, microtype, booktabs, xcolor, graphicx}
\usepackage[colorlinks, linkcolor=blue!60!black]{hyperref}

\definecolor{header}{HTML}{1a365d}
\usepackage{titlesec}
\titleformat{\section}{\Large\bfseries\color{header}}{\thesection}{1em}{}

\begin{document}
\title{\color{header}Quarterly Report}
\author{Finance Team}
\date{Q4 2024}
\maketitle
\tableofcontents
\newpage
\section{Executive Summary}
Content here.
\end{document}
```

**Technical Documentation:**
```latex
\documentclass[11pt]{scrartcl}
\usepackage[margin=1in]{geometry}
\usepackage{charter, inconsolata, listings, xcolor, booktabs}
\usepackage[colorlinks]{hyperref}

\definecolor{codebg}{HTML}{f7fafc}
\lstset{basicstyle=\ttfamily\small, backgroundcolor=\color{codebg}, frame=single, framerule=0pt}

\begin{document}
\title{API Documentation}
\author{Engineering Team}
\maketitle
\section{Getting Started}
\begin{lstlisting}
pip install mypackage
\end{lstlisting}
\end{document}
```

---

## Creating PDFs with ReportLab

ReportLab provides two approaches: low-level Canvas for precise control, and Platypus for flowing document layouts.

### Professional Document with Platypus

```python
from reportlab.lib.pagesizes import letter
from reportlab.lib.units import inch
from reportlab.lib import colors
from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
from reportlab.lib.enums import TA_CENTER, TA_JUSTIFY
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, Table, TableStyle

# Color palette
NAVY = colors.HexColor('#1a365d')
SLATE = colors.HexColor('#2c5282')
LIGHT = colors.HexColor('#f7fafc')
DARK_TEXT = colors.HexColor('#2d3748')

# Set up styles
base_styles = getSampleStyleSheet()

title_style = ParagraphStyle('DocTitle',
    parent=base_styles['Title'],
    fontSize=28, textColor=NAVY,
    spaceAfter=30, alignment=TA_CENTER)

heading_style = ParagraphStyle('DocHeading',
    parent=base_styles['Heading1'],
    fontSize=16, textColor=NAVY,
    spaceBefore=20, spaceAfter=10)

body_style = ParagraphStyle('DocBody',
    parent=base_styles['Normal'],
    fontSize=10, textColor=DARK_TEXT,
    leading=14, alignment=TA_JUSTIFY,
    spaceBefore=6, spaceAfter=6)

# Build the document
pdf = SimpleDocTemplate("report.pdf", pagesize=letter,
    topMargin=inch, bottomMargin=inch,
    leftMargin=inch, rightMargin=inch)

content = []
content.append(Paragraph("Quarterly Report", title_style))
content.append(Paragraph("Q4 2024 Summary", body_style))
content.append(Spacer(1, 0.5*inch))

content.append(Paragraph("Performance Overview", heading_style))
content.append(Paragraph(
    "Revenue grew 15% year-over-year with strong enterprise adoption. "
    "Customer retention improved while acquisition costs decreased.",
    body_style))

# Data table
metrics = [
    ['Metric', 'Q3', 'Q4', 'Change'],
    ['Revenue', '$2.4M', '$2.8M', '+16.7%'],
    ['Users', '1,240', '1,485', '+19.8%'],
    ['Churn', '4.2%', '3.1%', '-26.2%'],
]

tbl = Table(metrics, colWidths=[2*inch, 1.2*inch, 1.2*inch, inch])
tbl.setStyle(TableStyle([
    ('BACKGROUND', (0, 0), (-1, 0), NAVY),
    ('TEXTCOLOR', (0, 0), (-1, 0), colors.white),
    ('FONTNAME', (0, 0), (-1, 0), 'Helvetica-Bold'),
    ('ROWBACKGROUNDS', (0, 1), (-1, -1), [colors.white, LIGHT]),
    ('FONTNAME', (0, 1), (-1, -1), 'Helvetica'),
    ('FONTSIZE', (0, 0), (-1, -1), 9),
    ('ALIGN', (1, 0), (-1, -1), 'CENTER'),
    ('VALIGN', (0, 0), (-1, -1), 'MIDDLE'),
    ('TOPPADDING', (0, 0), (-1, -1), 8),
    ('BOTTOMPADDING', (0, 0), (-1, -1), 8),
    ('LINEBELOW', (0, 0), (-1, 0), 2, SLATE),
]))

content.append(Spacer(1, 0.3*inch))
content.append(tbl)

pdf.build(content)
```

### Canvas for Precise Layout

```python
from reportlab.pdfgen import canvas
from reportlab.lib.pagesizes import letter
from reportlab.lib import colors

c = canvas.Canvas("letterhead.pdf", pagesize=letter)
w, h = letter

# Header band
c.setFillColor(colors.HexColor('#1a365d'))
c.rect(0, h - 80, w, 80, fill=True, stroke=False)

# Header text
c.setFillColor(colors.white)
c.setFont('Helvetica-Bold', 24)
c.drawString(72, h - 50, "Company Name")

# Body content
c.setFillColor(colors.HexColor('#2d3748'))
c.setFont('Helvetica', 11)
c.drawString(72, h - 130, "Document content starts here.")

c.save()
```

### Advanced Features

**Horizontal dividers:**
```python
from reportlab.platypus import HRFlowable
content.append(HRFlowable(width="100%", thickness=0.5, color=colors.HexColor('#e2e8f0')))
```

**Custom paragraph styles:**
```python
# Block quote with indentation
quote_style = ParagraphStyle('Quote',
    fontSize=10.5, leading=16,
    leftIndent=30, rightIndent=30,
    alignment=TA_JUSTIFY)

# References with hanging indent
ref_style = ParagraphStyle('Reference',
    fontSize=10, leftIndent=20,
    firstLineIndent=-20)

# Centered equation
equation_style = ParagraphStyle('Equation',
    fontSize=12, alignment=TA_CENTER,
    fontName='Helvetica-Oblique',
    spaceBefore=8, spaceAfter=8)
```

**Page numbering (Page X of Y):**
```python
from reportlab.pdfgen import canvas

class PageNumberCanvas(canvas.Canvas):
    def __init__(self, *args, **kwargs):
        canvas.Canvas.__init__(self, *args, **kwargs)
        self.pages = []

    def showPage(self):
        self.pages.append(dict(self.__dict__))
        self._startPage()

    def save(self):
        total = len(self.pages)
        for idx, page_state in enumerate(self.pages):
            self.__dict__.update(page_state)
            self.setFont("Helvetica", 9)
            self.drawCentredString(letter[0]/2, 36, f"Page {idx + 1} of {total}")
            canvas.Canvas.showPage(self)
        canvas.Canvas.save(self)

# Usage: pdf.build(content, canvasmaker=PageNumberCanvas)
```

**Subscripts and superscripts:**
```python
# Use XML-style tags in Paragraph text
Paragraph("Water: H<sub>2</sub>O", body_style)
Paragraph("Area: r<super>2</super>", body_style)
```

**Warning:** ReportLab's built-in fonts lack Unicode subscript/superscript glyphs (₂, ²). These render as black boxes. Always use `<sub>` and `<super>` tags instead.

---

## Manipulating PDFs with pypdf

### Reading PDF Content

```python
from pypdf import PdfReader

doc = PdfReader("input.pdf")
print(f"Total pages: {len(doc.pages)}")

full_text = ""
for pg in doc.pages:
    full_text += pg.extract_text() or ""
```

### Combining Multiple Files

```python
from pypdf import PdfReader, PdfWriter

output = PdfWriter()

for filename in ["part1.pdf", "part2.pdf", "part3.pdf"]:
    src = PdfReader(filename)
    for pg in src.pages:
        output.add_page(pg)

with open("combined.pdf", "wb") as f:
    output.write(f)
```

### Extracting Page Ranges

```python
from pypdf import PdfReader, PdfWriter

src = PdfReader("large_document.pdf")

# Extract pages 5-10 (0-indexed: 4-9)
subset = PdfWriter()
for i in range(4, 10):
    subset.add_page(src.pages[i])

with open("pages_5_to_10.pdf", "wb") as f:
    subset.write(f)
```

### Single Page Per File

```python
from pypdf import PdfReader, PdfWriter

src = PdfReader("multipage.pdf")

for idx, pg in enumerate(src.pages, start=1):
    single = PdfWriter()
    single.add_page(pg)
    with open(f"page_{idx:03d}.pdf", "wb") as f:
        single.write(f)
```

### Rotating Pages

```python
from pypdf import PdfReader, PdfWriter

src = PdfReader("landscape.pdf")
out = PdfWriter()

for pg in src.pages:
    pg.rotate(90)  # Clockwise: 90, 180, 270
    out.add_page(pg)

with open("portrait.pdf", "wb") as f:
    out.write(f)
```

### Adding Password Protection

```python
from pypdf import PdfReader, PdfWriter

src = PdfReader("confidential.pdf")
protected = PdfWriter()

for pg in src.pages:
    protected.add_page(pg)

protected.encrypt(user_password="read123", owner_password="admin456")

with open("secured.pdf", "wb") as f:
    protected.write(f)
```

### Overlaying Watermarks

```python
from pypdf import PdfReader, PdfWriter

watermark_page = PdfReader("watermark.pdf").pages[0]
src = PdfReader("document.pdf")
out = PdfWriter()

for pg in src.pages:
    pg.merge_page(watermark_page)
    out.add_page(pg)

with open("watermarked.pdf", "wb") as f:
    out.write(f)
```

### Working with Encrypted Files

```python
from pypdf import PdfReader

doc = PdfReader("protected.pdf")
if doc.is_encrypted:
    doc.decrypt("password123")

# Now read normally
print(doc.pages[0].extract_text())
```

---

## Extracting Data with pdfplumber

pdfplumber excels at layout-aware text extraction and table detection.

### Basic Text Extraction

```python
import pdfplumber

with pdfplumber.open("report.pdf") as doc:
    for pg in doc.pages:
        txt = pg.extract_text()
        if txt:
            print(txt)
```

### Table Extraction to DataFrame

```python
import pdfplumber
import pandas as pd

with pdfplumber.open("financial_report.pdf") as doc:
    all_data = []

    for pg in doc.pages:
        tbls = pg.extract_tables()
        for tbl in tbls:
            if tbl and len(tbl) > 1:
                headers = tbl[0]
                rows = tbl[1:]
                df = pd.DataFrame(rows, columns=headers)
                all_data.append(df)

    if all_data:
        combined = pd.concat(all_data, ignore_index=True)
        combined.to_excel("extracted_data.xlsx", index=False)
```

---

## OCR for Scanned Documents

When PDFs contain images instead of text, use OCR.

```python
import pytesseract
from pdf2image import convert_from_path

pages = convert_from_path("scanned_document.pdf", dpi=300)

extracted = []
for idx, img in enumerate(pages, start=1):
    txt = pytesseract.image_to_string(img)
    extracted.append(f"=== Page {idx} ===\n{txt}")

full_text = "\n\n".join(extracted)
print(full_text)
```

---

## Command-Line Utilities

### Text Extraction (poppler-utils)

```bash
# Basic extraction
pdftotext document.pdf output.txt

# Preserve spatial layout
pdftotext -layout document.pdf output.txt

# Specific page range (pages 1-5)
pdftotext -f 1 -l 5 document.pdf output.txt
```

### PDF Manipulation (qpdf)

```bash
# Combine files
qpdf --empty --pages first.pdf second.pdf third.pdf -- combined.pdf

# Extract page range
qpdf source.pdf --pages . 1-10 -- first_ten.pdf

# Rotate specific page (page 1, 90 degrees clockwise)
qpdf input.pdf --rotate=+90:1 -- rotated.pdf

# Remove encryption
qpdf --password=secret --decrypt locked.pdf unlocked.pdf

# Validate structure
qpdf --check document.pdf
```

### Image Extraction (poppler-utils)

```bash
# Extract embedded images as JPEGs
pdfimages -j document.pdf img_prefix
# Creates: img_prefix-000.jpg, img_prefix-001.jpg, ...
```

### Convert to Images (poppler-utils)

```bash
# Render pages as JPEGs at 150 DPI
pdftoppm -jpeg -r 150 document.pdf page
# Creates: page-01.jpg, page-02.jpg, ...
```

---

## QA Checklist

**Verify every PDF you create.** Problems are common; catching them is your job.

### Content Verification

```python
from pypdf import PdfReader

doc = PdfReader("output.pdf")
print(f"Page count: {len(doc.pages)}")

for idx, pg in enumerate(doc.pages, start=1):
    txt = pg.extract_text() or ""
    print(f"Page {idx}: {len(txt)} characters")
```

### Visual Inspection

Convert to images and review:

```bash
pdftoppm -jpeg -r 150 output.pdf preview
# Open preview-01.jpg, preview-02.jpg, etc.
```

**Look for:**
- Text running off page edges
- Overlapping elements
- Missing content or blank areas
- Garbled characters (often Unicode issues)
- Misaligned table columns
- Broken images

### Fix-Verify Loop

1. Generate PDF
2. Check content programmatically
3. Convert to images and inspect visually
4. Document any issues found
5. Fix problems
6. Re-verify (fixes often introduce new issues)
7. Repeat until clean

---

## Troubleshooting

### Black Boxes in Output
Unicode subscripts/superscripts (₀₁₂, ⁰¹²) aren't supported by ReportLab's default fonts. Use `<sub>` and `<super>` XML tags in Paragraph objects instead.

### Empty Text Extraction
The PDF may contain scanned images rather than text. Use OCR (pytesseract + pdf2image) as a fallback.

### Corrupted Files
```bash
qpdf --check damaged.pdf
qpdf --replace-input damaged.pdf
```

### Memory Issues with Large Files
```python
import sys
sys.setrecursionlimit(10000)
```

---

## Dependencies

**Python packages:**
```bash
pip install pypdf pdfplumber reportlab pdf2image pytesseract
```

**System tools (Ubuntu/Debian):**
```bash
apt install poppler-utils qpdf tesseract-ocr
```

**LaTeX (Ubuntu/Debian):**
```bash
apt install texlive texlive-latex-extra texlive-fonts-extra latexmk
```

**LaTeX (macOS):**
```bash
brew install --cask mactex
# Or minimal: brew install basictex
```
