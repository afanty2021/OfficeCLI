[根目录](../CLAUDE.md) > **examples**

---

# OfficeCLI Examples Module

> **Module Type**: Documentation & Testing | **Language**: Shell, Python, Markdown | **Status**: Active

---

## 📋 Change Log

| Date | Change | Impact |
|------|--------|--------|
| 2026-05-18 | Initial module documentation created | Examples structure catalogued |

---

## 🎯 Module Responsibilities

The `examples/` module serves as:

- **Comprehensive Usage Examples**: Real-world scripts demonstrating all OfficeCLI capabilities
- **Integration Test Suite**: Executable tests that validate core functionality
- **Learning Resource**: Step-by-step guides for common workflows
- **Template Library**: Ready-to-use document templates and build scripts

---

## 🚀 Entry Points & Structure

### Directory Organization

```
examples/
├── README.md                          # Master guide (quick start, patterns)
├── word/                              # Word (.docx) examples
│   ├── formulas.sh / .docx           # LaTeX math formulas
│   ├── tables.sh / .docx             # Styled tables
│   ├── textbox.sh                    # Formatted text boxes
│   └── numbering-showcase.sh / .docx # List/numbering styles
├── excel/                             # Excel (.xlsx) examples
│   ├── charts.sh / .xlsx             # Master chart showcase (14+ types)
│   ├── charts-demo.sh / .xlsx        # Interactive chart demo
│   ├── charts-<type>.py / .xlsx      # Per-type chart scripts
│   └── pivot-tables.py / .xlsx       # Pivot table generation
└── ppt/                               # PowerPoint (.pptx) examples
    ├── presentation.{md,sh,pptx}     # Morph transitions / full deck
    ├── animations.{md,sh,pptx}       # Animation effects
    ├── video.{md,py,pptx}            # Video embedding
    ├── 3d-model.{md,sh,pptx}         # 3D model embedding
    └── templates/                    # Pre-built presentation templates
```

### Standard Example Format

Each example follows the trio pattern:

1. **`<name>.md`**: Markdown walkthrough with explanations
2. **`<name>.sh` / `<name>.py`**: Executable build script
3. **`<name>.<ext>`**: Pre-generated output document

---

## 🔌 Public Interface (Example Categories)

### 📄 Word Examples

Located in `examples/word/`

| Example | Script | Demonstrates |
|---------|--------|-------------|
| **Formulas** | `formulas.sh` | LaTeX math equations, OMML formatting |
| **Tables** | `tables.sh` | Complex tables with styles, borders, merges |
| **Text Box** | `textbox.sh` | Formatted text boxes with positioning |
| **Numbering** | `numbering-showcase.sh` | List styles, multi-level numbering |

**Key Techniques**:
- Paragraph and run formatting
- Table creation and styling
- Equation insertion via LaTeX syntax
- Numbering and bullet lists
- Text boxes and positioning

### 📊 Excel Examples

Located in `examples/excel/`

#### Chart Examples (14+ Types)

| Example | Script | Chart Type |
|---------|--------|------------|
| **Basic** | `charts-basic.py` | Line, column, pie basics |
| **Advanced** | `charts-advanced.py` | Multi-series, combo charts |
| **Extended** | `charts-extended.py` | Box-whisker, waterfall, histogram |
| **Area** | `charts-area.py` | Stacked and 100% area charts |
| **Bar** | `charts-bar.py` | Clustered, stacked, 100% bar |
| **Box-Whisker** | `charts-boxwhisker.py` | Statistical box plots |
| **Bubble** | `charts-bubble.py` | X-Y size bubble charts |
| **Column** | `charts-column.py` | Column variants |
| **Combo** | `charts-combo.py` | Mixed chart types |
| **Histogram** | `charts-histogram.py` | Frequency distributions |
| **Line** | `charts-line.py` | Line charts with markers |
| **Pie** | `charts-pie.py` | Pie and doughnut charts |
| **Radar** | `charts-radar.py` | Radar/spider charts |
| **Scatter** | `charts-scatter.py` | X-Y scatter plots |
| **Stock** | `charts-stock.py` | Stock/candlestick charts |
| **Waterfall** | `charts-waterfall.py` | Bridge/waterfall charts |

**Master Scripts**:
- `charts.sh` / `charts.xlsx`: Complete showcase of all chart types
- `charts-demo.sh` / `charts-demo.xlsx`: Interactive demo

#### Pivot Tables

| Example | Script | Demonstrates |
|---------|--------|-------------|
| **Pivot Tables** | `pivot-tables.py` | Multi-field pivots, date grouping, calculated fields |

