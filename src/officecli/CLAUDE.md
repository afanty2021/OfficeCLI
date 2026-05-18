[根目录](../CLAUDE.md) > **src/officecli**

---

# OfficeCLI Core Module

> **Module Type**: Core Application | **Language**: C# (.NET 10.0) | **Status**: Active Development

---

## 📋 Change Log

| Date | Change | Impact |
|------|--------|--------|
| 2026-05-18 | Initial module documentation created | Core structure documented |

---

## 🎯 Module Responsibilities

The `src/officecli` module is the heart of OfficeCLI, containing:

- **CLI Application Entry Point**: Main program initialization and command routing
- **Command Builder System**: All user-facing commands (create, view, add, set, remove, etc.)
- **Core Business Logic**: Document handling, formula evaluation, chart generation, pivot tables
- **Plugin Architecture**: Extensibility system for foreign formats
- **Rendering Engine**: HTML/PNG preview generation
- **Schema Help System**: Built-in documentation for all element types

---

## 🚀 Entry Points & Startup

### Application Entry

- **File**: `Program.cs` (implicit in .NET console apps)
- **Assembly**: `officecli` (output: `officecli` or `officecli.exe`)
- **Target Framework**: .NET 10.0
- **Runtime**: Self-contained (embedded .NET runtime)

### Build Configuration

```xml
<TargetFramework>net10.0</TargetFramework>
<OutputType>Exe</OutputType>
<PublishSingleFile>true</PublishSingleFile>
<SelfContained>true</SelfContained>
<RuntimeIdentifier>osx-arm64|linux-x64|win-x64</RuntimeIdentifier>
```

### Main Command Router

- **Class**: `CommandBuilder` (CommandBuilder.cs)
- **Pattern**: System.CommandLine with subcommand tree
- **Root Command**: `officecli [subcommand] [arguments]`

---

## 🔌 Public Interface (CLI Commands)

### Document Lifecycle Commands

| Command | Handler | Purpose |
|---------|---------|---------|
| `create` | `CommandBuilder.Create()` | Create blank .docx/.xlsx/.pptx |
| `open` | `CommandBuilder.Open()` | Start resident mode |
| `close` | `CommandBuilder.Close()` | Save and close resident mode |
| `validate` | `CommandBuilder.Validate()` | Check against OpenXML schema |

### Read Commands (L1)

| Command | Handler | Purpose |
|---------|---------|---------|
| `view` | `CommandBuilder.View()` | Semantic views (text/annotated/outline/stats/issues/html/screenshot) |
| `get` | `CommandBuilder.GetQuery()` | Get element with children |
| `query` | `CommandBuilder.GetQuery()` | CSS-like queries |
| `raw` | `CommandBuilder.Raw()` | View raw XML of document part |

### Modify Commands (L2)

| Command | Handler | Purpose |
|---------|---------|---------|
| `set` | `CommandBuilder.Set()` | Modify element properties |
| `add` | `CommandBuilder.Add()` | Add new element |
| `remove` | `CommandBuilder.Remove()` | Delete element |
| `move` | `CommandBuilder.Goto()` | Move element to new parent/position |
| `swap` | `CommandBuilder.*` | Swap two elements |
| `batch` | `CommandBuilder.Batch()` | Multiple operations in one open/save |

### Advanced Commands (L3)

| Command | Handler | Purpose |
|---------|---------|---------|
| `raw-set` | `CommandBuilder.Raw()` | Modify raw XML via XPath |
| `add-part` | `CommandBuilder.Raw()` | Add new document part |
| `dump` | `CommandBuilder.Dump()` | Serialize document to replayable JSON |
| `refresh` | `CommandBuilder.Refresh()` | Recalculate TOC/fields (Word) |
| `merge` | `CommandBuilder.*` | Template merge (`{{key}}` placeholders) |

### Integration Commands

| Command | Handler | Purpose |
|---------|---------|---------|
| `install` | `Core/Installer.cs` | Install binary + skills + MCP |
| `mcp` | `CommandBuilder.Plugins()` | Start MCP server |
| `load_skill` | `CommandBuilder.Help()` | Print embedded skill content |
| `watch` | `CommandBuilder.Watch()` | Live HTML preview server |

---

## 🔧 Key Dependencies & Configuration

### NuGet Packages

```xml
<PackageReference Include="DocumentFormat.OpenXml" Version="3.4.1" />
<PackageReference Include="OpenMcdf" Version="3.1.3" />
<PackageReference Include="System.CommandLine" Version="3.0.0-preview.2.26159.112" />
```

### Embedded Resources

