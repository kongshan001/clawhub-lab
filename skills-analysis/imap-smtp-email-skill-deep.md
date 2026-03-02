# IMAP/SMTP Email Skill 深度分析报告

**分析日期**: 2026-03-02 22:04
**Skill 名称**: imap-smtp-email
**评分**: ⭐ 3.847 (当前最高评分)
**版本**: 1.0.0
**作者**: NetEase
**许可**: MIT

---

## 一、概述

**imap-smtp-email** 是一个功能完整的邮件客户端 Skill，通过标准的 IMAP/SMTP 协议实现邮件的接收和发送。支持所有主流邮箱服务，包括 Gmail、Outlook、QQ Mail 以及网易系邮箱（163.com, 126.com, 188.com）。

**核心价值**：
- 📧 **完整的邮件客户端** - 收发一体，无需多个工具
- 🔐 **安全凭证管理** - 环境变量存储，支持授权码
- 🌐 **广泛兼容性** - 支持所有标准 IMAP/SMTP 服务器
- 🔍 **强大搜索** - 多条件过滤（发件人、主题、时间范围）
- 📎 **附件支持** - 下载和发送附件

---

## 二、技术架构

### 2.1 依赖库

```json
{
  "imap": "^0.8.19",           // IMAP 协议客户端
  "imap-simple": "^5.1.0",     // IMAP 简化封装
  "mailparser": "^3.9.3",      // 邮件解析器
  "nodemailer": "^7.0.13",     // SMTP 发送引擎
  "dotenv": "^16.6.1"          // 环境变量管理
}
```

**技术亮点**：
- `imap-simple` 提供简化的 IMAP 操作 API
- `mailparser` 支持 MIME 解析、字符集转换
- `nodemailer` 是 Node.js 最成熟的 SMTP 库

### 2.2 脚本结构

```
scripts/
├── imap.js  (14.5KB) - IMAP 接收邮件逻辑
└── smtp.js  (5.9KB)  - SMTP 发送邮件逻辑
```

---

## 三、核心功能详解

### 3.1 IMAP 接收邮件

#### 3.1.1 check - 检查新邮件

```bash
node scripts/imap.js check [--limit 10] [--mailbox INBOX] [--recent 2h]
```

**参数**：
- `--limit <n>`: 最多返回多少封（默认 10）
- `--mailbox <name>`: 邮箱文件夹（默认 INBOX）
- `--recent <time>`: 时间过滤（如 30m, 2h, 7d）

**使用场景**：
- 快速查看最近未读邮件
- 定时检查新邮件通知
- 过滤特定时间段的邮件

#### 3.1.2 fetch - 获取完整邮件

```bash
node scripts/imap.js fetch <uid> [--mailbox INBOX]
```

**返回信息**：
- 邮件头部（发件人、收件人、主题、日期）
- 正文内容（纯文本和 HTML）
- 附件列表

#### 3.1.3 download - 下载附件

```bash
node scripts/imap.js download <uid> [--dir <path>] [--file <filename>]
```

**特性**：
- 支持批量下载所有附件
- 支持指定单个附件下载
- 自定义输出目录

#### 3.1.4 search - 高级搜索

```bash
node scripts/imap.js search [options]
```

**搜索条件**：
| 参数 | 说明 | 示例 |
|------|------|------|
| `--unseen` | 只搜未读 | `--unseen` |
| `--seen` | 只搜已读 | `--seen` |
| `--from <email>` | 发件人过滤 | `--from boss@company.com` |
| `--subject <text>` | 主题过滤 | `--subject "报告"` |
| `--recent <time>` | 时间过滤 | `--recent 7d` |
| `--since <date>` | 起始日期 | `--since 2026-03-01` |
| `--before <date>` | 结束日期 | `--before 2026-03-02` |

**组合示例**：
```bash
# 查找最近 7 天来自 boss 的未读邮件
node scripts/imap.js search --unseen --from boss@company.com --recent 7d
```

#### 3.1.5 mark-read / mark-unread

```bash
node scripts/imap.js mark-read <uid> [uid2 uid3...]
node scripts/imap.js mark-unread <uid> [uid2 uid3...]
```

**批量操作**：支持一次标记多封邮件

#### 3.1.6 list-mailboxes

```bash
node scripts/imap.js list-mailboxes
```

**返回**：所有邮箱文件夹（INBOX, Sent, Drafts, Trash 等）

---

### 3.2 SMTP 发送邮件

#### 3.2.1 send - 发送邮件

```bash
node scripts/smtp.js send --to <email> --subject <text> [options]
```

**必填参数**：
- `--to <email>`: 收件人（逗号分隔多人）
- `--subject <text>`: 邮件主题

