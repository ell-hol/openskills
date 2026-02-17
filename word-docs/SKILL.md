---
name: word-docs
description: "Create and edit Word documents (.docx). Triggers when user mentions Word, .docx, document creation, reports, memos, letters, or needs professional document formatting with tables, images, and styles."
license: MIT
---

# Word Documents

Create Word documents using the docx package (JavaScript/Node.js).

## Installation

```bash
npm install docx
```

## Basic Document

```javascript
const { Document, Packer, Paragraph, TextRun } = require("docx");
const fs = require("fs");

const doc = new Document({
    sections: [{
        children: [
            new Paragraph({
                children: [
                    new TextRun("Hello World")
                ]
            })
        ]
    }]
});

Packer.toBuffer(doc).then(buffer => {
    fs.writeFileSync("document.docx", buffer);
});
```

## Text Formatting

```javascript
const { Document, Paragraph, TextRun, HeadingLevel } = require("docx");

new Paragraph({
    children: [
        new TextRun({
            text: "Bold text",
            bold: true
        }),
        new TextRun({
            text: " and italic",
            italics: true
        }),
        new TextRun({
            text: " and colored",
            color: "FF0000"
        })
    ]
})

// Headings
new Paragraph({
    text: "Chapter Title",
    heading: HeadingLevel.HEADING_1
})
```

### TextRun Options

| Property | Description |
|----------|-------------|
| `bold` | Bold text |
| `italics` | Italic text |
| `underline` | Underlined text |
| `strike` | Strikethrough |
| `color` | Text color (hex) |
| `size` | Font size (half-points, 24 = 12pt) |
| `font` | Font name |
| `highlight` | Highlight color |

## Paragraphs

```javascript
const { Paragraph, AlignmentType } = require("docx");

new Paragraph({
    text: "Centered text",
    alignment: AlignmentType.CENTER
})

new Paragraph({
    text: "Indented paragraph",
    indent: {
        left: 720  // Twips (1/20 of a point)
    }
})

new Paragraph({
    text: "Spaced paragraph",
    spacing: {
        before: 200,
        after: 200
    }
})
```

## Lists

```javascript
const { Document, Paragraph, TextRun, LevelFormat, AlignmentType } = require("docx");

const doc = new Document({
    numbering: {
        config: [
            {
                reference: "my-bullets",
                levels: [{
                    level: 0,
                    format: LevelFormat.BULLET,
                    text: "\u2022",
                    alignment: AlignmentType.LEFT,
                    style: {
                        paragraph: {
                            indent: { left: 720, hanging: 360 }
                        }
                    }
                }]
            },
            {
                reference: "my-numbers",
                levels: [{
                    level: 0,
                    format: LevelFormat.DECIMAL,
                    text: "%1.",
                    alignment: AlignmentType.LEFT,
                    style: {
                        paragraph: {
                            indent: { left: 720, hanging: 360 }
                        }
                    }
                }]
            }
        ]
    },
    sections: [{
        children: [
            // Bullet list
            new Paragraph({
                text: "First bullet",
                numbering: { reference: "my-bullets", level: 0 }
            }),
            // Numbered list
            new Paragraph({
                text: "Step one",
                numbering: { reference: "my-numbers", level: 0 }
            }),
            new Paragraph({
                text: "Step two",
                numbering: { reference: "my-numbers", level: 0 }
            })
        ]
    }]
});
```

## Tables

**CRITICAL: Tables need dual widths** - set both `columnWidths` on the table AND `width` on each cell. Without both, tables render incorrectly.

```javascript
const { Table, TableRow, TableCell, Paragraph, WidthType, BorderStyle, ShadingType, TextRun } = require("docx");

// Border style helper
const border = { style: BorderStyle.SINGLE, size: 1, color: "CCCCCC" };
const borders = { top: border, bottom: border, left: border, right: border };

// US Letter with 1" margins = 9360 DXA content width
const table = new Table({
    width: { size: 9360, type: WidthType.DXA },
    columnWidths: [2340, 2340, 2340, 2340],  // Must sum to 9360
    rows: [
        new TableRow({
            children: [
                new TableCell({
                    children: [new Paragraph("Header 1")],
                    width: { size: 2340, type: WidthType.DXA },  // Must match columnWidth
                    borders,
                    shading: { fill: "D5E8F0", type: ShadingType.CLEAR },  // CLEAR not SOLID
                    margins: { top: 80, bottom: 80, left: 120, right: 120 }
                }),
                new TableCell({
                    children: [new Paragraph("Header 2")],
                    width: { size: 2340, type: WidthType.DXA },
                    borders,
                    shading: { fill: "D5E8F0", type: ShadingType.CLEAR },
                    margins: { top: 80, bottom: 80, left: 120, right: 120 }
                }),
                new TableCell({
                    children: [new Paragraph("Header 3")],
                    width: { size: 2340, type: WidthType.DXA },
                    borders,
                    shading: { fill: "D5E8F0", type: ShadingType.CLEAR },
                    margins: { top: 80, bottom: 80, left: 120, right: 120 }
                }),
                new TableCell({
                    children: [new Paragraph("Header 4")],
                    width: { size: 2340, type: WidthType.DXA },
                    borders,
                    shading: { fill: "D5E8F0", type: ShadingType.CLEAR },
                    margins: { top: 80, bottom: 80, left: 120, right: 120 }
                })
            ]
        }),
        new TableRow({
            children: [
                new TableCell({ children: [new Paragraph("Cell 1")], width: { size: 2340, type: WidthType.DXA }, borders, margins: { top: 80, bottom: 80, left: 120, right: 120 } }),
                new TableCell({ children: [new Paragraph("Cell 2")], width: { size: 2340, type: WidthType.DXA }, borders, margins: { top: 80, bottom: 80, left: 120, right: 120 } }),
                new TableCell({ children: [new Paragraph("Cell 3")], width: { size: 2340, type: WidthType.DXA }, borders, margins: { top: 80, bottom: 80, left: 120, right: 120 } }),
                new TableCell({ children: [new Paragraph("Cell 4")], width: { size: 2340, type: WidthType.DXA }, borders, margins: { top: 80, bottom: 80, left: 120, right: 120 } })
            ]
        })
    ]
});
```

