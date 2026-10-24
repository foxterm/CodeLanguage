# CodeLanguage

[English](README.md) | [中文文档](README_CN.md)

基于 Tree-sitter 的语法高亮与语言检测库，FoxTerm APP 专用核心组件。提供增量解析、按语言配置的高亮查询，以及从文件名、Shebang、内容智能识别语言的能力。

> 本库为 [FoxTerm](https://foxterm.app/) APP 专用核心组件，欢迎下载使用：[https://foxterm.app/](https://foxterm.app/)

## 功能

- **Tree-sitter 解析** — 增量解析，支持各语言语法绑定（Bash、Dockerfile、INI、JSON、Markdown、SSH Config、TOML、XML、YAML）
- **语法高亮** — 线程安全的全局查询缓存，按语言配置 `.scm` 高亮查询
- **语言检测** — 从文件名、路径后缀、Shebang、首行特征或全文扫描检测语言
- **文件后缀映射** — 完整的文件后缀与特殊文件名映射（`.bashrc`、`Makefile`、`Dockerfile`、`.gitignore` 等）
- **内存管理** — 提供手动清空缓存 API，适用于内存警告与后台切换场景
- **SwiftUI 就绪** — 轻量纯 Swift API，无 UI 依赖

## 环境要求

| 平台  | 最低版本 |
|-------|---------|
| iOS   | 17.0    |
| macOS | 14.0    |
| Swift | 5.10    |

## 安装

### Swift Package Manager

```swift
.package(url: "https://github.com/foxterm/CodeLanguage.git", branch: "main"),
```

```swift
.product(name: "CodeLanguage", package: "CodeLanguage"),
```

### 依赖

- [tree-sitter](https://github.com/tree-sitter/tree-sitter.git) — 增量解析库
- [Sync](https://github.com/foxterm/Sync) — 并发同步原语
- [Extension](https://github.com/foxterm/Extension) — Foundation 扩展

## 使用

### 语言检测

```swift
import CodeLanguage

// 从文件名检测
let lang = CodeLanguage.from(fileName: "config.yaml")  // .yaml

// 从路径检测
let lang = CodeLanguage.from(fileName: "/etc/ssh/sshd_config")  // .ssh_config

// 从内容检测
let lang = CodeLanguage.from(content: "#!/bin/bash\necho hello")  // .bash

// 综合检测（文件名优先，内容回退）
let lang = CodeLanguage.from(name: "Makefile", content: nil)  // .bash
```

### 语法高亮

```swift
import CodeLanguage

let code = #"{"key": "value"}"#
let language = CodeLanguage.json

language.highlights(code: code, encoding: .utf8) { capture in
    // capture.name: "string"、"number"、"keyword" 等
    // capture.capture.node: 包含字节范围的 tree-sitter 节点
    print(capture.name, capture.capture.node.startByte, capture.capture.node.endByte)
}
```

### 支持的语言

```swift
import CodeLanguage

let languages: [CodeLanguage] = [
    .bash, .sh, .yaml, .ini, .json,
    .toml, .xml, .markdown, .ssh_config, .dockerfile
]
```

### 内存管理

```swift
// 收到内存警告时清空全局查询缓存
CodeLanguage.clearMemoryCache()
```

## 项目结构

```
Sources/
├── CodeLanguage/
│   ├── CodeLanguage.swift          # 语言枚举定义
│   ├── CodeLanguage+C.swift        # Tree-sitter 语言绑定与版本检查
│   ├── Syntax.swift                # 高亮查询引擎（线程安全全局缓存）
│   ├── QueryCapture.swift          # 查询捕获结果模型
│   └── Config.swift                # 文件后缀映射与语言检测
├── TreeSitterBash/                 # Tree-sitter 语法：Bash
├── TreeSitterDockerfile/           # Tree-sitter 语法：Dockerfile
├── TreeSitterINI/                  # Tree-sitter 语法：INI
├── TreeSitterJSON/                 # Tree-sitter 语法：JSON
├── TreeSitterMarkdown/             # Tree-sitter 语法：Markdown
├── TreeSitterSSHConfig/            # Tree-sitter 语法：SSH Config
├── TreeSitterTOML/                 # Tree-sitter 语法：TOML
├── TreeSitterXML/                  # Tree-sitter 语法：XML
├── TreeSitterYAML/                 # Tree-sitter 语法：YAML
└── TreeSitter*Queries/             # 各语言高亮查询 .scm 文件
```

## 许可证

MIT
