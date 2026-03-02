# Himalaya Email CLI Skill 深度分析

## 基本信息

| 属性 | 值 |
|------|-----|
| **名称** | himalaya |
| **类型** | OpenClaw 内置 Skill |
| **分类** | 通信/邮件 |
| **Emoji** | 📧 |
| **依赖** | `himalaya` CLI |
| **主页** | https://github.com/pimalaya/himalaya |

## 功能概述

Himalaya 是一个强大的终端邮件客户端，支持：

- **邮件管理** - 列出、读取、搜索邮件
- **邮件撰写** - 写新邮件、回复、转发
- **文件夹操作** - 移动、复制、删除邮件
- **多账户** - 支持多个邮箱账户
- **附件处理** - 下载邮件附件
- **多种后端** - IMAP、SMTP、Notmuch、Sendmail

## 安装

```bash
# macOS/Linux
brew install himalaya

# 验证安装
himalaya --version
```

## 配置

### 配置文件位置

`~/.config/himalaya/config.toml`

### 快速配置（交互式向导）

```bash
himalaya account configure
```

### 手动配置示例

```toml
[accounts.personal]
email = "you@example.com"
display-name = "Your Name"
default = true

# IMAP 后端（读取邮件）
backend.type = "imap"
backend.host = "imap.example.com"
backend.port = 993
backend.encryption.type = "tls"
backend.login = "you@example.com"
backend.auth.type = "password"
backend.auth.cmd = "pass show email/imap"  # 安全存储密码

# SMTP 后端（发送邮件）
message.send.backend.type = "smtp"
message.send.backend.host = "smtp.example.com"
message.send.backend.port = 587
message.send.backend.encryption.type = "start-tls"
message.send.backend.login = "you@example.com"
message.send.backend.auth.type = "password"
message.send.backend.auth.cmd = "pass show email/smtp"
```

## 常用邮件提供商配置

### Gmail

```toml
[accounts.gmail]
email = "you@gmail.com"
display-name = "Your Name"
default = true

backend.type = "imap"
backend.host = "imap.gmail.com"
backend.port = 993
backend.encryption.type = "tls"
backend.login = "you@gmail.com"
backend.auth.type = "password"
backend.auth.cmd = "pass show google/app-password"

message.send.backend.type = "smtp"
message.send.backend.host = "smtp.gmail.com"
message.send.backend.port = 587
message.send.backend.encryption.type = "start-tls"
message.send.backend.login = "you@gmail.com"
message.send.backend.auth.type = "password"
message.send.backend.auth.cmd = "pass show google/app-password"
```

⚠️ **注意**: Gmail 需要使用应用专用密码（App Password），在 Google 账户设置中生成。

### iCloud

```toml
[accounts.icloud]
email = "you@icloud.com"
display-name = "Your Name"

backend.type = "imap"
backend.host = "imap.mail.me.com"
backend.port = 993
backend.encryption.type = "tls"
backend.login = "you@icloud.com"
backend.auth.type = "password"
backend.auth.cmd = "pass show icloud/app-password"

message.send.backend.type = "smtp"
message.send.backend.host = "smtp.mail.me.com"
message.send.backend.port = 587
message.send.backend.encryption.type = "start-tls"
message.send.backend.login = "you@icloud.com"
backend.auth.type = "password"
message.send.backend.auth.cmd = "pass show icloud/app-password"
```

⚠️ **注意**: 在 appleid.apple.com 生成应用专用密码。

## 命令参考

### 文件夹操作

| 命令 | 功能 | 示例 |
|------|------|------|
| `folder list` | 列出文件夹 | `himalaya folder list` |

### 邮件列表

| 命令 | 功能 | 示例 |
|------|------|------|
| `envelope list` | 列出邮件 | `himalaya envelope list` |
| `envelope list --folder` | 指定文件夹 | `himalaya envelope list --folder "Sent"` |
| `envelope list --page` | 分页 | `himalaya envelope list --page 1 --page-size 20` |

### 搜索邮件

```bash
# 按发件人搜索
himalaya envelope list from john@example.com

# 按主题搜索
himalaya envelope list subject meeting

# 组合搜索
himalaya envelope list from boss subject urgent
```

### 读取邮件

| 命令 | 功能 | 示例 |
|------|------|------|
| `message read` | 读取邮件 | `himalaya message read 42` |
| `message export` | 导出原始 MIME | `himalaya message export 42 --full` |

### 撰写邮件

| 命令 | 功能 | 示例 |
|------|------|------|
| `message write` | 写新邮件（打开编辑器） | `himalaya message write` |
| `message reply` | 回复 | `himalaya message reply 42` |
| `message reply --all` | 回复全部 | `himalaya message reply 42 --all` |
| `message forward` | 转发 | `himalaya message forward 42` |

### 直接发送邮件

```bash
# 使用模板发送
cat << 'EOF' | himalaya template send
From: you@example.com
To: recipient@example.com
Subject: Test Message

Hello from Himalaya!
EOF

# 使用参数发送
himalaya message write -H "To:recipient@example.com" -H "Subject:Test" "Message body here"
```

