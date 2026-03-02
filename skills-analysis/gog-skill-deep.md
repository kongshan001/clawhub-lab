# gog Skill 深度分析

## 基本信息

| 属性 | 值 |
|------|-----|
| **名称** | gog |
| **描述** | Google Workspace CLI for Gmail, Calendar, Drive, Contacts, Sheets, and Docs |
| **作者** | Peter Steinberger (@steipete) |
| **主页** | https://gogcli.sh |
| **Emoji** | 🎮 |
| **类型** | OpenClaw 内置 Skill |
| **依赖** | `gog` CLI 二进制文件 |

## 功能概览

`gog` 是一个强大的 Google Workspace 命令行工具，支持以下 Google 服务：

| 服务 | 功能 |
|------|------|
| **Gmail** | 搜索、发送、草稿、回复邮件 |
| **Calendar** | 事件查询、创建、更新、颜色标记 |
| **Drive** | 文件搜索 |
| **Contacts** | 联系人列表 |
| **Sheets** | 读取、更新、追加、清除单元格数据 |
| **Docs** | 导出文档、读取内容 |

## 安装配置

### 安装方式

```bash
# Homebrew 安装
brew install steipete/tap/gogcli
```

### OAuth 认证配置

```bash
# 1. 设置凭证文件（从 Google Cloud Console 下载）
gog auth credentials /path/to/client_secret.json

# 2. 添加账户并授权服务
gog auth add you@gmail.com --services gmail,calendar,drive,contacts,docs,sheets

# 3. 查看已授权账户
gog auth list
```

### 环境变量

```bash
# 设置默认账户（避免重复 --account）
export GOG_ACCOUNT=you@gmail.com
```

## 核心功能详解

### 1. Gmail 邮件管理

#### 搜索邮件

```bash
# 搜索最近 7 天的邮件（最多 10 封）
gog gmail search 'newer_than:7d' --max 10

# 按发件人和文件夹搜索（返回单封邮件，忽略线程）
gog gmail messages search "in:inbox from:ryanair.com" --max 20 --account you@example.com
```

#### 发送邮件

```bash
# 纯文本邮件
gog gmail send --to a@b.com --subject "Hi" --body "Hello"

# 多行文本（从文件读取）
gog gmail send --to a@b.com --subject "Hi" --body-file ./message.txt

# 从 stdin 读取（推荐用于脚本）
gog gmail send --to a@b.com --subject "Hi" --body-file -

# HTML 格式邮件
gog gmail send --to a@b.com --subject "Hi" --body-html "<p>Hello</p>"
```

#### HereDoc 示例

```bash
gog gmail send --to recipient@example.com \
  --subject "Meeting Follow-up" \
  --body-file - <<'EOF'
Hi Name,

Thanks for meeting today. Next steps:
- Item one
- Item two

Best regards,
Your Name
EOF
```

#### 草稿管理

```bash
# 创建草稿
gog gmail drafts create --to a@b.com --subject "Hi" --body-file ./message.txt

# 发送草稿
gog gmail drafts send <draftId>
```

#### 回复邮件

```bash
# 回复指定邮件
gog gmail send --to a@b.com --subject "Re: Hi" --body "Reply" --reply-to-message-id <msgId>
```

### 2. Calendar 日历管理

#### 查询事件

```bash
# 列出指定日期范围的事件
gog calendar events <calendarId> --from 2026-03-01 --to 2026-03-07
```

#### 创建事件

```bash
# 基本创建
gog calendar create <calendarId> --summary "Title" --from 2026-03-02T10:00:00 --to 2026-03-02T11:00:00

# 带颜色标记
gog calendar create <calendarId> --summary "Title" --from 2026-03-02T10:00:00 --to 2026-03-02T11:00:00 --event-color 7
```

#### 更新事件

```bash
# 修改标题和颜色
gog calendar update <calendarId> <eventId> --summary "New Title" --event-color 4
```

#### 颜色代码

| ID | 颜色 |
|----|------|
| 1 | 淡蓝 #a4bdfc |
| 2 | 绿色 #7ae7bf |
| 3 | 淡紫 #dbadff |
| 4 | 红色 #ff887c |
| 5 | 黄色 #fbd75b |
| 6 | 橙色 #ffb878 |
| 7 | 青色 #46d6db |
| 8 | 灰色 #e1e1e1 |
| 9 | 蓝色 #5484ed |
| 10 | 深绿 #51b749 |
| 11 | 深红 #dc2127 |

```bash
# 查看所有颜色
gog calendar colors
```

### 3. Google Sheets 表格操作

#### 读取数据

```bash
# 读取单元格范围（JSON 格式）
gog sheets get <sheetId> "Tab!A1:D10" --json
```

#### 更新数据

```bash
# 更新单元格（覆盖）
gog sheets update <sheetId> "Tab!A1:B2" --values-json '[["A","B"],["1","2"]]' --input USER_ENTERED
```

