---
name: slide-creator
description: "Create PowerPoint presentations (.pptx) with JavaScript. Triggers when user mentions slides, deck, presentation, .pptx, or needs to generate visual content for meetings, pitches, or talks."
license: MIT
---

# Slide Creator

Generate PowerPoint files using PptxGenJS - a JavaScript library for creating .pptx presentations.

## Quick Start

```javascript
const pptxgen = require("pptxgenjs");

let pres = new pptxgen();
pres.layout = "LAYOUT_16x9";  // 10" x 5.625"
pres.author = "Your Name";
pres.title = "Presentation Title";

let slide = pres.addSlide();
slide.addText("Hello World!", { x: 0.5, y: 0.5, fontSize: 36, color: "363636" });

pres.writeFile({ fileName: "presentation.pptx" });
```

## Layout Dimensions

Slide dimensions (coordinates in inches):
- `LAYOUT_16x9`: 10" x 5.625" (default)
- `LAYOUT_16x10`: 10" x 6.25"
- `LAYOUT_4x3`: 10" x 7.5"
- `LAYOUT_WIDE`: 13.3" x 7.5"

---

## Text & Formatting

```javascript
// Basic text
slide.addText("Simple Text", {
  x: 1, y: 1, w: 8, h: 2, fontSize: 24, fontFace: "Arial",
  color: "363636", bold: true, align: "center", valign: "middle"
});

// Rich text arrays
slide.addText([
  { text: "Bold ", options: { bold: true } },
  { text: "Italic ", options: { italic: true } }
], { x: 1, y: 3, w: 8, h: 1 });

// Multi-line text (requires breakLine: true)
slide.addText([
  { text: "Line 1", options: { breakLine: true } },
  { text: "Line 2", options: { breakLine: true } },
  { text: "Line 3" }
], { x: 0.5, y: 0.5, w: 8, h: 2 });

// Text box margin (internal padding)
slide.addText("Title", {
  x: 0.5, y: 0.3, w: 9, h: 0.6,
  margin: 0  // Use 0 when aligning text with shapes or icons
});
```

### Text Properties

| Property | Values |
|----------|--------|
| `fontSize` | Number (points) |
| `fontFace` | Font name string |
| `color` | Hex color without # |
| `bold` | true/false |
| `italic` | true/false |
| `underline` | true/false |
| `align` | "left", "center", "right" |
| `valign` | "top", "middle", "bottom" |
| `charSpacing` | Number (use this, NOT letterSpacing) |

---

## Lists & Bullets

```javascript
// Multiple bullets
slide.addText([
  { text: "First item", options: { bullet: true, breakLine: true } },
  { text: "Second item", options: { bullet: true, breakLine: true } },
  { text: "Third item", options: { bullet: true } }
], { x: 0.5, y: 0.5, w: 8, h: 3 });

// Sub-items and numbered lists
{ text: "Sub-item", options: { bullet: true, indentLevel: 1 } }
{ text: "First", options: { bullet: { type: "number" }, breakLine: true } }
```

---

## Shapes

**Use `pres.shapes.RECTANGLE`, `pres.shapes.OVAL`, etc.**

```javascript
slide.addShape(pres.shapes.RECTANGLE, {
  x: 0.5, y: 0.8, w: 1.5, h: 3.0,
  fill: { color: "FF0000" },
  line: { color: "000000", width: 2 }
});

slide.addShape(pres.shapes.OVAL, {
  x: 4, y: 1, w: 2, h: 2,
  fill: { color: "0000FF" }
});

slide.addShape(pres.shapes.LINE, {
  x: 1, y: 3, w: 5, h: 0,
  line: { color: "FF0000", width: 3, dashType: "dash" }
});

// With transparency
slide.addShape(pres.shapes.RECTANGLE, {
  x: 1, y: 1, w: 3, h: 2,
  fill: { color: "0088CC", transparency: 50 }
});

// Rounded rectangle
slide.addShape(pres.shapes.ROUNDED_RECTANGLE, {
  x: 1, y: 1, w: 3, h: 2,
  fill: { color: "FFFFFF" },
  rectRadius: 0.1
});

// With shadow (MUST use fresh object each time - see Common Pitfalls)
const cardShadow = () => ({
  type: "outer", color: "000000", blur: 8,
  offset: 3, angle: 135, opacity: 0.1
});

slide.addShape(pres.shapes.RECTANGLE, {
  x: 1, y: 1, w: 3, h: 2,
  fill: { color: "FFFFFF" },
  shadow: cardShadow()
});
```

Available shapes: `RECTANGLE`, `OVAL`, `LINE`, `ROUNDED_RECTANGLE`

### Shadow Options

| Property | Type | Range | Notes |
|----------|------|-------|-------|
| `type` | string | `"outer"`, `"inner"` | |
| `color` | string | 6-char hex | No `#` prefix, no 8-char hex |
| `blur` | number | 0-100 pt | |
| `offset` | number | 0-200 pt | **Must be non-negative** |
| `angle` | number | 0-359 degrees | 135 = bottom-right, 270 = upward |
| `opacity` | number | 0.0-1.0 | Use this for transparency |

