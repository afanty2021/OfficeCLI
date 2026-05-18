[根目录](../CLAUDE.md) > **docs**

---

# OfficeCLI Documentation Module

> **Module Type**: Technical Specification | **Language**: Markdown | **Status**: Active

---

## 📋 Change Log

| Date | Change | Impact |
|------|--------|--------|
| 2026-05-18 | Initial module documentation created | Plugin protocol documented |

---

## 🎯 Module Responsibilities

The `docs/` module serves as:

- **Plugin Protocol Specification**: Complete protocol definition for external plugins
- **Technical Architecture**: Deep-dive documentation for advanced users
- **Extension Guidelines**: How to extend OfficeCLI via plugins
- **Integration Reference**: For tool authors integrating OfficeCLI

---

## 🚀 Entry Points & Structure

### Documentation Files

Located in `docs/` directory:

| Document | Purpose | Audience |
|----------|---------|----------|
| **plugin-protocol.md** | Complete plugin protocol specification | Plugin authors, contributors |

### Documentation Organization

```
docs/
└── plugin-protocol.md           # Complete v1 plugin protocol specification
```

---

## 🔌 Public Interface (Specifications)

### Plugin Protocol Specification

**File**: `plugin-protocol.md`

#### Document Sections

1. **Motivation** (§1)
   - Why plugins exist (legacy formats, regional formats, export targets)
   - Drivers for external architecture

2. **Plugin Kinds** (§2)
   - `dump-reader`: Read foreign formats, emit officecli commands
   - `exporter`: Convert native formats to foreign targets
   - `format-handler`: Own foreign formats end-to-end
   - Reserved kinds for future use

3. **Plugin Discovery** (§3)
   - Four-path discovery system (environment → user → bundled → PATH)
   - Naming conventions and path resolution
   - Caching and invalidation

4. **Manifest** (§4)
   - Required fields (name, version, protocol, kinds, extensions)
   - Idle timeout configuration
   - Vocabulary declaration (format-handler)
   - Example manifests

5. **Invocation** (§5)
   - Per-kind command-line interfaces
   - IPC patterns (stdin/stdout for format-handler)
   - Open handshake and session lifecycle
   - Cross-runtime conventions

6. **IPC Protocol** (§6)
   - Transport (stdin/stdout/stderr)
   - Framing and concurrency
   - Message envelopes and types
   - Exit codes and error codes
   - Session state machine

7. **Vocabulary Contract** (§7)
   - Universal protocol shell
   - Per-format vocabulary
   - Extension points

8. **Installation** (§8)
   - Installation mechanisms
   - Plugin registry
   - Package manager integration