### 邮件管理

| 命令 | 功能 | 示例 |
|------|------|------|
| `message move` | 移动邮件 | `himalaya message move 42 "Archive"` |
| `message copy` | 复制邮件 | `himalaya message copy 42 "Important"` |
| `message delete` | 删除邮件 | `himalaya message delete 42` |

### 标记管理

```bash
# 添加标记
himalaya flag add 42 --flag seen

# 移除标记
himalaya flag remove 42 --flag seen

# 常用标记: seen, answered, flagged, deleted, draft
```

### 附件

```bash
# 下载附件
himalaya attachment download 42

# 指定目录
himalaya attachment download 42 --dir ~/Downloads
```

### 多账户

```bash
# 列出账户
himalaya account list

# 使用特定账户
himalaya --account work envelope list
```

## 输出格式

```bash
# JSON 格式（便于脚本处理）
himalaya envelope list --output json

# 纯文本格式
himalaya envelope list --output plain
```

## 高级功能

### Notmuch 后端（本地邮件）

```toml
[accounts.local]
email = "user@example.com"

backend.type = "notmuch"
backend.db-path = "~/.mail/.notmuch"
```

### OAuth2 认证

```toml
backend.auth.type = "oauth2"
backend.auth.client-id = "your-client-id"
backend.auth.client-secret.cmd = "pass show oauth/client-secret"
backend.auth.access-token.cmd = "pass show oauth/access-token"
backend.auth.refresh-token.cmd = "pass show oauth/refresh-token"
backend.auth.auth-url = "https://provider.com/oauth/authorize"
backend.auth.token-url = "https://provider.com/oauth/token"
```

### 自定义签名

```toml
[accounts.default]
signature = "Best regards,\nYour Name"
signature-delim = "-- \n"
```

### 下载目录

```toml
[accounts.default]
downloads-dir = "~/Downloads/himalaya"
```

## 与 OpenClaw 集成

### 典型使用场景

1. **检查新邮件**
   ```
   用户: "我有新邮件吗？"
   Agent: himalaya envelope list --page 1 --page-size 5
   ```

2. **发送邮件**
   ```
   用户: "给 john@example.com 发邮件，说会议改到下午3点"
   Agent: himalaya message write -H "To:john@example.com" -H "Subject:会议时间变更" "会议已改到下午3点，请知悉。"
   ```

3. **搜索邮件**
   ```
   用户: "找一下老板发的关于预算的邮件"
   Agent: himalaya envelope list from boss@company.com subject budget
   ```

4. **读取邮件内容**
   ```
   用户: "读一下最新的那封邮件"
   Agent: himalaya message read <latest-id>
   ```

### 自动化示例

```bash
# 定期检查重要邮件并发送通知
*/30 * * * * himalaya envelope list --folder INBOX --page 1 --page-size 10 --output json | jq '.[] | select(.flags | contains(["unread"])) | .subject' | while read subject; do notify-send "New Email" "$subject"; done

# 自动归档已读邮件
0 0 * * * himalaya envelope list --folder INBOX | grep -v "UNSEEN" | awk '{print $1}' | xargs -I {} himalaya message move {} "Archive"
```

## 调试

```bash
# 启用调试日志
RUST_LOG=debug himalaya envelope list

# 完整追踪
RUST_LOG=trace RUST_BACKTRACE=1 himalaya envelope list
```

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| "Authentication failed" | 密码错误 | 检查密码/应用密码 |
| "Connection refused" | 服务器不可达 | 检查网络和服务器地址 |
| "TLS error" | 证书问题 | 检查 encryption.type 设置 |
| 邮件 ID 无效 | 文件夹切换后 ID 变化 | 重新列出邮件获取新 ID |
| 中文乱码 | 编码问题 | 检查终端编码设置 |

## 安全建议

1. **使用密码管理器** - 通过 `backend.auth.cmd` 调用 `pass` 或其他密码管理器
2. **不要硬编码密码** - 避免 `backend.auth.raw`
3. **使用应用专用密码** - Gmail/iCloud 等需要
4. **启用 TLS** - 确保加密传输

## 密码存储选项对比

| 方式 | 安全性 | 推荐度 |
|------|--------|--------|
| `auth.raw` | ❌ 明文 | 不推荐 |
| `auth.cmd` | ✅ 调用外部工具 | ⭐⭐⭐⭐⭐ |
| `auth.keyring` | ✅ 系统密钥环 | ⭐⭐⭐⭐ |

## 总结

Himalaya Skill 为 OpenClaw 提供了完整的邮件管理能力：

- 📧 **全功能邮件** - 收发、搜索、归档、删除
- 🔒 **安全认证** - 支持密码管理器和 OAuth2
- 📱 **多账户** - 同时管理多个邮箱
- 📎 **附件支持** - 下载和处理邮件附件
- 🤖 **脚本友好** - JSON 输出便于自动化

**适用场景**: 邮件自动化、智能邮件助手、批量邮件处理、邮件监控和通知。

---

*分析日期: 2026-03-02 | 分析者: Glint 🔺*