**Key Techniques**:
- Cell formatting and number formats
- Formula evaluation (150+ functions)
- Chart generation and styling
- Pivot table creation and manipulation
- Named ranges and data validation

### 🎨 PowerPoint Examples

Located in `examples/ppt/`

| Example | Script | Demonstrates |
|---------|--------|-------------|
| **Presentation** | `presentation.sh` | Morph transitions, full deck workflow |
| **Animations** | `animations.sh` | Animation effects and timing |
| **Video** | `video.py` | Video embedding and playback |
| **3D Model** | `3d-model.sh` | 3D model (.glb) embedding |
| **Templates** | `templates/*/build.sh` | Pre-built presentation templates |

**Template Gallery** (`templates/styles/`):
- `brand--aura-coffee-dark`: Dark themed coffee brand deck
- `brand--aura-coffee`: Light coffee brand presentation
- `future--2050-vision`: Futuristic vision deck
- `lifestyle--cat-philosophy`: Playful cat philosophy deck
- `lifestyle--cat-secret-life`: Cat secret life narrative
- `lifestyle--feline-report`: Formal feline report
- `product--aionui-promo`: Product promotion deck
- `product--geminicli-timetravel`: Time travel theme
- `productivity--attention-budget`: Productivity talk
- `science--alien-guide`: Sci-fi alien guide
- `science--mars-settlement`: Mars colonization guide
- `science--space-exploration`: Space exploration history
- `science--time-travel`: Time travel presentation
- `tech--wildlife-company`: Tech company deck

**Key Techniques**:
- Slide creation and layout
- Shape manipulation and styling
- Morph transitions
- Animation effects
- Media embedding (video, 3D models)
- Master slides and themes
- Notes and comments

---

## 🔧 Key Dependencies & Configuration

### Script Requirements

**Shell Scripts (`.sh`)**:
- Bash-compatible shell
- OfficeCLI binary in PATH
- Standard Unix utilities (cat, mkdir, etc.)

**Python Scripts (`.py`)**:
- Python 3.6+
- OfficeCLI binary in PATH
- No external dependencies (uses subprocess)

### Configuration

- **Output Directory**: Scripts generate files in current directory
- **Overwrite Protection**: Many scripts check for existing files
- **Validation**: All scripts run `officecli validate` before completion

---

## 📊 Data Models

### Example Metadata

Each example includes implicit metadata:

```bash
#!/bin/bash
# Brief description of what this demonstrates
# Key techniques: list them here

set -e  # Exit on error

FILE="output.docx"
officecli create "$FILE"
# ... commands ...
officecli validate "$FILE"
echo "Created: $FILE"
```

### Template Build System

Templates use structured build scripts:

```bash
#!/bin/bash
# Metadata: theme, color scheme, slide count
set -e

SOURCE="outline.md"
OUTPUT="presentation.pptx"

# Parse outline.md and generate slides
# ... OfficeCLI commands ...

officecli validate "$OUTPUT"
```

---

## 🧪 Testing & Quality

### Test Coverage

- **Integration Tests**: All examples serve as integration tests
- **Regression Prevention**: Pre-generated outputs validate against changes
- **Documentation Accuracy**: Examples verify that documentation matches behavior

### Running Examples

```bash
# Word examples
cd examples/word
bash formulas.sh
bash tables.sh

# Excel examples
cd examples/excel
python charts-line.py
python pivot-tables.py

# PowerPoint examples
cd examples/ppt
bash presentation.sh
bash animations.sh
```

### Validation Workflow

Every example script follows this pattern:

1. Create new document (`officecli create`)
2. Add content via commands (`add`, `set`, etc.)
3. Validate structure (`officecli validate`)
4. Report success with output path

---

## 🎯 Common Patterns

### Pattern 1: Create and Populate

```bash
#!/bin/bash
set -e

FILE="document.docx"
officecli create "$FILE"
officecli add "$FILE" /body --type paragraph --prop text="Hello World"
officecli validate "$FILE"
```

### Pattern 2: Batch Operations

```bash
cat << 'EOF' > commands.json
[
  {"command":"add","parent":"/body","type":"paragraph","props":{"text":"Para 1"}},
  {"command":"set","path":"/body/p[1]","props":{"bold":"true","size":"24"}}
]
EOF
officecli batch document.docx < commands.json
```

### Pattern 3: Resident Mode (Performance)