9. **Writing a Plugin** (§9)
   - Minimum dump-reader (C#)
   - Minimum exporter (Go)
   - Minimum format-handler (C#)

10. **Stability Commitments** (§10)
    - Main → Plugins stability guarantees
    - Plugins → Main expectations

11. **FAQ** (§11)
    - Common questions about plugins

12. **Versioning** (§12)
    - Protocol versioning strategy
    - Compatibility requirements

13. **Open Questions** (§13)
    - Post-v1 considerations

---

## 🔧 Key Dependencies & Concepts

### Plugin System Architecture

**Three Plugin Kinds**:

1. **dump-reader**:
   - **Purpose**: Migrate foreign formats to native formats
   - **Lifecycle**: Short-lived (one shot)
   - **IPC**: JSONL over stdout
   - **Output**: Native format file (.docx/.xlsx/.pptx)

2. **exporter**:
   - **Purpose**: Convert native formats to foreign targets
   - **Lifecycle**: Short-lived
   - **IPC**: None (CLI invocation)
   - **Output**: Foreign format file (.pdf, .epub, etc.)

3. **format-handler**:
   - **Purpose**: First-class support for foreign formats
   - **Lifecycle**: Long-lived (session duration)
   - **IPC**: stdin/stdout JSONL frames
   - **Output**: Plugin-defined vocabulary

### Protocol Versioning

- **Current Version**: v1
- **Stability**: No backward-compatibility goal in pre-release
- **Version Gate**: Main rejects plugins with mismatched protocol major version
- **Evolution**: Additive changes in v1.x, breaking changes require v2

### Discovery Order

Plugins are discovered in this fixed order (first match wins):

1. **Environment Variable**: `$OFFICECLI_PLUGIN_<KIND>_<EXT>`
2. **User Directory**: `~/.officecli/plugins/<kind>/<ext>/plugin(.exe)`
3. **Bundled Directory**: `<binary-dir>/plugins/<kind>/<ext>/plugin(.exe)`
4. **PATH Lookup**: `officecli-<kind>-<ext>` or `officecli-<ext>`

---

## 📊 Data Models & Protocols

### Manifest Schema

```json
{
  "name": "officecli-doc",
  "version": "1.0.0",
  "protocol": 1,
  "kinds": ["dump-reader"],
  "extensions": [".doc"],
  "target": "docx",
  "runtime": "dotnet",
  "idle_timeout_seconds": {
    "default": 60,
    "verbs": {
      "dump": 30
    }
  },
  "description": "Read legacy .doc files",
  "tier": "basic",
  "supports": ["paragraphs", "runs", "tables"],
  "limits": {
    "maxFileSizeMb": 200
  },
  "homepage": "https://github.com/example/officecli-doc",
  "license": "Apache-2.0"
}
```

### IPC Message Envelope

All messages use this envelope:

```json
{
  "protocol": 1,
  "msg_type": "<type>",
  ... type-specific fields ...
}
```

**Request Types**:
- `open`: Handshake (format-handler)
- `command`: Execute verb (add/set/get/etc.)
- `save`: Flush writes (format-handler)
- `close`: End session
- `ping`: Liveness check

**Response Types**:
- `ok`: Success with result
- `error`: Failure with code/message

### Session State Machine

```
(none) → spawning → ready → busy → (ready|broken) → closed
```

**Transitions**:
- **spawning → ready**: Successful open handshake
- **ready ↔ busy**: Command request/reply
- **busy → ready**: Command reply received
- **any → broken**: IO failure / timeout / malformed reply
- **any → closed**: Dispose / Kill

---

## 🧪 Testing & Validation

### Plugin Validation

**Manifest Validation**:
```bash
# Test plugin manifest
plugin --info
```

**Protocol Validation**:
```bash
# Test dump-reader
officecli create test.docx
plugin dump source.doc | officecli batch test.docx -

# Test exporter
officecli view test.docx pdf --out output.pdf

# Test format-handler
officecli open test.hwpx
officecli get test.hwpx /
officecli close test.hwpx
```

### Session Testing

**Format-Handler Session**:
1. Spawn plugin with `open <file>`
2. Verify handshake reply (capabilities + vocabulary)
3. Send test commands (get/set/add/etc.)
4. Verify replies match expected results
5. Send `close` and verify clean exit

### Error Handling

**Exit Codes**:
- `0`: Success
- `2`: Corrupt input file
- `3`: Feature unsupported
- `4`: License expired
- `5`: Protocol mismatch
- `6`: Idle timeout
- `64-78`: Reserved (sysexits.h)
- Other: Internal error

**Error Codes** (in `error.code` field):
- `invalid_request`: Malformed message
- `unsupported_command`: Unimplemented command
- `invalid_argument`: Argument validation failed
- `not_found`: Target element missing
- `corrupt_input`: Source file malformed
- `corrupt_batch`: Invalid JSONL
- `license_expired`: Commercial license check failed
- `protocol_mismatch`: Version mismatch
- `plugin_idle_timeout`: Watchdog fired
- `plugin_stream_closed`: IPC failure
- `internal_error`: Catch-all

---

## 🎯 Common Use Cases

### Use Case 1: Legacy Format Migration

**Scenario**: Convert old .doc files to .docx

**Solution**: dump-reader plugin

```bash
# Plugin emits officecli commands
officecli-doc dump old.doc | officecli batch new.docx -
```

**Flow**:
1. User opens `.doc` file
2. Main checks for cached `.docx` sibling
3. If missing/stale, spawns dump-reader plugin
4. Plugin streams `add`/`set` commands as JSONL
5. Main replays into new `.docx` file
6. Subsequent edits target `.docx` file

### Use Case 2: PDF Export

**Scenario**: Export .docx to PDF

**Solution**: exporter plugin

```bash
officecli view document.docx pdf --out document.pdf
```

**Flow**:
1. User invokes `view pdf` mode
2. Main resolves to exporter plugin
3. Main spawns plugin with source + target paths
4. Plugin reads .docx (read-only), writes .pdf
5. Plugin exits 0 on success

### Use Case 3: Regional Format Support

**Scenario**: Support Korean .hwpx format

**Solution**: format-handler plugin

```bash
officecli open document.hwpx
officecli get document.hwpx /
officecli set document.hwpx /body/p[1] --prop bold=true
officecli close document.hwpx
```

**Flow**:
1. User opens `.hwpx` file
2. Main spawns format-handler plugin
3. Open handshake: plugin declares capabilities + vocabulary
4. Main wraps plugin in `FormatHandlerProxy`
5. All commands proxied via stdin/stdout
6. `close` flushes writes and terminates plugin

---

## 💡 Implementation Guidelines

### Writing a dump-reader Plugin

**Requirements**:
- Stream JSONL output (one `BatchItem` per line)
- Flush each line individually
- Use main's target vocabulary (no custom commands)
- Exit 0 on success

**Example** (C#):
```csharp
// Parse source file
foreach (var element in sourceFile.Elements) {
    var batchItem = new {
        command = "add",
        parent = "/body",
        type = element.Type,
        props = element.Properties
    };
    Console.WriteLine(JsonSerializer.Serialize(batchItem));
    Console.Out.Flush();  // Critical: reset idle watchdog
}
return 0;
```

### Writing an exporter Plugin

**Requirements**:
- Source file is read-only (MUST NOT modify)
- Write target file to specified path
- Emit diagnostics to stderr
- Exit 0 on success

**Example** (Go):
```go
// Source is read-only - do NOT modify
source := os.Args[2]
target := getOutPath()

// Heartbeat for long jobs
go func() {
    for {
        fmt.Fprintln(os.Stderr, `{"heartbeat":true}`)
        time.Sleep(20 * time.Second)
    }
}()

// Convert and write
cmd := exec.Command("libreoffice", "--headless", "--convert-to", "pdf",
    "--outdir", filepath.Dir(target), source)
cmd.Run()
```

### Writing a format-handler Plugin

**Requirements**:
- Long-lived process (session duration)
- Read requests from stdin, write replies to stdout
- Implement open handshake
- Support proxied verbs
- Flush on `save` (normative)
- Exit on `close`

**Example** (C#):
```csharp
while (true) {
    var line = stdin.ReadLine();
    if (line == null) break;
    var msg = JsonNode.Parse(line);
    
    switch ((string)msg["msg_type"]) {
        case "open":
            // Load file, return capabilities + vocabulary
            stdout.WriteLine(OpenHandshakeReply());
            break;
        case "command":
            // Execute command, return result
            stdout.WriteLine(ExecuteCommand(msg));
            break;
        case "save":
            // MUST flush to disk before replying
            File.WriteAllBytes(filePath, currentBytes);
            stdout.WriteLine(SaveReply());
            break;
        case "close":
            stdout.WriteLine(CloseReply());
            return 0;
    }
}
```

---

## 🔍 Deep Dive: Plugin System

### Architecture

**Plugin → Main Communication**:
- **dump-reader**: Plugin → Main (stdout JSONL)
- **exporter**: Plugin → File (write target)
- **format-handler**: Plugin ↔ Main (bidirectional JSONL)

**Main → Plugin Communication**:
- **dump-reader**: Main spawns plugin, reads stdout
- **exporter**: Main spawns plugin with paths
- **format-handler**: Main writes stdin, reads stdout

### Idle Timeout Watchdog

**Purpose**: Detect hung plugins without false positives

**Mechanism**:
- Watchdog thread monitors each plugin process
- Any byte on stdout resets idle timer
- Heartbeat lines on stderr reset idle timer
- Kill process when `now - last_activity > idle_timeout`

**Heartbeat Format**:
```json
{"heartbeat":true}
```

**Override**: `OFFICECLI_PLUGIN_IDLE_TIMEOUT_SECONDS=0` disables watchdog

### Cross-Runtime Conventions

To ensure .NET / Go / Rust / Python plugins work together:

1. **UTF-8 without BOM** on stdout/stderr
2. **`\n`** line separator on all platforms (including Windows)
3. **snake_case** for all JSON keys
4. **Documented exit codes** (avoid custom codes)

---

## 📚 Related Files

### Protocol Specification

- `plugin-protocol.md` - Complete v1 protocol specification (13 sections)

### Core Implementation

- `src/officecli/Core/Plugins/PluginRegistry.cs` - Plugin discovery
- `src/officecli/Core/Plugins/PluginProcess.cs` - Process management
- `src/officecli/Core/Plugins/FormatHandlerProxy.cs` - Long-lived plugin proxy
- `src/officecli/Core/Plugins/DumpReaderInvoker.cs` - dump-reader invocation
- `src/officecli/Core/Plugins/ExporterInvoker.cs` - exporter invocation

### Plugin Manifest

- `src/officecli/Core/Plugins/PluginManifest.cs` - Manifest data model

---

## 🔗 External References

- **[Main Documentation](../README.md)** - Project overview
- **[Core Module](../src/officecli/CLAUDE.md)** - Core application architecture
- **[GitHub Repository](https://github.com/iOfficeAI/OfficeCLI)** - Source code
- **[Plugin Issues](https://github.com/iOfficeAI/OfficeCLI/issues)** - Report problems

---

## 🚀 Getting Started with Plugins

### For Plugin Authors

1. **Read the protocol**: Start with `plugin-protocol.md`
2. **Choose your kind**: dump-reader / exporter / format-handler
3. **Implement manifest**: Respond to `--info` with valid JSON
4. **Implement commands**: Follow protocol specification
5. **Test discovery**: Use `officecli plugins list`
6. **Test invocation**: Use format-specific commands

### For Users

1. **Install plugins**: `officecli plugins install <name>`
2. **List plugins**: `officecli plugins list`
3. **Use plugins**: Commands work transparently
4. **Debug issues**: Check `--verbose` output

### For Contributors

1. **Understand architecture**: Read core module documentation
2. **Review protocol**: Study `plugin-protocol.md`
3. **Implement changes**: Update core + protocol in sync
4. **Test compatibility**: Verify existing plugins still work
5. **Document changes**: Update protocol version if needed

---

*This module contains the complete plugin protocol specification for extending OfficeCLI. Plugins enable support for legacy formats, regional formats, and export targets without bloating the main binary.*