**可选参数**：
| 参数 | 说明 | 示例 |
|------|------|------|
| `--body <text>` | 纯文本正文 | `--body "Hello"` |
| `--html` | 发送 HTML 正文 | `--html --body "<h1>Hi</h1>"` |
| `--body-file <file>` | 从文件读取正文 | `--body-file email.txt` |
| `--html-file <file>` | 从文件读取 HTML | `--html-file template.html` |
| `--cc <email>` | 抄送 | `--cc team@company.com` |
| `--bcc <email>` | 密送 | `--bcc boss@company.com` |
| `--attach <file>` | 附件（逗号分隔） | `--attach report.pdf,data.xlsx` |
| `--from <email>` | 覆盖默认发件人 | `--from noreply@company.com` |

**高级示例**：
```bash
# HTML 邮件 + 附件
node scripts/smtp.js send \
  --to "client@example.com,sales@company.com" \
  --cc manager@company.com \
  --subject "Monthly Report - March 2026" \
  --html \
  --body-file templates/report-email.html \
  --attach reports/march-2026.pdf,charts.xlsx
```

#### 3.2.2 test - 测试连接

```bash
node scripts/smtp.js test
```

**功能**：发送测试邮件给自己，验证 SMTP 配置

---

## 四、配置详解

### 4.1 环境变量

```bash
# IMAP 配置（接收邮件）
IMAP_HOST=imap.gmail.com
IMAP_PORT=993
IMAP_USER=your@email.com
IMAP_PASS=your_password
IMAP_TLS=true
IMAP_REJECT_UNAUTHORIZED=true
IMAP_MAILBOX=INBOX

# SMTP 配置（发送邮件）
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_SECURE=false          # true=SSL(465), false=STARTTLS(587)
SMTP_USER=your@gmail.com
SMTP_PASS=your_password
SMTP_FROM=your@gmail.com
SMTP_REJECT_UNAUTHORIZED=true
```

### 4.2 常见邮箱服务器配置

| 邮箱 | IMAP 主机 | IMAP 端口 | SMTP 主机 | SMTP 端口 |
|------|-----------|-----------|-----------|-----------|
| 163.com | imap.163.com | 993 | smtp.163.com | 465 |
| 126.com | imap.126.com | 993 | smtp.126.com | 465 |
| 188.com | imap.188.com | 993 | smtp.188.com | 465 |
| yeah.net | imap.yeah.net | 993 | smtp.yeah.net | 465 |
| Gmail | imap.gmail.com | 993 | smtp.gmail.com | 587 |
| Outlook | outlook.office365.com | 993 | smtp.office365.com | 587 |
| QQ Mail | imap.qq.com | 993 | smtp.qq.com | 587 |

### 4.3 重要安全说明

**网易邮箱（163/126/188）**：
- ⚠️ **必须使用授权码**，不能使用账号密码
- 需要先在网页版设置中开启 IMAP/SMTP
- 授权码在"设置 → POP3/SMTP/IMAP → 授权码管理"中生成

**Gmail**：
- 如果启用了两步验证，**必须使用应用专用密码**
- 在 Google 账户设置中生成应用专用密码

---

## 五、使用场景

### 5.1 自动化工作流

**场景 1：每日邮件摘要**
```bash
# 每天早上检查未读邮件
node scripts/imap.js check --unseen --recent 24h --limit 20
```

**场景 2：自动回复**
```bash
# 搜索特定主题的邮件并回复
node scripts/imap.js search --subject "咨询" --unseen --recent 7d
# 然后发送回复
node scripts/smtp.js send --to $RECIPIENT --subject "Re: 咨询" --body "感谢您的咨询..."
```

**场景 3：附件自动处理**
```bash
# 下载所有报告附件
node scripts/imap.js search --subject "周报" --recent 7d
node scripts/imap.js download $UID --dir ./reports/
```

### 5.2 邮件监控

**监控特定发件人**：
```bash
node scripts/imap.js search --from alerts@system.com --unseen --recent 1h
```

**监控紧急主题**：
```bash
node scripts/imap.js search --subject "URGENT" --unseen --recent 1h
```

### 5.3 批量操作

**批量标记已读**：
```bash
node scripts/imap.js mark-read 101 102 103 104 105
```

**批量下载附件**：
```bash
# 循环处理多封邮件
for uid in 101 102 103; do
  node scripts/imap.js download $uid --dir ./attachments/
done
```

---

## 六、与其他 Skills 对比

| Skill | 评分 | 功能 | 特点 |
|-------|------|------|------|
| **imap-smtp-email** | 3.847 | 收发邮件 | 最完整、最高评分 |
| email-daily-summary | 3.738 | 邮件摘要 | 自动生成日报 |
| himalaya | 1.217 | 邮件 CLI | 已内置、轻量级 |

