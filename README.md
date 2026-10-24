# CodeLanguage

[English](README.md) | [中文文档](README_CN.md)

A tree-sitter based syntax highlighting and language detection library for the FoxTerm APP. Provides incremental parsing, per-language highlight queries, and intelligent language detection from filenames, shebangs, and content.

> This library is a dedicated core component for the [FoxTerm](https://foxterm.app/) APP. Download it here: [https://foxterm.app/](https://foxterm.app/)

## Features

- **Tree-sitter Parsing** — Incremental parsing with per-language grammar bindings (Bash, Dockerfile, INI, JSON, Markdown, SSH Config, TOML, XML, YAML)
- **Syntax Highlighting** — Thread-safe global query cache with per-language `.scm` highlight queries
- **Language Detection** — Detect language from filename, path extension, shebang, first-line heuristics, or full content scan
- **File Extension Mapping** — Comprehensive mapping of file extensions and special filenames (`.bashrc`, `Makefile`, `Dockerfile`, `.gitignore`, etc.)
- **Memory Management** — Manual cache purge API for memory warnings and background transitions
- **SwiftUI Ready** — Lightweight, pure Swift API with no UI dependencies

## Requirements

| Platform | Minimum Version |
|----------|----------------|
| iOS      | 17.0           |
| macOS    | 14.0           |
| Swift    | 5.10           |

## Installation

### Swift Package Manager

```swift
.package(url: "https://github.com/foxterm/CodeLanguage.git", branch: "main"),
```

```swift
.product(name: "CodeLanguage", package: "CodeLanguage"),
```

### Dependencies

- [tree-sitter](https://github.com/tree-sitter/tree-sitter.git) — Incremental parsing library
- [Sync](https://github.com/foxterm/Sync) — Concurrency synchronization primitives
- [Extension](https://github.com/foxterm/Extension) — Foundation extensions

## Usage

### Language Detection

```swift
import CodeLanguage

// Detect from filename
let lang = CodeLanguage.from(fileName: "config.yaml")  // .yaml

// Detect from path
let lang = CodeLanguage.from(fileName: "/etc/ssh/sshd_config")  // .ssh_config

// Detect from content
let lang = CodeLanguage.from(content: "#!/bin/bash\necho hello")  // .bash

// Combined detection (filename + content fallback)
let lang = CodeLanguage.from(name: "Makefile", content: nil)  // .bash
```

### Syntax Highlighting

```swift
import CodeLanguage

let code = #"{"key": "value"}"#
let language = CodeLanguage.json

language.highlights(code: code, encoding: .utf8) { capture in
    // capture.name: "string", "number", "keyword", etc.
    // capture.capture.node: tree-sitter node with byte range
    print(capture.name, capture.capture.node.startByte, capture.capture.node.endByte)
}
```

### Supported Languages

```swift
import CodeLanguage

let languages: [CodeLanguage] = [
    .bash, .sh, .yaml, .ini, .json,
    .toml, .xml, .markdown, .ssh_config, .dockerfile
]
```

### Memory Management

```swift
// Purge the global query cache on memory warning
CodeLanguage.clearMemoryCache()
```

## Project Structure

```
Sources/
├── CodeLanguage/
│   ├── CodeLanguage.swift          # Language enum definition
│   ├── CodeLanguage+C.swift        # Tree-sitter language bindings & version checks
│   ├── Syntax.swift                # Highlight query engine with thread-safe global cache
│   ├── QueryCapture.swift          # Query capture result model
│   └── Config.swift                # File extension mapping & language detection
├── TreeSitterBash/                 # Tree-sitter grammar: Bash
├── TreeSitterDockerfile/           # Tree-sitter grammar: Dockerfile
├── TreeSitterINI/                  # Tree-sitter grammar: INI
├── TreeSitterJSON/                 # Tree-sitter grammar: JSON
├── TreeSitterMarkdown/             # Tree-sitter grammar: Markdown
├── TreeSitterSSHConfig/            # Tree-sitter grammar: SSH Config
├── TreeSitterTOML/                 # Tree-sitter grammar: TOML
├── TreeSitterXML/                  # Tree-sitter grammar: XML
├── TreeSitterYAML/                 # Tree-sitter grammar: YAML
└── TreeSitter*Queries/             # Highlight query .scm files per language
```

## License

MIT
