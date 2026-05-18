[根目录](../CLAUDE.md) > **skills**

---

# OfficeCLI Skills Module

> **Module Type**: Embedded AI Skills | **Format**: Markdown (.md) + Python/Shell Scripts | **Status**: Active Collection

---

## 📋 Change Log

| Date | Change | Impact |
|------|--------|--------|
| 2026-05-18 | Initial module documentation created | Skills structure documented |

---

## 🎯 Module Responsibilities

The `skills/` module contains specialized AI skill files that extend OfficeCLI's base capabilities. These skills are:

- **Embedded** in the main binary as resources
- **Loadable** via `officecli load_skill <name>`
- **Specialized** for specific document types or workflows
- **Self-contained** with examples, templates, and reference materials

---

## 🚀 Entry Points & Usage

### Loading a Skill

```bash
# List available skills
officecli load_skill --list

# Load a specific skill (prints to stdout)
officecli load_skill morph-ppt

# Use the skill (feed to AI agent)
officecli load_skill morph-ppt > agent-context.md
```

### Skill File Structure

Each skill directory contains:

- **SKILL.md**: Main skill definition (AI agent instructions)
- **reference/**: Supporting documentation and examples
- **templates/**: Optional document templates
- **build.sh** or scripts: Example generation scripts

---

## 📦 Skill Catalog

### Morph Transitions

**Location**: `morph-ppt/`, `morph-ppt-3d/`

**Purpose**: Create PowerPoint presentations with smooth morph transitions between slides

**Features**:
- 50+ predefined design styles (dark, light, warm, vivid, mixed)
- Style reference system with decision rules
- Build scripts for style generation
- 3D model support (morph-ppt-3d)

**Key Files**:
- `SKILL.md` - Main skill instructions
- `reference/pptx-design.md` - Morph design principles
- `reference/decision-rules.md` - Style selection logic
- `reference/styles/INDEX.md` - Complete style catalog

**Usage Example**:
```bash
officecli load_skill morph-ppt
# AI agent learns morph workflow and style system
```

### Document Type Skills

**Location**: `officecli-*/`

**Skills Available**:
- `officecli-academic-paper` - Academic paper formatting (Word)
- `officecli-data-dashboard` - Data dashboard creation (Excel)
- `officecli-docx` - General Word document skills
- `officecli-financial-model` - Financial modeling (Excel)
- `officecli-pitch-deck` - Investor pitch decks (PowerPoint)

**Usage Example**:
```bash
officecli load_skill officecli-pitch-deck
# Agent learns to create investor-ready presentations
```

---

## 🔧 Style Reference System

### Style Categories

**Dark Styles** (`dark--*`):
- Architectural plan, blueprint grid, circle digital, cosmic neon
- Cyber future, diagonal cut, editorial story, investor pitch
- Liquid flow, luxury minimal, midnight blueprint, neon productivity
- Obsidian amber, premium navy, sage grain, space odyssey
- Spotlight stage, velvet rose

**Light Styles** (`light--*`):
- Bold type, firmwise saas, fluid gradient, glassmorphism vc
- Isometric clean, minimal corporate, minimal product
- Project proposal, spring launch, training interactive
- Watercolor wash

**Warm Styles** (`warm--*`):
- Bloom academy, brand refresh, coral culture, earth organic
- Monument editorial, playful organic, sunset mosaic, vital bloom

**Vivid Styles** (`vivid--*`):
- Bauhaus electric, candy stripe, energy neon, pink editorial
- Playful marketing

**Mixed Styles** (`mixed--*`):
- Bauhaus blocks, chromatic aberration, duotone split, spectral grid

**Black & White Styles** (`bw--*`):
- Brutalist raw, mono line, swiss bauhaus, swiss system

### Style File Structure

Each style in `morph-ppt/reference/styles/` contains:

- **style.md**: Style description, color palette, typography
- **build.sh**: Script to generate example .pptx
- **<name>.pptx**: Pre-built example (optional)
- **template.pptx**: Reusable template (some styles)

---

## 📊 Skill Metadata

### Skill Manifest

Each `SKILL.md` file includes YAML frontmatter:

```yaml
---
name: officecli-pitch-deck
description: Create investor-ready pitch decks
version: 1.0.0
formats: [pptx]
platforms: [all]
requires: [officecli]
---
```

### Loading Mechanism

Skills are embedded in the main binary:

```xml
<EmbeddedResource Include="../../skills/**/*" 
  LogicalName="skills/$([System.String]::Copy('%(RecursiveDir)').Replace('\','/'))%(Filename)%(Extension)" />
```

Accessed via `Assembly.GetManifestResourceStream()`.

---

## 🧪 Testing & Quality

### Skill Validation

- **Syntax Check**: Valid Markdown frontmatter
- **Load Test**: `officecli load_skill <name>` succeeds
- **Example Generation**: `build.sh` produces valid output
- **Documentation**: Complete reference materials

### Manual Testing

```bash
# Test skill loading
officecli load_skill morph-ppt | head -20

