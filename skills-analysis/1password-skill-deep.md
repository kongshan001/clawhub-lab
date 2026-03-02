# 1Password CLI Skill 深度分析

## 基本信息

| 属性 | 值 |
|------|-----|
| **名称** | 1password |
| **类型** | OpenClaw 内置 Skill |
| **分类** | 安全/密码管理 |
| **Emoji** | 🔐 |
| **依赖** | `op` CLI (1Password CLI) |
| **主页** | https://developer.1password.com/docs/cli/get-started/ |

## 功能概述

1Password CLI Skill 提供 OpenClaw 与 1Password 密码管理器的安全集成，支持：

- **CLI 安装与配置** - 安装 1Password CLI 并启用桌面应用集成
- **账户认证** - 单账户或多账户登录
- **密钥读取** - 安全读取存储在 1Password 中的密钥
- **密钥注入** - 使用 `op run` / `op inject` 将密钥注入环境
- **命令执行** - 使用存储的密钥执行命令

## 安装要求

### 系统要求

| 平台 | 最低版本 | 特殊要求 |
|------|----------|----------|
| macOS | Big Sur 11.0.0+ | Touch ID 可选 |
| Windows | - | Windows Hello |
| Linux | - | PolKit + auth agent |

### 依赖安装

```bash
# macOS (推荐)
brew install 1password-cli

# Linux
# 参见官方文档: https://developer.1password.com/docs/cli/get-started/
```

### 桌面应用集成

1. 打开并解锁 1Password 桌面应用
2. 选择账户/集合
3. 启用 CLI 集成：
   - **macOS**: Settings > Developer > Integrate with 1Password CLI
   - **Windows**: Settings > Developer > Integrate (需先启用 Windows Hello)
   - **Linux**: Settings > Security > Unlock using system authentication → Settings > Developer > Integrate

## 核心工作流程

### 1. 验证 CLI

```bash
op --version
```

### 2. 创建专用 tmux 会话（必须）

由于 OpenClaw 的 shell 工具每次使用新 TTY，为避免重复提示和失败，**所有 `op` 命令必须在专用 tmux 会话中运行**：

```bash
SOCKET_DIR="${OPENCLAW_TMUX_SOCKET_DIR:-${TMPDIR:-/tmp}/openclaw-tmux-sockets}"
mkdir -p "$SOCKET_DIR"
SOCKET="$SOCKET_DIR/openclaw-op.sock"
SESSION="op-auth-$(date +%Y%m%d-%H%M%S)"

# 创建 tmux 会话
tmux -S "$SOCKET" new -d -s "$SESSION" -n shell

# 发送命令
tmux -S "$SOCKET" send-keys -t "$SESSION":0.0 -- "op signin --account my.1password.com" Enter

# 捕获输出
tmux -S "$SOCKET" capture-pane -p -J -t "$SESSION":0.0 -S -200

# 清理会话
tmux -S "$SOCKET" kill-session -t "$SESSION"
```

### 3. 登录认证

```bash
# 使用桌面应用集成
op signin

# 指定账户
op signin --account my.1password.com

# 无桌面集成时
op account add
```

### 4. 验证访问

```bash
op whoami
```

## 常用命令

### 密钥库操作

```bash
# 列出所有 vault
op vault list

# 获取 vault ID
op vault get "Private"
```

### 读取密钥

```bash
# 获取项目
op item get "Database Password"

# 获取特定字段
op item get "API Key" --field password

# 以 JSON 格式输出
op item get "API Key" --format json
```

### 注入密钥

```bash
# 使用 op run 运行命令
op run --print --vault "Private" -- "npm run start"

# 使用 op inject 注入到文件
cat config.template.yml | op inject > config.yml
```

### 多账户管理

```bash
# 列出账户
op account list

# 使用特定账户
op --account work item list
# 或设置环境变量
export OP_ACCOUNT=work
```

## 安全最佳实践

### ⚠️ 严禁操作

- **禁止**将密钥粘贴到日志、聊天或代码中
- **禁止**在 tmux 之外直接运行 `op` 命令

### ✅ 推荐做法

1. **优先使用 `op run` / `op inject`** 而非将密钥写入磁盘
2. **始终在 tmux 会话中运行** `op` 命令
3. **使用桌面应用集成** 获得最佳安全体验
4. **多账户时明确指定账户** 使用 `--account` 或 `OP_ACCOUNT`

## 与 OpenClaw 集成

### 典型使用场景

1. **安全读取 API 密钥**
   ```
   用户: "读取我的 OpenAI API 密钥"
   Agent: 在 tmux 中运行 op item get "OpenAI API Key" --field password
   ```

2. **注入环境变量运行命令**
   ```
   用户: "用生产数据库密码运行迁移脚本"
   Agent: op run --vault "Production" -- "python migrate.py"
   ```

3. **管理多个账户**
   ```
   用户: "列出工作账户的所有密码"
   Agent: op --account work item list --vault "Work"
   ```

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| "account is not signed in" | 会话过期 | 在 tmux 中重新运行 `op signin` |
| CLI 找不到 | 未安装或不在 PATH | `brew install 1password-cli` |
| 桌面集成不工作 | 未在应用中启用 | 检查 Settings > Developer |
| TTY 提示问题 | 直接运行 op | 必须在 tmux 会话中运行 |

## 参考文件

- `references/get-started.md` - 安装 + 应用集成 + 登录流程
- `references/cli-examples.md` - 实际 `op` 命令示例

## 总结

1Password CLI Skill 是 OpenClaw 安全工具链的重要组成部分，提供了：

- 🔐 **安全密钥管理** - 无需明文存储敏感信息
- 🖥️ **桌面应用集成** - 生物识别解锁支持
- 🔄 **多账户支持** - 个人/工作账户隔离
- 🛡️ **安全注入** - 直接注入环境变量或进程

**适用场景**: 任何需要安全访问 API 密钥、数据库密码、证书等敏感信息的自动化任务。

---

*分析日期: 2026-03-02 | 分析者: Glint 🔺*