- **CSS/JS**: `Resources/preview.css`, `Resources/preview.js`, `Resources/watch-*.js`
- **Skills**: `skills/**/*` (all skill files embedded)
- **Schemas**: `schemas/help/**/*.json` (help system)
- **Chart Resources**: `Resources/chartex-*.xml`, `Resources/cx-gallery/**`

### Configuration Files

- **Runtime Config**: `~/.officecli/config.json` (user settings)
- **Skill Files**: `SKILL.md` (root) + `skills/*/SKILL.md`
- **Help Schemas**: `schemas/help/<format>/<element>.json`

---

## 📊 Data Models

### DocumentNode

Core abstraction for Office document elements:

```csharp
public class DocumentNode
{
    public string Tag { get; set; }           // Element type (paragraph, shape, cell, etc.)
    public string Path { get; set; }          // /slide[N]/shape[M] format
    public Dictionary<string, string> Attributes { get; set; }  // Element properties
    public List<DocumentNode> Children { get; set; }  // Child elements
}
```

### BatchItem

Atomic operation for batch mode:

```csharp
public class BatchItem
{
    public string Command { get; set; }       // add, set, remove, move, etc.
    public string Path { get; set; }          // Target path
    public string ParentPath { get; set; }    // For add/move
    public string Type { get; set; }          // Element type (for add)
    public Dictionary<string, string> Props { get; set; }  // Properties
    public string Position { get; set; }      // Index/after/before
}
```

### PluginManifest

Plugin metadata (see `Core/Plugins/PluginManifest.cs`):

```csharp
public class PluginManifest
{
    public string Name { get; set; }
    public string Version { get; set; }
    public int Protocol { get; set; }         // Plugin protocol version
    public string[] Kinds { get; set; }       // dump-reader, exporter, format-handler
    public string[] Extensions { get; set; }  // .doc, .pdf, etc.
    public IdleTimeout IdleTimeoutSeconds { get; set; }
}
```

---

## 🧪 Testing & Quality

### Test Location

- **Unit Tests**: Embedded in command handlers (see individual CommandBuilder files)
- **Integration Tests**: `examples/` directory (shell/Python scripts)
- **Schema Tests**: `schemas/help/` validated on build

### Quality Tools

- **Validation**: `officecli validate <file>` checks OpenXML compliance
- **Issue Detection**: `officecli view <file> issues` finds common problems
- **Help System**: `officecli help <format> <element>` validates schemas

### Manual Testing Workflow

```bash
# 1. Create test document
officecli create test.docx

# 2. Add content
officecli add test.docx /body --type paragraph --prop text="Test"

# 3. Validate structure
officecli validate test.docx

# 4. Check for issues
officecli view test.docx issues --json
```

---

## 🔍 Core Subsystems

### Format Handlers

Located in `Core/` with per-format implementations:

- **Word**: `WordHandler.cs` (implicit, via DocumentFormat.OpenXml)
- **Excel**: `ExcelHandler.cs` + `Formula/` subsystem
- **PowerPoint**: `PowerPointHandler.cs` + `Chart/` subsystem

### Formula Engine

**Location**: `Core/Formula/`

- **FormulaParser.cs**: Parse Excel formulas to AST
- **FormulaEvaluator.cs**: Evaluate 150+ built-in functions
- **ModernFunctionQualifier.cs**: Auto-prefix `_xlfn.` for dynamic array functions
- **FormulaRefShifter.cs**: Adjust references on copy/move

### Chart Engine

**Location**: `Core/Chart/`

- **ChartHelper.cs**: Main chart builder/reader
- **ChartExBuilder.cs**: Extended chart types (box-whisker, waterfall, etc.)
- **ChartSvgRenderer.cs**: SVG rendering for HTML preview
- **ChartPresets.cs**: Predefined chart styles

### Pivot Table Engine

**Location**: `Core/PivotTableHelper.cs`

- **Cache.cs**: Pivot cache definition
- **Definition.cs**: Pivot table structure
- **Render.cs**: Generate pivot table OOXML
- **Set.cs**: Modify pivot table properties

### Plugin System

**Location**: `Core/Plugins/`

- **PluginRegistry.cs**: Discover and load plugins
- **PluginProcess.cs**: Spawn and manage plugin subprocesses
- **FormatHandlerProxy.cs**: Proxy for long-lived format-handler plugins
- **DumpReaderInvoker.cs**: Invoke dump-reader plugins
- **ExporterInvoker.cs**: Invoke exporter plugins

### Rendering Engine

**Location**: `Core/HtmlPreviewHelper.cs`, `Core/HtmlScreenshot.cs`

- **HTML Generation**: Convert OOXML to standalone HTML
- **PNG Screenshot**: Headless browser screenshots via embedded resources
- **Live Preview**: `watch` command with SSE auto-refresh