#### 追加数据

```bash
# 追加行
gog sheets append <sheetId> "Tab!A:C" --values-json '[["x","y","z"]]' --insert INSERT_ROWS
```

#### 清除数据

```bash
# 清除范围
gog sheets clear <sheetId> "Tab!A2:Z"
```

#### 元数据查询

```bash
# 获取表格元数据
gog sheets metadata <sheetId> --json
```

### 4. Google Docs 文档操作

```bash
# 导出为文本文件
gog docs export <docId> --format txt --out /tmp/doc.txt

# 直接输出内容
gog docs cat <docId>
```

### 5. Drive & Contacts

```bash
# Drive 搜索
gog drive search "query" --max 10

# 联系人列表
gog contacts list --max 20
```

## 使用场景

### 场景 1: 自动化会议跟进邮件

```bash
# 发送带格式的跟进邮件
gog gmail send --to team@company.com \
  --subject "Weekly Standup Notes" \
  --body-file - <<'EOF'
Team,

Today's standup summary:
- ✅ Feature A deployed
- 🔄 Feature B in review
- 📋 Feature C planned

Actions:
1. Review PR #123
2. Update documentation

Best,
Bot
EOF
```

### 场景 2: 日历事件自动化

```bash
# 批量创建周会事件
for day in Monday Tuesday Wednesday Thursday Friday; do
  gog calendar create primary \
    --summary "Daily Standup" \
    --from "2026-03-02T09:00:00" \
    --to "2026-03-02T09:15:00" \
    --event-color 2
done
```

### 场景 3: 表格数据同步

```bash
# 追加日志数据到表格
gog sheets append <sheetId> "Logs!A:E" \
  --values-json '[["2026-03-02","INFO","User login","success","192.168.1.1"]]' \
  --insert INSERT_ROWS
```

### 场景 4: 邮件搜索与分析

```bash
# 搜索并统计未读邮件
gog gmail search 'is:unread' --max 100 --json | jq 'length'

# 搜索特定发件人的邮件
gog gmail messages search "from:notifications@github.com" --max 50 --json
```

## 最佳实践

### 1. 邮件格式选择

- **优先使用纯文本**：`--body-file` 或 `--body`
- **需要格式时使用 HTML**：`--body-html`
- **多段落内容用 HereDoc**：`--body-file -`

### 2. 脚本友好模式

```bash
# 使用 JSON 输出便于解析
gog gmail search 'newer_than:1d' --json --no-input

# 设置默认账户
export GOG_ACCOUNT=you@gmail.com
```

### 3. 确认机制

- **发送邮件前确认**：避免误发
- **创建日历事件前确认**：避免重复

### 4. 注意事项

- `--body` 不解析 `\n` 转义，需要换行请用 `--body-file`
- `gog gmail search` 返回线程摘要，`messages search` 返回单封邮件
- Sheets 的 `--values-json` 需要是二维数组

## 依赖要求

| 依赖 | 说明 |
|------|------|
| `gog` CLI | 主程序，通过 Homebrew 安装 |
| OAuth 凭证 | Google Cloud Console 生成的 client_secret.json |
| Google API | 需要启用 Gmail、Calendar、Drive 等 API |

## 与其他 Skills 对比

| Skill | 优势 | 适用场景 |
|-------|------|----------|
| **gog** | 功能全面，覆盖所有 Google Workspace | 企业办公、G Suite 用户 |
| **himalaya** | 轻量级，支持多协议 IMAP/SMTP | 个人邮件、自建服务器 |
| **notion** | 知识库和项目管理 | 文档协作、Wiki |

## 安全考虑

1. **OAuth 凭证安全**：
   - `client_secret.json` 应妥善保管
   - 避免提交到版本控制

2. **权限最小化**：
   - 只授权需要的服务
   - 定期检查授权列表

3. **敏感邮件**：
   - 发送前预览内容
   - 避免在命令行中直接写敏感信息

## 故障排除

### 认证失败

```bash
# 重新添加账户
gog auth add you@gmail.com --services gmail,calendar
```

### API 配额限制

- Google API 有每日配额限制
- 使用 `--max` 限制查询数量

### JSON 解析错误

- 确保 `--values-json` 格式正确
- 使用 `jq` 验证 JSON 格式

## 总结

**gog** 是一个功能强大的 Google Workspace CLI 工具，适合：

- ✅ 自动化邮件发送和回复
- ✅ 日历事件批量管理
- ✅ 表格数据读写自动化
- ✅ 文档导出和处理
- ✅ 企业办公流程集成

**推荐指数**：⭐⭐⭐⭐⭐ (5/5)

对于使用 Google Workspace 的用户来说，这是一个必备的自动化工具，可以显著提升办公效率。

---

*分析时间: 2026-03-02 10:32*
*分析者: Glint 🔺*
