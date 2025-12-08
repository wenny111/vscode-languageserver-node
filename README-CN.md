# VSCode Language Server Protocol - Node.js 实现

这是 VSCode Language Server Protocol (LSP) 的官方 Node.js 实现，由 Microsoft 维护。该项目提供了用于构建语言服务器和客户端的完整 npm 模块集合。

## 📦 核心模块说明

本项目包含 6 个主要的 npm 包，每个包都有特定的职责：

### 1. **vscode-languageclient** (客户端)
**作用**: 在 VSCode 扩展中与语言服务器通信的客户端实现

- **位置**: `client/` 目录
- **版本**: `10.0.0-next.18`
- **功能**:
  - 管理语言服务器的生命周期（启动、停止、重启）
  - 处理文档同步（打开、关闭、更改、保存）
  - 提供中间件机制，允许拦截和扩展请求/通知
  - 支持多种传输方式：stdio、IPC、socket、pipe
  - 支持 Node.js 和浏览器环境

**主要类**:
- `LanguageClient`: 语言客户端主类，用于连接和管理语言服务器

### 2. **vscode-languageserver** (服务器)
**作用**: 实现语言服务器的核心库

- **位置**: `server/` 目录
- **版本**: `10.0.0-next.15`
- **功能**:
  - 提供连接管理（`createConnection`）
  - 处理客户端请求（代码补全、跳转定义、查找引用等）
  - 支持动态注册/注销能力
  - 支持工作区、文档、窗口等远程 API
  - 支持 Node.js 和浏览器环境

**主要函数**:
- `createConnection()`: 创建与客户端的连接

### 3. **vscode-languageserver-protocol** (协议定义)
**作用**: LSP 协议的 TypeScript 类型定义和消息实现

- **位置**: `protocol/` 目录
- **版本**: `3.17.6-next.15`
- **功能**:
  - 定义所有 LSP 消息类型（Request、Notification、Response）
  - 提供协议版本 3.17.x 的完整实现
  - 包含消息序列化/反序列化逻辑
  - 支持协议元模型生成

### 4. **vscode-languageserver-types** (类型定义)
**作用**: 客户端和服务器共享的数据类型定义

- **位置**: `types/` 目录
- **功能**:
  - 定义 Position、Range、Document 等基础类型
  - 定义 CompletionItem、Diagnostic、SymbolInformation 等功能类型
  - 被客户端和服务器共同使用，确保类型一致性

### 5. **vscode-jsonrpc** (底层通信)
**作用**: 实现客户端与服务器之间的 JSON-RPC 消息协议

- **位置**: `jsonrpc/` 目录
- **功能**:
  - 实现 JSON-RPC 2.0 协议
  - 提供消息读取器（MessageReader）和写入器（MessageWriter）
  - 支持多种传输方式：stdio、IPC、socket、pipe
  - 处理请求/响应、通知、错误等消息类型

### 6. **vscode-languageserver-textdocument** (文本文档)
**作用**: 提供文本文档的实现，支持增量更新

- **位置**: `textDocument/` 目录
- **功能**:
  - 实现 `TextDocument` 类
  - 支持文档的增量更新（避免全量替换）
  - 提供位置转换、范围计算等工具方法

## ⚡ 快速开始

如果你想快速体验项目，可以按照以下步骤：

```bash
# 1. 安装依赖（会自动创建符号链接）
npm install

# 2. 编译所有模块
npm run compile

# 3. 编译并启动测试环境
cd testbed
npm run compile
code .  # 在 VSCode 中打开，然后按 F5 启动调试
```

## 🚀 如何启动项目

### 前置要求

- Node.js 22.13.14 或更高版本
- npm 或 yarn 包管理器
- VSCode（用于开发和测试）

### 安装步骤

1. **克隆仓库并安装依赖**
```bash
# 克隆仓库
git clone https://github.com/Microsoft/vscode-languageserver-node.git
cd vscode-languageserver-node

# 安装所有依赖（会自动执行 postinstall 脚本）
npm install
```