---

## 🔨 Common Development Tasks

### Adding a New CLI Command

1. Create `CommandBuilder.<CommandName>.cs`
2. Add command in `CommandBuilder.cs` root router
3. Implement handler logic
4. Add JSON schema to `schemas/help/`
5. Update examples and documentation

**Example structure**:

```csharp
// CommandBuilder.MyCommand.cs
public static Command MyCommand()
{
    var command = new Command("mycommand", "Description");
    var fileArg = new Argument<FileInfo>("file");
    var opt = new Option<string>("--opt");
    
    command.SetHandler((file, opt) => {
        // Implementation
    }, fileArg, opt);
    
    return command;
}
```

### Adding a New Element Type

1. Define element in format handler (Word/Excel/PowerPoint)
2. Create JSON schema: `schemas/help/<format>/<element>.json`
3. Implement add/set/get logic in handler
4. Add examples and tests

### Extending the Plugin System

1. Define new plugin kind in `Core/Plugins/PluginManifest.cs`
2. Implement invoker in `Core/Plugins/`
3. Update protocol documentation
4. Add example plugin

---

## ❓ FAQ (Frequently Asked Questions)

### Q: How does resident mode work?

A: Resident mode keeps the document in memory using named pipes (Unix) or shared memory (Windows). The first `open` loads the document; subsequent commands operate on the in-memory copy; `close` flushes changes to disk. This avoids repeated file I/O for multi-step workflows.

### Q: Why are some commands in separate files?

A: Commands are split into separate `CommandBuilder.<Command>.cs` files for maintainability. Each file registers its command with the root router in `CommandBuilder.cs`. This keeps the codebase organized as the command set grows.

### Q: How does the formula engine handle cell references?

A: Formula parsing and evaluation happens in `Core/Formula/`. References like `A1` or `Sheet2!B5` are resolved to cell values, with support for ranges (`A1:A10`), intersections (`SUM(A1:B5 A3:B7)`), and external references. The evaluator automatically recalculates dependencies.

### Q: What's the difference between `view` and `get`?

A: `view` provides high-level semantic views (text, outline, stats, issues) designed for human or agent consumption. `get` returns structured `DocumentNode` objects with full element hierarchy, suitable for programmatic manipulation.

### Q: How are plugins discovered and invoked?

A: Plugins are discovered via environment variables, user directories (`~/.officecli/plugins/`), bundled directories (`plugins/` next to binary), or PATH lookup. Each plugin kind (dump-reader, exporter, format-handler) has a specific invocation pattern and IPC contract (see `docs/plugin-protocol.md`).

---

## 📚 Related Files

### Core Command Files

- `CommandBuilder.cs` - Root command router
- `CommandBuilder.Add.cs` - Add elements
- `CommandBuilder.Set.cs` - Modify elements
- `CommandBuilder.GetQuery.cs` - Get/query elements
- `CommandBuilder.View.cs` - View commands
- `CommandBuilder.Batch.cs` - Batch operations
- `CommandBuilder.Raw.cs` - Raw XML access
- `CommandBuilder.Watch.cs` - Live preview server

### Core Business Logic

- `Core/IDocumentHandler.cs` - Document handler interface
- `Core/DocumentNode.cs` - Universal element model
- `Core/CliException.cs` - Structured error handling
- `Core/OfficeCliMetadata.cs` - Assembly metadata

### Format-Specific Logic

- `Core/Chart/` - Chart generation and rendering
- `Core/Formula/` - Excel formula evaluation
- `Core/PivotTableHelper.cs` - Pivot table generation
- `Core/ExcelStyleManager.cs` - Excel style handling
- `Core/LocaleFontRegistry.cs` - i18n font management

### Plugin System

- `Core/Plugins/PluginRegistry.cs` - Plugin discovery
- `Core/Plugins/PluginProcess.cs` - Process management
- `Core/Plugins/FormatHandlerProxy.cs` - Long-lived plugin proxy
- `Core/Plugins/DumpReaderInvoker.cs` - Dump-reader invocation
- `Core/Plugins/ExporterInvoker.cs` - Exporter invocation

---

## 🔗 External References

- **OpenXML SDK**: https://learn.microsoft.com/en-us/office/open-xml/
- **System.CommandLine**: https://learn.microsoft.com/en-us/dotnet/standard/commandline/
- **Plugin Protocol**: [../../docs/plugin-protocol.md](../../docs/plugin-protocol.md)
- **Schema System**: [../../schemas/README.md](../../schemas/README.md)

---

*This module is the core of OfficeCLI. All document operations flow through these handlers. For format-specific details, see the individual format handlers in the `Core/` directory.*