# Test example generation
cd skills/morph-ppt/reference/styles/dark--cyber-future
bash build.sh
# Should generate: dark__cyber_future.pptx

# Validate output
officecli validate dark__cyber_future.pptx
```

---

## 🔨 Common Development Tasks

### Creating a New Skill

1. **Create directory**: `skills/<skill-name>/`
2. **Write SKILL.md**: Define skill purpose and workflow
3. **Add reference/**: Supporting docs and examples
4. **Create templates/**: Optional starter documents
5. **Test loading**: `officecli load_skill <skill-name>`
6. **Update catalog**: Add to root SKILL.md skills list

**Example SKILL.md structure**:

```markdown
---
name: my-custom-skill
description: Specialized workflow for X
version: 1.0.0
---

# My Custom Skill

## Purpose
Brief description of what this skill does.

## Prerequisites
- OfficeCLI installed
- Required templates

## Workflow
Step-by-step instructions for AI agents.

## Examples
```bash
officecli create output.docx
officecli add output.docx /body --type paragraph --prop text="Example"
```

## Reference
Link to detailed documentation.
```

### Adding a New Morph Style

1. **Create style directory**: `morph-ppt/reference/styles/<category>--<name>/`
2. **Write style.md**: Define colors, fonts, layout
3. **Create build.sh**: Generate example .pptx
4. **Test generation**: Run build script and validate output
5. **Update INDEX.md**: Add to style catalog

**Example style.md**:

```markdown
# Style: Cyber Future

## Color Palette
- Background: #0A0E27
- Accent 1: #00F0FF (Cyan)
- Accent 2: #FF006E (Magenta)
- Text: #FFFFFF

## Typography
- Headings: Orbitron, 48pt, Bold
- Body: Roboto, 18pt, Regular

## Layout
- Slide margins: 1cm
- Content alignment: Center
- Shape fills: Gradient (Accent 1 → Accent 2)

## Effects
- Glow: Accent 1, 15pt radius
- Shadow: Soft shadow, 10pt offset
```

---

## ❓ FAQ

### Q: How are skills different from the main SKILL.md?

A: The root `SKILL.md` is a general reference for all OfficeCLI commands. Individual skills in `skills/` are specialized workflows that teach agents how to accomplish specific tasks (e.g., create pitch decks, use morph transitions, format academic papers).

### Q: Can I create my own skills?

A: Yes. Create a new directory under `skills/`, add a `SKILL.md` file, and optionally include reference materials and templates. Use `officecli load_skill <your-skill>` to load it. Skills can be contributed back to the project via pull request.

### Q: How do styles work in morph-ppt?

A: Styles are predefined design systems (colors, fonts, layouts, effects). The `decision-rules.md` file helps agents choose appropriate styles based on content type, audience, and mood. Each style has a corresponding `.pptx` example generated by its `build.sh` script.

### Q: What's the difference between morph-ppt and morph-ppt-3d?

A: `morph-ppt` focuses on 2D morph transitions between slides. `morph-ppt-3d` extends this with 3D model support (.glb files), allowing objects to move, rotate, and transform in 3D space during morph transitions.

### Q: How are skills embedded in the binary?

A: Skills are embedded as .NET resources during the build process (see `officecli.csproj`). The `SkillInstaller` class reads them via `Assembly.GetManifestResourceStream()` and makes them available via the `load_skill` command.

---

## 📚 Related Files

### Core Skills

- `../SKILL.md` - Root skill file (general OfficeCLI reference)
- `morph-ppt/SKILL.md` - Morph transitions workflow
- `morph-ppt-3d/SKILL.md` - 3D morph transitions
- `officecli-*/SKILL.md` - Document-type specific skills

### Reference Materials

- `morph-ppt/reference/pptx-design.md` - Morph design principles
- `morph-ppt/reference/decision-rules.md` - Style selection logic
- `morph-ppt/reference/morph-helpers.py` - Python utilities
- `morph-ppt/reference/morph-helpers.sh` - Shell utilities

### Style Catalog

- `morph-ppt/reference/styles/INDEX.md` - Complete style listing
- `morph-ppt/reference/styles/*/style.md` - Individual style definitions
- `morph-ppt/reference/styles/*/build.sh` - Style generation scripts

---

## 🔗 External References

- **Morph Transitions**: https://support.microsoft.com/en-us/topic/use-morph-transitions-in-powerpoint-1c02a24b-4366-4497-8b4c-7d73c03df976
- **3D Models in PowerPoint**: https://support.microsoft.com/en-us/topic/insert-3d-models-in-powerpoint-c181746c-7150-4be9-85a4-f0c767f6e72e
- **PowerPoint Design Best Practices**: https://support.microsoft.com/en-us/topic/design-tips-for-powerpoint-presentations-023257d2-0ebc-4d0b-950a-eea4aec90c78

---

*Skills are how OfficeCLI teaches AI agents specialized workflows. Each skill is a self-contained learning module with examples, templates, and reference materials.*
