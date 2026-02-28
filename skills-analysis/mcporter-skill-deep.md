# mcporter Skill 深度分析

> 📅 分析日期: 2026-02-28
> ⭐ 评分: N/A (OpenClaw 内置)
> 🔗 来源: OpenClaw 内置 Skill
> 🏠 Homepage: http://mcporter.dev

---

## 基本信息

| 属性 | 值 |
|------|-----|
| 名称 | mcporter |
| Emoji | 📦 |
| 功能 | MCP 服务器/工具的直接调用和管理 |
| 依赖 | mcporter CLI |

## 核心功能

### 1. 列出工具

```bash
# 列出所有服务器
mcporter list

# 列出特定服务器的工具（带 schema）
mcporter list <server> --schema
```

### 2. 调用工具

```bash
# 选择器语法
mcporter call linear.list_issues team=ENG limit:5

# 函数语法
mcporter call "linear.create_issue(title: \"Bug\")"

# 完整 URL
mcporter call https://api.example.com/mcp.fetch url:https://example.com

# Stdio 模式
mcporter call --stdio "bun run ./server.ts" scrape url=https://example.com

# JSON payload
mcporter call <server.tool> --args '{"limit":5}'
```

### 3. 认证和配置

```bash
# OAuth 认证
mcporter auth <server | url> [--reset]

# 配置管理
mcporter config list
mcporter config get <key>
mcporter config add <key> <value>
mcporter config remove <key>
mcporter config import <file>
mcporter config login
mcporter config logout
```

### 4. 守护进程

```bash
mcporter daemon start
mcporter daemon status
mcporter daemon stop
mcporter daemon restart
```

### 5. 代码生成

```bash
# 生成 CLI
mcporter generate-cli --server <name>
mcporter generate-cli --command <url>

# 检查 CLI
mcporter inspect-cli <path> [--json]

# 生成 TypeScript
mcporter emit-ts <server> --mode client|types
```

## 配置

- 默认配置文件: `./config/mcporter.json`
- 覆盖: `--config <path>`

## 输出格式

```bash
# 机器可读输出
mcporter list --output json
```

## 使用场景

| 场景 | 命令示例 |
|------|----------|
| 调用 Linear API | `mcporter call linear.list_issues team=ENG` |
| 抓取网页 | `mcporter call --stdio "bun run ./server.ts" scrape url=...` |
| 生成类型定义 | `mcporter emit-ts my-server --mode types` |
| 管理 MCP 配置 | `mcporter config add server_url https://...` |

## MCP 协议

MCP (Model Context Protocol) 是一种让 AI 模型与外部工具通信的协议：
- 支持 HTTP 和 Stdio 传输
- 工具有 schema 定义
- 支持 OAuth 认证

---

*由 ClawHub Lab Agent 分析*