`postinstall` 脚本会自动：
- 安装所有子模块的依赖
- 创建包之间的符号链接（symlink）
- 安装测试环境的依赖
- 安装 Playwright（用于浏览器测试）

2. **创建符号链接**（如果 postinstall 未执行）
```bash
npm run symlink
```

这一步很重要，因为它将各个包链接在一起，使得本地开发时可以使用本地版本而不是 npm 上的版本。

### 编译项目

```bash
# 编译所有模块
npm run compile

# 或者分别编译各个模块
npm run compile:types          # 编译类型定义
npm run compile:jsonrpc        # 编译 JSON-RPC
npm run compile:protocol       # 编译协议定义
npm run compile:server         # 编译服务器
npm run compile:client         # 编译客户端
npm run compile:textDocument   # 编译文本文档
```

### 运行测试

```bash
# 运行所有测试
npm test

# 运行 lint 检查
npm run lint
```

### 开发模式（监听文件变化）

```bash
# 监听所有文件变化并自动编译
npm run watch

# 监听特定模块
npm run watch:testbed
npm run watch:tsconfig-gen
```

## 🧪 测试环境 (Testbed)

`testbed/` 目录包含一个完整的示例项目，展示了如何使用这些模块构建一个语言服务器扩展。

### Testbed 结构

```
testbed/
├── client/          # VSCode 扩展客户端
│   └── src/
│       └── extension.ts    # 扩展入口，创建 LanguageClient
├── server/          # 语言服务器实现
│   └── src/
│       └── server.ts      # 服务器入口，实现各种 LSP 功能
└── workspace/       # 测试工作区文件
```

### 启动 Testbed

#### 完整启动步骤

**重要**: 在启动 testbed 之前，必须确保所有依赖模块已编译并创建符号链接。

1. **首次启动前的准备工作**（在项目根目录执行）
```bash
# 1. 创建符号链接（将本地包链接到 testbed）
npm run symlink:testbed

# 2. 编译所有依赖模块（如果还没编译）
npm run compile:types          # 编译类型定义
npm run compile:jsonrpc        # 编译 JSON-RPC
npm run compile:protocol       # 编译协议定义
npm run compile:client         # 编译客户端（必需）
npm run compile:server         # 编译服务器（可选，testbed 会自己编译）

# 或者一次性编译所有模块
npm run compile
```

2. **编译 testbed**
```bash
# 在项目根目录执行
cd testbed
npm run compile

# 或者从根目录直接编译
npm run compile:testbed
```

3. **在 VSCode 中打开 testbed 目录**
```bash
code testbed
```

4. **启动调试**
   - 在 VSCode 中按 `F5` 或点击"运行和调试"
   - 选择 "Launch Client" 配置
   - 这会启动一个新的 VSCode 窗口（Extension Development Host）
   - 在新窗口中打开 `testbed/workspace` 目录
   - 打开 `.bat` 文件测试语言服务器功能
   - 尝试代码补全、悬停提示、跳转定义等功能

5. **调试服务器**（可选）
   - 如果需要调试服务器端代码，先启动 "Launch Client"
   - 然后启动 "Attach to Server" 配置（端口 6009）
   - 这样可以在服务器代码中设置断点

6. **测试命令**
   - 在新窗口中按 `Ctrl+Shift+P` (Windows/Linux) 或 `Cmd+Shift+P` (Mac)
   - 输入 "Open Test File" 打开测试文件
   - 输入 "Open file with dynamic content" 测试动态内容

#### 常见问题排查

**问题 1: 报错 "Cannot find module 'vscode-languageclient/node'"**

**原因**: 符号链接未创建或依赖模块未编译

**解决方法**:
```bash
# 1. 确保创建了符号链接
npm run symlink:testbed

# 2. 确保 client 模块已编译（必须有 lib 目录）
npm run compile:client

# 3. 重新编译 testbed
cd testbed
npm run compile
```

