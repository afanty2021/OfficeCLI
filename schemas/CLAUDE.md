[根目录](../CLAUDE.md) > **schemas**

---

# OfficeCLI Schema System Module

> **Module Type**: Help System | **Format**: JSON Schema | **Status**: Active Development

---

## 📋 Change Log

| Date | Change | Impact |
|------|--------|--------|
| 2026-05-18 | Initial module documentation created | Schema structure documented |

---

## 🎯 Module Responsibilities

The `schemas/` module contains OfficeCLI's built-in help system:

- **JSON Schemas**: Complete property definitions for every element type
- **Validation Rules**: Allowed values, types, formats, and constraints
- **Examples**: Sample usage for each property
- **AI Agent Discovery**: Structured help for automated tool selection
- **Cross-format References**: Shared schemas for common elements

---

## 🚀 Entry Points & Usage

### Accessing Schemas via CLI

```bash
# List all schemas for a format
officecli help docx

# Get specific element schema
officecli help docx paragraph

# Get settable properties only
officecli help docx set paragraph

# Get machine-readable JSON
officecli help docx paragraph --json

# Get all schemas (flat dump)
officecli help all
```

### Schema File Locations

```
schemas/
├── help/                           # Format-specific schemas
│   ├── docx/                      # Word schemas
│   │   ├── paragraph.json
│   │   ├── run.json
│   │   ├── table.json
│   │   └── ...
│   ├── xlsx/                      # Excel schemas
│   │   ├── cell.json
│   │   ├── sheet.json
│   │   ├── chart.json
│   │   └── ...
│   ├── pptx/                      # PowerPoint schemas
│   │   ├── slide.json
│   │   ├── shape.json
│   │   ├── chart.json
│   │   └── ...
│   └── _shared/                   # Cross-format schemas
│       ├── paragraph.json         # Shared by docx/pptx
│       ├── table.json             # Shared by docx/xlsx/pptx
│       ├── chart.json             # Shared by xlsx/pptx
│       ├── picture.json
│       └── ...
└── README.md                      # This file
```

---

## 📊 Schema Structure

### JSON Schema Format

Each schema file follows this structure:

```json
{
  "element": "paragraph",
  "description": "A paragraph element in Word documents",
  "path": "/body/p[N]",
  "parent": "/body",
  "addable": true,
  "settable": true,
  "properties": {
    "style": {
      "type": "string",
      "description": "Paragraph style name",
      "examples": ["Normal", "Heading1", "Title"],
      "default": "Normal"
    },
    "alignment": {
      "type": "string",
      "description": "Text alignment",
      "enum": ["left", "center", "right", "justify"],
      "default": "left"
    },
    "bold": {
      "type": "boolean",
      "description": "Apply bold formatting to all runs in paragraph",
      "default": false
    },
    "spacing": {
      "type": "string",
      "description": "Line spacing (pt, cm, in, or multiplier)",
      "examples": ["12pt", "1.5", "150%", "0.5cm"],
      "pattern": "^\\d+(\\.\\d+)?(pt|cm|in|%|x)?$"
    }
  },
  "aliases": {
    "align": "alignment",
    "justify": "alignment",
    "fontsize": "size"
  },
  "examples": [
    {
      "description": "Create a heading paragraph",
      "commands": [
        "officecli add doc.docx /body --type paragraph --prop style=Heading1 --prop text=\"Introduction\""
      ]
    }
  ]
}
```

### Schema Fields

| Field | Type | Description |
|-------|------|-------------|
| `element` | string | Element name (lowercase, local name) |
| `description` | string | Human-readable description |
| `path` | string | XPath-style path to this element |
| `parent` | string | Valid parent paths |
| `addable` | boolean | Can this element be added? |
| `settable` | boolean | Can this element be modified? |
| `properties` | object | Property definitions (see below) |
| `aliases` | object | Property name aliases |
| `examples` | array | Usage examples |

### Property Definition

Each property in `properties` object:

```json
{
  "type": "string|boolean|integer|number|array|object",
  "description": "Human-readable description",
  "enum": ["value1", "value2"],        // Optional: allowed values
  "pattern": "regex",                   // Optional: validation pattern
  "default": "default_value",           // Optional: default value
  "examples": ["example1", "example2"], // Optional: example values
  "min": 0,                             // Optional: minimum value
  "max": 100                            // Optional: maximum value
}
```

---

## 🔧 Key Schemas by Format

### Word (.docx) Schemas

**Core Elements**:
- `document.json` - Document root
- `body.json` - Document body
- `paragraph.json` - Paragraphs
- `run.json` - Text runs
- `table.json` - Tables
- `image.json` - Embedded images

**Advanced Elements**:
- `style.json` - Paragraph/character styles
- `header.json`, `footer.json` - Headers/footers
- `footnote.json`, `endnote.json` - Notes
- `bookmark.json` - Document bookmarks
- `field.json` - Field codes (MERGEFIELD, REF, etc.)
- `formfield.json` - Form fields
- `sdt.json` - Content controls (SDT)
- `equation.json` - Math equations (OMML)
- `chart.json` - Embedded charts
- `ole.json` - OLE embedded objects

### Excel (.xlsx) Schemas

**Core Elements**:
- `sheet.json` - Worksheets
- `cell.json` - Cells
- `row.json` - Rows
- `column.json` - Columns
- `table.json` - Excel tables

**Advanced Elements**:
- `chart.json` - Charts (all types)
- `pivottable.json` - Pivot tables
- `slicer.json` - Pivot slicers
- `validation.json` - Data validation
- `conditionalformatting.json` - Conditional formats
- `autofilter.json` - Auto filters
- `namedrange.json` - Named ranges
- `sparkline.json` - Sparklines
- `comment.json` - Cell comments

### PowerPoint (.pptx) Schemas

**Core Elements**:
- `presentation.json` - Presentation root
- `slide.json` - Slides
- `shape.json` - Shapes (text boxes, rectangles, etc.)
- `table.json` - Tables
- `picture.json` - Images

**Advanced Elements**:
- `chart.json` - Charts
- `animation.json` - Animation effects
- `transition.json` - Slide transitions
- `video.json`, `audio.json` - Media elements
- `model3d.json` - 3D models (.glb)
- `group.json` - Shape groups
- `connector.json` - Connectors
- `notes.json` - Speaker notes
- `comment.json` - Comments
- `placeholder.json` - Layout placeholders
- `slidelayout.json` - Slide layouts
- `slidemaster.json` - Slide masters
- `theme.json` - Themes

### Cross-Format Schemas (_shared/)

Elements shared across multiple formats:

- `paragraph.json` - Shared by docx/pptx
- `run.json` - Shared by docx/pptx
- `table.json` - Shared by docx/xlsx/pptx
- `chart.json` - Shared by xlsx/pptx
- `picture.json` - Shared by docx/xlsx/pptx
- `hyperlink.json` - Shared by all formats
- `ole.json` - Shared by docx/pptx

---

## 🧪 Schema Validation

### Validation Rules

Schemas are validated on build:

```bash
# Check all schemas are valid JSON
find schemas/help -name "*.json" -exec jq empty {} \;

# Check required fields
jq '.element, .description, .properties' schemas/help/docx/paragraph.json

# Check examples are runnable
jq '.examples[].commands[]' schemas/help/docx/paragraph.json
```

### Schema Coverage

```bash
# Count schemas per format
find schemas/help/docx -name "*.json" | wc -l    # Word
find schemas/help/xlsx -name "*.json" | wc -l    # Excel
find schemas/help/pptx -name "*.json" | wc -l    # PowerPoint
find schemas/help/_shared -name "*.json" | wc -l # Shared
```

---

## 🔨 Common Development Tasks

### Adding a New Element Schema

1. **Create JSON file**: `schemas/help/<format>/<element>.json`
2. **Define properties**: Add all settable properties with types/examples
3. **Add aliases**: Map common abbreviations to canonical names
4. **Add examples**: Include runnable command examples
5. **Validate**: Check JSON syntax and required fields
6. **Test**: Run `officecli help <format> <element>`