### Table Width Rules

- **Always use `WidthType.DXA`** - never `WidthType.PERCENTAGE` (breaks in Google Docs)
- **Table width = sum of columnWidths** - e.g., 9360 = 2340 + 2340 + 2340 + 2340
- **Cell width must match its columnWidth** - both use same DXA value
- **Use `ShadingType.CLEAR`** - never SOLID (causes black backgrounds)
- **Always add cell margins** - for readable padding

### Common Page Widths (DXA)

| Page | Content Width (1" margins) |
|------|---------------------------|
| US Letter | 9360 (12240 - 2880) |
| A4 | 9026 (11906 - 2880) |

*1440 DXA = 1 inch*
```

## Images

```javascript
const { Paragraph, ImageRun } = require("docx");
const fs = require("fs");

new Paragraph({
    children: [
        new ImageRun({
            data: fs.readFileSync("image.png"),
            transformation: {
                width: 200,
                height: 150
            },
            type: "png"  // Required: png, jpg, gif, bmp
        })
    ]
})
```

## Page Setup

```javascript
const { Document, PageOrientation } = require("docx");

const doc = new Document({
    sections: [{
        properties: {
            page: {
                size: {
                    width: 12240,   // US Letter width in DXA
                    height: 15840,  // US Letter height
                    orientation: PageOrientation.PORTRAIT
                },
                margin: {
                    top: 1440,      // 1 inch = 1440 DXA
                    right: 1440,
                    bottom: 1440,
                    left: 1440
                }
            }
        },
        children: [/* content */]
    }]
});
```

### Page Sizes (DXA units)

| Paper | Width | Height |
|-------|-------|--------|
| US Letter | 12240 | 15840 |
| A4 | 11906 | 16838 |

## Headers and Footers

```javascript
const { Document, Header, Footer, Paragraph, TextRun, PageNumber, AlignmentType } = require("docx");

const doc = new Document({
    sections: [{
        headers: {
            default: new Header({
                children: [
                    new Paragraph({
                        text: "Document Title",
                        alignment: AlignmentType.CENTER
                    })
                ]
            })
        },
        footers: {
            default: new Footer({
                children: [
                    new Paragraph({
                        alignment: AlignmentType.CENTER,
                        children: [
                            new TextRun("Page "),
                            new TextRun({ children: [PageNumber.CURRENT] }),
                            new TextRun(" of "),
                            new TextRun({ children: [PageNumber.TOTAL_PAGES] })
                        ]
                    })
                ]
            })
        },
        children: [/* content */]
    }]
});
```

Note: Use `PageNumber.CURRENT` and `PageNumber.TOTAL_PAGES` inside a `TextRun` with `children` property for dynamic page numbering.

## Page Breaks

```javascript
const { Paragraph, PageBreak } = require("docx");

new Paragraph({
    children: [new PageBreak()]
})
```

## Saving Documents

```javascript
const { Packer } = require("docx");
const fs = require("fs");

// Save to file (Node.js)
Packer.toBuffer(doc).then(buffer => {
    fs.writeFileSync("output.docx", buffer);
});

// Get as base64
Packer.toBase64String(doc).then(base64 => {
    console.log(base64);
});

// Browser download
Packer.toBlob(doc).then(blob => {
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = url;
    a.download = "document.docx";
    a.click();
});
```

## Multiple Sections

```javascript
const doc = new Document({
    sections: [
        {
            properties: { /* section 1 settings */ },
            children: [/* section 1 content */]
        },
        {
            properties: { /* section 2 settings */ },
            children: [/* section 2 content */]
        }
    ]
});
```

## Dependencies

- `docx` (npm package)

## Notes

- Measurements in DXA (1 inch = 1440 DXA, 1 point = 20 DXA)
- Font sizes in half-points (24 = 12pt)
- Use separate Paragraph elements instead of `\n` for line breaks
- PageBreak must be inside a Paragraph
- ImageRun requires the `type` property
- Works in both Node.js and browsers