**问题 2: 调试时打开新窗口但扩展未激活**

**原因**: testbed 未编译或编译失败

**解决方法**:
```bash
# 检查编译输出
cd testbed
npm run compile

# 查看是否有错误，确保 testbed/client/out/extension.js 存在
```

**问题 3: 服务器无法启动**

**原因**: 服务器模块未编译或路径错误

**解决方法**:
```bash
# 编译服务器模块
npm run compile:server

# 确保 testbed/server/out/server.js 存在
```

### Testbed 功能示例

Testbed 服务器实现了以下 LSP 功能：

- ✅ 代码补全（Completion）
- ✅ 悬停提示（Hover）
- ✅ 跳转定义（Definition）
- ✅ 查找引用（References）
- ✅ 文档符号（Document Symbols）
- ✅ 工作区符号（Workspace Symbols）
- ✅ 代码操作（Code Actions）
- ✅ 代码格式化（Formatting）
- ✅ 重命名（Rename）
- ✅ 诊断（Diagnostics）
- ✅ 语义标记（Semantic Tokens）
- ✅ 调用层次（Call Hierarchy）
- ✅ 笔记本文档支持（Notebook Documents）

## 📝 开发指南

### 项目结构

```
vscode-languageserver-node/
├── client/              # 客户端模块
│   ├── src/
│   │   ├── common/      # 通用代码（Node 和 Browser 共享）
│   │   ├── node/        # Node.js 特定实现
│   │   └── browser/     # 浏览器特定实现
│   └── lib/             # 编译输出
├── server/              # 服务器模块
│   ├── src/
│   │   ├── common/      # 通用代码
│   │   ├── node/        # Node.js 特定实现
│   │   └── browser/     # 浏览器特定实现
│   └── lib/             # 编译输出
├── protocol/            # 协议定义
├── jsonrpc/             # JSON-RPC 实现
├── types/               # 类型定义
├── textDocument/        # 文本文档实现
├── testbed/             # 测试环境
└── build/               # 构建脚本
```

### 关键脚本说明

| 脚本 | 说明 |
|------|------|
| `npm run symlink` | 创建包之间的符号链接 |
| `npm run compile` | 编译所有 TypeScript 代码 |
| `npm run watch` | 监听文件变化并自动编译 |
| `npm test` | 运行所有测试 |
| `npm run lint` | 运行 ESLint 检查 |
| `npm run clean` | 清理编译输出 |
| `npm run all` | 执行完整构建流程（symlink + compile + test） |
| `npm run rebuildAll` | 清理后重新构建所有内容 |

### 模块导出方式

从 10.0.0 版本开始，所有模块使用 `exports` 字段而不是 `main` 和 `typings`：

```typescript
// 导入通用 API
import { LanguageClient } from 'vscode-languageclient';

// 导入 Node.js 特定实现
import { LanguageClient } from 'vscode-languageclient/node';

// 导入浏览器特定实现
import { LanguageClient } from 'vscode-languageclient/browser';
```

### 传输方式

语言客户端和服务器支持多种通信方式：

1. **stdio** (标准输入输出) - 最常用
2. **IPC** (进程间通信) - Node.js 环境
3. **Socket** (网络套接字)
4. **Pipe** (命名管道) - Windows/Linux

### 技术栈

- **TypeScript**: 5.9.2
- **Node.js**: 22.13.14
- **ES 版本**: ES2022
- **模块解析**: node16
- **测试框架**: Mocha + Playwright

## 🔗 相关资源

- [Language Server Protocol 规范](https://microsoft.github.io/language-server-protocol/)
- [VSCode 扩展开发文档](https://code.visualstudio.com/docs/extensions/example-language-server)
- [GitHub 仓库](https://github.com/Microsoft/vscode-languageserver-node)
- [问题反馈](https://github.com/Microsoft/vscode-languageserver-node/issues)

## 📄 许可证

[MIT License](https://github.com/Microsoft/vscode-languageserver-node/blob/master/License.txt)