**优势**：
- 评分最高（3.847）
- 功能最完整（收发一体）
- 支持最多邮箱提供商
- 详细的文档和示例

---

## 七、安全性分析

### 7.1 凭证存储

✅ **最佳实践**：
- 使用 `.env` 文件存储凭证
- `.env` 应加入 `.gitignore`
- 支持授权码（网易邮箱）
- 支持应用专用密码（Gmail）

⚠️ **注意事项**：
- 不要将 `.env` 提交到代码仓库
- 定期更换授权码/密码
- 使用专用邮箱账号进行自动化

### 7.2 TLS/SSL 支持

- IMAP: 支持 TLS（端口 993）
- SMTP: 支持 STARTTLS（587）和 SSL（465）
- 可配置 `REJECT_UNAUTHORIZED` 处理自签名证书

---

## 八、故障排除

### 8.1 连接超时

**原因**：
- 服务器地址或端口错误
- 网络防火墙阻止
- 服务器不可达

**解决**：
```bash
# 测试连接
telnet imap.gmail.com 993
telnet smtp.gmail.com 587
```

### 8.2 认证失败

**原因**：
- 用户名或密码错误
- 网易邮箱未使用授权码
- Gmail 未使用应用专用密码
- IMAP/SMTP 服务未开启

**解决**：
- 网易邮箱：在网页设置中生成授权码
- Gmail：生成应用专用密码
- 检查邮箱设置中 IMAP/SMTP 是否启用

### 8.3 TLS/SSL 错误

**原因**：
- 端口与加密方式不匹配
- 服务器使用自签名证书

**解决**：
```bash
# SSL (465 端口)
SMTP_SECURE=true
SMTP_PORT=465

# STARTTLS (587 端口)
SMTP_SECURE=false
SMTP_PORT=587

# 自签名证书
SMTP_REJECT_UNAUTHORIZED=false
```

---

## 九、代码示例

### 9.1 集成到自动化脚本

```bash
#!/bin/bash
# daily-email-check.sh - 每日邮件检查

cd /root/.openclaw/workspace/skills/imap-smtp-email

# 检查未读邮件
echo "=== 未读邮件 ===" 
node scripts/imap.js check --unseen --recent 24h --limit 10

# 检查特定发件人
echo "=== 来自老板的邮件 ==="
node scripts/imap.js search --from boss@company.com --recent 7d

# 下载报告附件
echo "=== 下载周报附件 ==="
UIDS=$(node scripts/imap.js search --subject "周报" --recent 7d --limit 5 | grep "UID:" | awk '{print $2}')
for uid in $UIDS; do
  node scripts/imap.js download $uid --dir ~/reports/
done
```

### 9.2 定时任务

```bash
# crontab -e
# 每小时检查新邮件
0 * * * * cd /root/.openclaw/workspace/skills/imap-smtp-email && node scripts/imap.js check --unseen --recent 1h >> /var/log/email-check.log 2>&1

# 每天早上 8 点发送日报
0 8 * * * cd /root/.openclaw/workspace/skills/imap-smtp-email && node scripts/smtp.js send --to team@company.com --subject "Daily Update" --body-file ~/daily-report.txt
```

---

## 十、总结与评分

### 10.1 优点

✅ **功能完整**：收发一体，附件支持
✅ **兼容性强**：支持所有主流邮箱
✅ **文档详细**：每个命令都有示例
✅ **安全性好**：支持授权码、TLS
✅ **易于集成**：清晰的 CLI 接口
✅ **评分最高**：3.847 分

### 10.2 缺点

⚠️ **需要配置**：需手动设置环境变量
⚠️ **授权码**：网易邮箱需要额外步骤
⚠️ **无 GUI**：纯命令行操作

### 10.3 综合评分

| 维度 | 评分 | 说明 |
|------|------|------|
| 功能完整性 | ⭐⭐⭐⭐⭐ | 收发搜索标记，功能齐全 |
| 易用性 | ⭐⭐⭐⭐ | CLI 清晰，文档详细 |
| 兼容性 | ⭐⭐⭐⭐⭐ | 支持所有主流邮箱 |
| 安全性 | ⭐⭐⭐⭐ | 支持授权码、TLS |
| 文档质量 | ⭐⭐⭐⭐⭐ | 示例丰富，故障排除详细 |

**总评**: ⭐⭐⭐⭐⭐ (5/5)

---

## 十一、推荐指数

**强烈推荐** 🔥

适用于：
- 需要邮件自动化的场景
- 定期检查邮件通知
- 批量处理邮件和附件
- 集成到自动化工作流

**安装命令**：
```bash
clawhub install imap-smtp-email
```

---

**分析者**: Glint 🔺
**ClawHub Lab**
**2026-03-02 22:04**
