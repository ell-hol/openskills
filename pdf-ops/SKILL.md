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
| Create PDFs (programmatic) | reportlab | Canvas or Platypus |
| Create PDFs (documents) | LaTeX | `pdflatex document.tex` |
| Command line merge | qpdf | `qpdf --empty --pages ...` |
| OCR scanned PDFs | pytesseract | Convert to image first |

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
# Basic compilation
pdflatex document.tex

# With bibliography
pdflatex document.tex
bibtex document
pdflatex document.tex
pdflatex document.tex

# XeLaTeX (for system fonts)
xelatex document.tex

# LuaLaTeX (modern, powerful)
lualatex document.tex

# latexmk (auto-handles dependencies)
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

#### Professional Document Example

```python
from reportlab.lib.pagesizes import letter
from reportlab.lib.units import inch
from reportlab.lib import colors
from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
from reportlab.lib.enums import TA_CENTER, TA_LEFT
from reportlab.platypus import (SimpleDocTemplate, Paragraph, Spacer,
                                 Table, TableStyle, PageBreak, Image)

# Define color palette
PRIMARY = colors.HexColor('#1a365d')
SECONDARY = colors.HexColor('#2c5282')
ACCENT = colors.HexColor('#ed8936')
LIGHT_BG = colors.HexColor('#f7fafc')
TEXT_DARK = colors.HexColor('#2d3748')

# Custom styles
styles = getSampleStyleSheet()
styles.add(ParagraphStyle(
    'CustomTitle',
    parent=styles['Title'],
    fontSize=28,
    textColor=PRIMARY,
    spaceAfter=30,
    alignment=TA_CENTER
))
styles.add(ParagraphStyle(
    'SectionHeader',
    parent=styles['Heading1'],
    fontSize=16,
    textColor=PRIMARY,
    spaceBefore=20,
    spaceAfter=10,
    borderPadding=(0, 0, 5, 0),
))
styles.add(ParagraphStyle(
    'BodyText',
    parent=styles['Normal'],
    fontSize=10,
    textColor=TEXT_DARK,
    leading=14,
    spaceBefore=6,
    spaceAfter=6
))

# Build document
doc = SimpleDocTemplate(
    "report.pdf",
    pagesize=letter,
    topMargin=1*inch,
    bottomMargin=1*inch,
    leftMargin=1*inch,
    rightMargin=1*inch
)

story = []

# Title
story.append(Paragraph("Quarterly Business Report", styles['CustomTitle']))
story.append(Paragraph("Q4 2024 Performance Summary", styles['BodyText']))
story.append(Spacer(1, 0.5*inch))

# Section with styled header
story.append(Paragraph("Executive Summary", styles['SectionHeader']))
story.append(Paragraph(
    "Revenue increased 15% year-over-year, driven by strong performance "
    "in the enterprise segment. Customer acquisition costs decreased while "
    "retention rates improved across all product lines.",
    styles['BodyText']
))

# Professional table
data = [
    ['Metric', 'Q3 2024', 'Q4 2024', 'Change'],
    ['Revenue', '$2.4M', '$2.8M', '+16.7%'],
    ['Customers', '1,240', '1,485', '+19.8%'],
    ['Churn Rate', '4.2%', '3.1%', '-26.2%'],
    ['NPS Score', '42', '51', '+21.4%'],
]

table = Table(data, colWidths=[2*inch, 1.2*inch, 1.2*inch, 1*inch])
table.setStyle(TableStyle([
    # Header
    ('BACKGROUND', (0, 0), (-1, 0), PRIMARY),
    ('TEXTCOLOR', (0, 0), (-1, 0), colors.white),
    ('FONTNAME', (0, 0), (-1, 0), 'Helvetica-Bold'),
    ('FONTSIZE', (0, 0), (-1, 0), 10),
    ('BOTTOMPADDING', (0, 0), (-1, 0), 10),
    ('TOPPADDING', (0, 0), (-1, 0), 10),

    # Body
    ('BACKGROUND', (0, 1), (-1, -1), colors.white),
    ('ROWBACKGROUNDS', (0, 1), (-1, -1), [colors.white, LIGHT_BG]),
    ('TEXTCOLOR', (0, 1), (-1, -1), TEXT_DARK),
    ('FONTNAME', (0, 1), (-1, -1), 'Helvetica'),
    ('FONTSIZE', (0, 1), (-1, -1), 9),
    ('BOTTOMPADDING', (0, 1), (-1, -1), 8),
    ('TOPPADDING', (0, 1), (-1, -1), 8),

    # Borders
    ('LINEBELOW', (0, 0), (-1, 0), 2, SECONDARY),
    ('LINEBELOW', (0, -1), (-1, -1), 1, colors.HexColor('#cbd5e0')),

    # Alignment
    ('ALIGN', (1, 0), (-1, -1), 'CENTER'),
    ('ALIGN', (0, 0), (0, -1), 'LEFT'),
    ('VALIGN', (0, 0), (-1, -1), 'MIDDLE'),
]))

story.append(Spacer(1, 0.3*inch))
story.append(table)

# Header and footer
def add_header_footer(canvas, doc):
    canvas.saveState()
    # Header line
    canvas.setStrokeColor(PRIMARY)
    canvas.setLineWidth(2)
    canvas.line(inch, letter[1] - 0.5*inch, letter[0] - inch, letter[1] - 0.5*inch)

    # Footer
    canvas.setFont('Helvetica', 8)
    canvas.setFillColor(colors.HexColor('#718096'))
    canvas.drawString(inch, 0.5*inch, "Confidential")
    canvas.drawRightString(letter[0] - inch, 0.5*inch, f"Page {doc.page}")
    canvas.restoreState()

doc.build(story, onFirstPage=add_header_footer, onLaterPages=add_header_footer)
```

#### Basic PDF with Canvas

```python
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas
from reportlab.lib import colors

c = canvas.Canvas("simple.pdf", pagesize=letter)
width, height = letter

# Colored header bar
c.setFillColor(colors.HexColor('#1a365d'))
c.rect(0, height - 80, width, 80, fill=True, stroke=False)

# Title on header
c.setFillColor(colors.white)
c.setFont('Helvetica-Bold', 24)
c.drawString(72, height - 50, "Document Title")

# Body text
c.setFillColor(colors.HexColor('#2d3748'))
c.setFont('Helvetica', 11)
c.drawString(72, height - 120, "Professional document content goes here.")

c.save()
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
- `reportlab` - create PDFs programmatically
- `pytesseract` + `pdf2image` - OCR

**LaTeX:**
- `texlive` - TeX Live distribution (full or basic)
- `texlive-fonts-extra` - additional fonts
- `texlive-latex-extra` - additional packages
- `latexmk` - build automation

**System:**
- `poppler-utils` - pdftotext, pdftoppm, pdfimages
- `qpdf` - manipulation and repair
- `tesseract-ocr` - OCR engine

Install:
```bash
# Python packages
pip install pypdf pdfplumber reportlab pdf2image pytesseract

# System packages (Ubuntu/Debian)
apt install poppler-utils qpdf tesseract-ocr

# LaTeX (Ubuntu/Debian)
apt install texlive texlive-latex-extra texlive-fonts-extra latexmk

# LaTeX (macOS with Homebrew)
brew install --cask mactex
# Or minimal: brew install basictex

# LaTeX (macOS with MacPorts)
port install texlive
```

## Notes

- Use reportlab for creating new PDFs, pypdf for manipulating existing ones
- Page sizes: `letter` (8.5x11"), `A4` (210x297mm), measured in points (72 per inch)
- Text extraction results depend on PDF structure - some files store text as images