**Example**:

```bash
# Create new schema
cat > schemas/help/docx/custom_element.json << 'EOF'
{
  "element": "custom_element",
  "description": "A custom element for demonstration",
  "path": "/body/custom[N]",
  "parent": "/body",
  "addable": true,
  "settable": true,
  "properties": {
    "text": {
      "type": "string",
      "description": "Element text content",
      "default": ""
    },
    "color": {
      "type": "string",
      "description": "Text color (hex or named)",
      "pattern": "^(#[0-9A-Fa-f]{6}|[a-zA-Z]+)$",
      "examples": ["#FF0000", "red", "blue"]
    }
  },
  "examples": [
    {
      "description": "Create a custom element",
      "commands": [
        "officecli add doc.docx /body --type custom_element --prop text=\"Hello\" --prop color=red"
      ]
    }
  ]
}
EOF

# Test schema
officecli help docx custom_element
```

### Updating an Existing Schema

1. **Edit JSON file**: Modify `schemas/help/<format>/<element>.json`
2. **Add new properties**: Follow existing property format
3. **Update examples**: Ensure examples are still valid
4. **Validate**: Check JSON syntax
5. **Test**: Reload and verify via CLI

### Creating Cross-Format Schemas

1. **Create in _shared/**: `schemas/help/_shared/<element>.json`
2. **Define common properties**: Focus on shared attributes
3. **Reference in format schemas**: Use `$ref` or copy properties
4. **Test across formats**: Verify works in docx/xlsx/pptx

---

## ❓ FAQ

### Q: How are schemas loaded at runtime?

A: Schemas are embedded as .NET resources (see `officecli.csproj`). The `SchemaHelpLoader` class reads them via `Assembly.GetManifestResourceStream()` and caches them in memory. The `help` command queries this cache.

### Q: Can I extend schemas without modifying the binary?

A: No. Schemas are compiled into the binary. To add custom schemas, you must rebuild OfficeCLI. However, you can create custom skills (see `skills/` module) that teach agents about your specific use cases.

### Q: How do aliases work?

A: Aliases map user-friendly property names to canonical names. For example, `align` → `alignment`. When a user specifies `--prop align=center`, the system maps it to the canonical `alignment` property before processing.

### Q: What's the difference between addable and settable?

A: `addable` means the element can be created new (via `add` command). `settable` means existing elements of this type can be modified (via `set` command). Some elements are `addable=false` (can only be referenced, not created) or `settable=false` (read-only).

### Q: How are cross-format schemas handled?

A: Cross-format schemas in `_shared/` define properties common to multiple formats. Format-specific schemas may extend or override these. For example, `chart.json` in `_shared/` defines basic chart properties, while `xlsx/chart.json` adds Excel-specific charting features.

---

## 📚 Related Files

### Schema Infrastructure

- `../src/officecli/Core/SchemaHelpLoader.cs` - Schema loading logic
- `../src/officecli/CommandBuilder.Help.cs` - Help command implementation
- `../src/officecli/Core/SchemaKeyNormalizer.cs` - Property name normalization

### Schema Files

- `help/docx/**/*.json` - Word element schemas
- `help/xlsx/**/*.json` - Excel element schemas
- `help/pptx/**/*.json` - PowerPoint element schemas
- `help/_shared/**/*.json` - Cross-format schemas

### Documentation

- `README.md` - This file
- `../docs/schema-format.md` - (hypothetical) Schema format specification

---

## 🔗 External References

- **JSON Schema Specification**: https://json-schema.org/
- **OpenXML Standards**: https://www.ecma-international.org/publications-and-standards/standards/ecma-376/
- **Office CLI Help System**: https://github.com/iOfficeAI/OfficeCLI/wiki/command-reference

---

*The schema system is how OfficeCLI teaches AI agents what properties exist, what values are valid, and how to use them. Every element type has a corresponding schema file that defines its capabilities.*