---

## Images

```javascript
// From file path
slide.addImage({ path: "images/chart.png", x: 1, y: 1, w: 5, h: 3 });

// From URL
slide.addImage({ path: "https://example.com/image.jpg", x: 1, y: 1, w: 5, h: 3 });

// From base64 (faster, no file I/O)
slide.addImage({ data: "image/png;base64,iVBORw0KGgo...", x: 1, y: 1, w: 5, h: 3 });

// Sizing modes
{ sizing: { type: 'contain', w: 4, h: 3 } }  // Fit inside, preserve ratio
{ sizing: { type: 'cover', w: 4, h: 3 } }    // Fill area, may crop
```

---

## Icons

Use react-icons to generate SVG icons, then rasterize to PNG for universal compatibility.

### Setup

```javascript
const React = require("react");
const ReactDOMServer = require("react-dom/server");
const sharp = require("sharp");
const { FaCheckCircle, FaChartLine, FaRocket } = require("react-icons/fa");

function renderIconSvg(IconComponent, color = "#000000", size = 256) {
  return ReactDOMServer.renderToStaticMarkup(
    React.createElement(IconComponent, { color, size: String(size) })
  );
}

async function iconToBase64Png(IconComponent, color, size = 256) {
  const svg = renderIconSvg(IconComponent, color, size);
  const pngBuffer = await sharp(Buffer.from(svg)).png().toBuffer();
  return "image/png;base64," + pngBuffer.toString("base64");
}
```

### Add Icon to Slide

```javascript
const iconData = await iconToBase64Png(FaCheckCircle, "#4472C4", 256);

slide.addImage({
  data: iconData,
  x: 1, y: 1, w: 0.5, h: 0.5  // Size in inches
});
```

**Note**: Use size 256 or higher for crisp icons. The size parameter controls rasterization resolution, not display size.

### Icon Libraries

Install: `npm install react-icons react react-dom sharp`

Popular sets: `react-icons/fa` (Font Awesome), `react-icons/md` (Material), `react-icons/hi` (Heroicons)

---

## Tables

```javascript
slide.addTable([
  ["Header 1", "Header 2"],
  ["Cell 1", "Cell 2"]
], {
  x: 1, y: 1, w: 8, h: 2,
  border: { pt: 1, color: "999999" },
  fill: { color: "F1F1F1" }
});

// Formatted cells
let tableData = [
  [{ text: "Header", options: { fill: { color: "6699CC" }, color: "FFFFFF", bold: true } }, "Cell"],
  [{ text: "Merged", options: { colspan: 2 } }]
];
slide.addTable(tableData, { x: 1, y: 3.5, w: 8, colW: [4, 4] });
```

### Table Options

| Property | Description |
|----------|-------------|
| `colW` | Column widths: number (uniform) or array |
| `rowH` | Row heights: number (uniform) or array |
| `border` | `{ pt, color }` |
| `align` | Text alignment |
| `valign` | Vertical alignment |
| `fill` | Background color |

---

## Charts

**Use `pres.charts.BAR`, `pres.charts.LINE`, `pres.charts.PIE`, etc.**

```javascript
// Bar chart
slide.addChart(pres.charts.BAR, [{
  name: "Sales",
  labels: ["Q1", "Q2", "Q3", "Q4"],
  values: [4500, 5500, 6200, 7100]
}], {
  x: 0.5, y: 0.6, w: 6, h: 3,
  barDir: "col",  // "col" (vertical) or "bar" (horizontal)
  showTitle: true,
  title: "Quarterly Sales"
});

// Line chart
slide.addChart(pres.charts.LINE, [{
  name: "Temp",
  labels: ["Jan", "Feb", "Mar"],
  values: [32, 35, 42]
}], {
  x: 0.5, y: 4, w: 6, h: 3,
  lineSize: 3,
  lineSmooth: true
});

// Pie chart
slide.addChart(pres.charts.PIE, [{
  name: "Share",
  labels: ["A", "B", "Other"],
  values: [35, 45, 20]
}], {
  x: 7, y: 1, w: 5, h: 4,
  showPercent: true
});
```

Chart types: `BAR`, `LINE`, `PIE`, `DOUGHNUT`, `SCATTER`, `BUBBLE`, `RADAR`

### Better-Looking Charts

Default charts look dated. Apply these options for modern appearance:

```javascript
slide.addChart(pres.charts.BAR, chartData, {
  x: 0.5, y: 1, w: 9, h: 4,
  barDir: "col",

  // Custom colors (match your presentation palette)
  chartColors: ["0D9488", "14B8A6", "5EEAD4"],

  // Clean background
  chartArea: { fill: { color: "FFFFFF" }, roundedCorners: false },

  // Muted axis labels
  catAxisLabelColor: "64748B",
  valAxisLabelColor: "64748B",
  catAxisLabelFontSize: 10,
  valAxisLabelFontSize: 10,

  // Subtle grid (value axis only)
  valGridLine: { color: "E2E8F0", size: 0.5 },
  catGridLine: { style: "none" },

  // Data labels on bars
  showValue: true,
  dataLabelPosition: "outEnd",
  dataLabelColor: "1E293B",
  dataLabelFontSize: 10,

  // Title
  showTitle: true,
  title: "Chart Title",
  titleFontSize: 12,
  titleColor: "1E293B",

  // Hide legend for single series
  showLegend: false
});
```

**Key styling options:**
- `chartColors: [...]` - hex colors for series/segments
- `chartArea: { fill, roundedCorners }` - chart background
- `catGridLine/valGridLine: { color, style, size }` - grid lines (`style: "none"` to hide)
- `lineSmooth: true` - curved lines (line charts)
- `legendPos: "r"` - legend position: "b", "t", "l", "r", "tr"

---

## Slide Backgrounds

```javascript
// Solid color
slide.background = { color: "F1F1F1" };

// Image background
slide.background = { path: "./background.jpg" };
// or base64
slide.background = { data: "image/png;base64,..." };
```

---

## Saving Output

All save methods return Promises:

```javascript
// Node.js - write to file
await pres.writeFile({ fileName: "output.pptx" });

// Get as base64
const base64 = await pres.write({ outputType: "base64" });

// Get as buffer (Node.js)
const buffer = await pres.write({ outputType: "nodebuffer" });
```

---

## Common Pitfalls

These issues cause file corruption, visual bugs, or broken output. Avoid them.

1. **NEVER use "#" with hex colors** - causes file corruption
   ```javascript
   color: "FF0000"      // CORRECT
   color: "#FF0000"     // WRONG - corrupts file
   ```

2. **NEVER encode opacity in hex color strings** - 8-char colors corrupt the file
   ```javascript
   shadow: { color: "00000020" }              // CORRUPTS FILE
   shadow: { color: "000000", opacity: 0.12 } // CORRECT
   ```

3. **Use `bullet: true`** - NEVER unicode symbols like "bullet" (creates double bullets)

4. **Use `breakLine: true`** between array items for multi-line text

5. **NEVER reuse option objects across calls** - PptxGenJS mutates objects in-place
   ```javascript
   const shadow = { type: "outer", blur: 6 };
   slide.addShape(pres.shapes.RECTANGLE, { shadow, ... });  // WRONG
   slide.addShape(pres.shapes.RECTANGLE, { shadow, ... });  // Gets corrupted

   // CORRECT: Create fresh objects
   const makeShadow = () => ({ type: "outer", blur: 6 });
   slide.addShape(pres.shapes.RECTANGLE, { shadow: makeShadow(), ... });
   slide.addShape(pres.shapes.RECTANGLE, { shadow: makeShadow(), ... });
   ```

6. **Each presentation needs fresh instance** - don't reuse `pptxgen()` objects

7. **Use correct API names**
   ```javascript
   pres.shapes.RECTANGLE   // CORRECT
   pres.ShapeType.rect     // WRONG - doesn't exist

   pres.charts.BAR         // CORRECT
   pres.ChartType.bar      // WRONG - doesn't exist
   ```

8. **Don't use ROUNDED_RECTANGLE with accent borders** - rectangular overlays won't cover rounded corners

---

## Design Ideas

### Color Palettes

Choose colors that match your topic - don't default to generic blue:

| Theme | Primary | Secondary | Accent |
|-------|---------|-----------|--------|
| **Teal Trust** | `028090` | `00A896` | `02C39A` |
| **Midnight Executive** | `1E2761` | `CADCFC` | `FFFFFF` |
| **Forest & Moss** | `2C5F2D` | `97BC62` | `F5F5F5` |
| **Coral Energy** | `F96167` | `F9E795` | `2F3C7E` |
| **Charcoal Minimal** | `36454F` | `F2F2F2` | `212121` |

### Typography

| Header Font | Body Font |
|-------------|-----------|
| Georgia | Calibri |
| Arial Black | Arial |
| Trebuchet MS | Calibri |

| Element | Size |
|---------|------|
| Slide title | 36-44pt bold |
| Section header | 20-24pt bold |
| Body text | 14-16pt |
| Captions | 10-12pt muted |

### Layout Tips

- Dark backgrounds for title + conclusion slides, light for content
- Use cards with shadows for content groupings
- Icon in colored circle + text for feature lists
- Large stat callouts (60-72pt numbers with small labels)
- 0.5" minimum margins from slide edges

---

## Dependencies

- `pptxgenjs` (npm package)
- `react`, `react-dom`, `sharp`, `react-icons` (for icon rendering)
- Works in Node.js and browsers

## Notes

- Dimensions default to inches
- Colors are hex without the # prefix
- Images must be accessible (local path or CORS-enabled URL)