```bash
officecli open document.docx
officecli add document.docx /body --type paragraph --prop text="Fast operation"
officecli set document.docx /body/p[1] --prop bold=true
officecli close document.docx
```

### Pattern 4: Query and Modify

```bash
# Find all Heading1 paragraphs
officecli query report.docx "paragraph[style=Heading1]" --json

# Change their color
officecli set report.docx /body/p[1] --prop color=FF0000
```

---

## 📚 Quick Reference

### Document Types

| Format | Extension | Create | View | Modify |
|--------|-----------|--------|------|--------|
| Word | .docx | ✓ | ✓ | ✓ |
| Excel | .xlsx | ✓ | ✓ | ✓ |
| PowerPoint | .pptx | ✓ | ✓ | ✓ |

### Common Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `create` | Create blank document | `officecli create file.docx` |
| `view` | View content | `officecli view file.docx text` |
| `get` | Get element | `officecli get file.docx /body/p[1]` |
| `set` | Modify element | `officecli set file.docx /body/p[1] --prop bold=true` |
| `add` | Add element | `officecli add file.docx /body --type paragraph` |
| `remove` | Remove element | `officecli remove file.docx /body/p[5]` |
| `query` | CSS-like query | `officecli query file.docx "paragraph[style=Normal]"` |
| `batch` | Multiple operations | `officecli batch file.docx < commands.json` |
| `validate` | Check schema | `officecli validate file.docx` |

### View Modes

| Mode | Description | Usage |
|------|-------------|-------|
| `text` | Plain text | `officecli view file.docx text` |
| `annotated` | Text with formatting | `officecli view file.docx annotated` |
| `outline` | Structure | `officecli view file.docx outline` |
| `stats` | Statistics | `officecli view file.docx stats` |
| `issues` | Problems | `officecli view file.docx issues` |
| `html` | HTML preview | `officecli view file.docx html` |
| `svg` | SVG preview | `officecli view file.docx svg` |
| `forms` | Form fields | `officecli view file.docx forms` |

---

## 💡 Tips for Using Examples

1. **Explore before modifying**:
   ```bash
   officecli view document.docx outline
   officecli get document.docx /body --depth 2
   ```

2. **Use `--json` for automation**:
   ```bash
   officecli query data.xlsx "cell[formula~=SUM]" --json | jq
   ```

3. **Check help for properties**:
   ```bash
   officecli help docx set paragraph
   officecli help xlsx set cell
   officecli help pptx set shape
   ```

4. **Validate after changes**:
   ```bash
   officecli validate document.docx
   ```

5. **Use resident mode for performance** (3+ operations):
   ```bash
   officecli open file.pptx
   # ... multiple commands ...
   officecli close file.pptx
   ```

---

## 🤝 Contributing Examples

### Guidelines

1. **Create script** with clear comments
2. **Test and verify** output
3. **Add to appropriate directory** (word/excel/ppt)
4. **Update directory README**
5. **Submit PR**

### Example Format

```bash
#!/bin/bash
# Brief description of what this demonstrates
# Key techniques: list them here

set -e

FILE="output.docx"
officecli create "$FILE"
# ... your commands ...
officecli validate "$FILE"
echo "Created: $FILE"
```

---

## 📚 Related Files

### Master Documentation

- `README.md` - This file, master guide for all examples

### Word Examples

- `word/formulas.sh` - LaTeX formula examples
- `word/tables.sh` - Complex table examples
- `word/textbox.sh` - Text box examples
- `word/numbering-showcase.sh` - Numbering examples

### Excel Examples

- `excel/charts.sh` - Master chart showcase
- `excel/charts-demo.sh` - Interactive demo
- `excel/charts-*.py` - Per-type chart examples (14+ files)
- `excel/pivot-tables.py` - Pivot table examples

### PowerPoint Examples

- `ppt/presentation.sh` - Full deck with morph transitions
- `ppt/animations.sh` - Animation effects
- `ppt/video.py` - Video embedding
- `ppt/3d-model.sh` - 3D model embedding
- `ppt/templates/*/build.sh` - Template build scripts (14+ templates)

---

## 🔗 External References

- **[Main Documentation](../README.md)** - Project overview and installation
- **[SKILL.md](../SKILL.md)** - Complete command reference for AI agents
- **[schemas/](../schemas/)** - JSON schema reference for all elements
- **[GitHub Issues](https://github.com/iOfficeAI/OfficeCLI/issues)** - Report problems or request examples

---

*This module contains comprehensive examples demonstrating all OfficeCLI capabilities. Use these as starting points for your own automation scripts.*
