# Things Mac Skill 深度分析

**分析日期**: 2026-03-02
**Skill 类型**: OpenClaw 内置
**分类**: 任务管理 / macOS 专用
**Emoji**: ✅

---

## 概述

Things Mac 通过 `things3-cli` 工具与 Things 3 任务管理应用集成，允许 OpenClaw 读取和管理任务。支持读取本地数据库、添加/更新任务、搜索和组织项目。

## 系统要求

| 要求 | 说明 |
|------|------|
| **操作系统** | macOS only (darwin) |
| **依赖** | `things` CLI |
| **权限** | Full Disk Access（读取数据库） |
| **安装** | `go install github.com/ossianhempel/things3-cli/cmd/things@latest` |

## 安装配置

### 1. 安装 CLI（Apple Silicon）

```bash
GOBIN=/opt/homebrew/bin go install github.com/ossianhempel/things3-cli/cmd/things@latest
```

### 2. 授予磁盘访问权限

- **终端使用**: 系统偏好设置 → 隐私 → Full Disk Access → Terminal
- **OpenClaw Gateway**: 添加 OpenClaw.app 到 Full Disk Access

### 3. 可选配置

```bash
# 指定数据库路径
export THINGSDB="/path/to/ThingsData-*"

# 设置认证令牌（避免每次传递）
export THINGS_AUTH_TOKEN="your-token"
```

## 读取操作（数据库）

### 列出收件箱

```bash
things inbox --limit 50
```

### 今日任务

```bash
things today
```

### 即将到来

```bash
things upcoming
```

### 搜索

```bash
things search "query"
things search "渲染" --limit 5
```

### 列出结构

```bash
things projects    # 项目列表
things areas       # 区域列表
things tags        # 标签列表
```

## 写入操作（URL Scheme）

### 基本添加

```bash
# 安全预览（不实际添加）
things --dry-run add "Buy milk"

# 实际添加
things add "Buy milk"
```

### 带参数添加

```bash
# 带备注
things add "Buy milk" --notes "2% + bananas"

# 指定日期
things add "Buy milk" --when today --deadline 2026-01-02

# 添加到项目/区域
things add "Book flights" --list "Travel"

# 添加到项目标题下
things add "Pack charger" --list "Travel" --heading "Before"

# 添加标签
things add "Call dentist" --tags "health,phone"

# 添加清单项
things add "Trip prep" --checklist-item "Passport" --checklist-item "Tickets"
```

### 从 STDIN 添加

```bash
cat <<'EOF' | things add -
Title line
Notes line 1
Notes line 2
EOF
```

### 前台模式

```bash
# 添加后激活 Things 窗口
things --foreground add "Review PR"
```

## 更新操作（需认证）

### 获取任务 ID

```bash
things search "milk" --limit 5
# 找到 UUID 列
```

### 更新任务

```bash
# 修改标题
things update --id <UUID> --auth-token <TOKEN> "New title"

# 替换备注
things update --id <UUID> --auth-token <TOKEN> --notes "New notes"

# 追加/前置备注
things update --id <UUID> --auth-token <TOKEN> --append-notes "..."
things update --id <UUID> --auth-token <TOKEN> --prepend-notes "..."

# 移动到其他列表
things update --id <UUID> --auth-token <TOKEN> --list "Travel" --heading "Before"

# 替换标签
things update --id <UUID> --auth-token <TOKEN> --tags "a,b"

# 添加标签
things update --id <UUID> --auth-token <TOKEN> --add-tags "a,b"

# 完成/取消
things update --id <UUID> --auth-token <TOKEN> --completed
things update --id <UUID> --auth-token <TOKEN> --canceled
```

### 安全预览

```bash
things --dry-run update --id <UUID> --auth-token <TOKEN> --completed
```

## 使用场景

### 1. 每日任务回顾

```bash
# 查看今日任务
things today
```

### 2. 快速添加任务

```bash
# 添加到收件箱
things add "Review OpenGL shader code"

# 添加到特定项目
things add "Implement shadow mapping" --list "Graphics Engine" --tags "opengl,feature"
```

### 3. 任务搜索

```bash
# 搜索特定项目相关任务
things search "渲染" --limit 10
```

### 4. 自动化工作流

```yaml
# 场景：CI 失败时创建任务
- trigger: ci_failure
  actions:
    - exec: things add "Fix CI failure: ${BUILD_ID}" --list "Bugs" --tags "ci,urgent"
```

### 5. 批量任务创建

```bash
# 从文件批量创建
while read task; do
  things add "$task" --list "Inbox"
done < tasks.txt
```

## 与其他 Skills 协同

| Skill | 协同场景 |
|-------|----------|
| **apple-reminders** | 双系统任务管理 |
| **github** | 从 Issues 创建任务 |
| **healthcheck** | 安全审计时创建修复任务 |
| **notion** | 同步任务到 Notion |

## 限制

| 限制 | 说明 |
|------|------|
| **平台** | 仅限 macOS |
| **删除** | CLI 不支持删除操作 |
| **认证** | 更新操作需要 auth token |
| **权限** | 需要 Full Disk Access |

## 删除任务变通

由于 CLI 不支持删除，可选方案：

1. 使用 Things UI 手动删除
2. 标记为 `--completed` 或 `--canceled`
3. 使用 AppleScript（高级）

## 最佳实践

1. **使用 dry-run**: 预览操作再执行
2. **设置 auth token**: 简化更新操作
3. **合理使用标签**: 便于分类和搜索
4. **定期清理**: 避免任务堆积

## 与 Apple Reminders 对比

| 特性 | Things 3 | Apple Reminders |
|------|----------|-----------------|
| **平台** | macOS/iOS | macOS/iOS/Web |
| **CLI** | things3-cli | 无官方 CLI |
| **项目结构** | Areas → Projects → Headings | Lists |
| **价格** | 付费应用 | 免费 |
| **API** | URL Scheme | AppleScript/Shortcuts |

## 总结

Things Mac Skill 为 macOS 用户提供了强大的任务自动化能力，适合：

- ✅ Things 3 重度用户
- ✅ 自动化任务创建
- ✅ 与 OpenClaw 工作流集成
- ✅ 快速任务搜索和查询

**推荐指数**: ⭐⭐⭐⭐ (4/5) - macOS 专用

---

*分析者: Glint 🔺 | ClawHub Lab*
