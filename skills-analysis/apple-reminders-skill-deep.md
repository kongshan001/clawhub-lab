# Apple Reminders Skill 深度分析

## 📋 基本信息

| 属性 | 值 |
|------|-----|
| 名称 | apple-reminders |
| 评分 | N/A（内置 Skill） |
| 平台 | macOS 专属 |
| 依赖 | `remindctl` CLI |
| 安装 | `brew install steipete/tap/remindctl` |
| 仓库 | https://github.com/steipete/remindctl |

## 🎯 核心功能

Apple Reminders Skill 让 OpenClaw 能够通过命令行管理 Apple 的提醒事项应用。

### 功能清单

1. **查看提醒**
   - 按时间过滤：today, tomorrow, week, overdue
   - 按日期查询：YYYY-MM-DD
   - 查看所有：`remindctl all`

2. **管理列表**
   - 列出所有列表
   - 创建/删除列表
   - 查看特定列表内容

3. **创建提醒**
   - 简单文本创建
   - 指定列表
   - 设置截止日期

4. **完成/删除**
   - 通过 ID 完成提醒
   - 强制删除提醒

5. **输出格式**
   - JSON（脚本友好）
   - TSV（表格格式）
   - 计数模式

## 💻 技术架构

```
┌─────────────────┐
│   OpenClaw      │
│   Agent         │
└────────┬────────┘
         │ 调用
         ▼
┌─────────────────┐
│   remindctl     │  ← Homebrew 安装
│   CLI           │
└────────┬────────┘
         │ Apple API
         ▼
┌─────────────────┐
│  Apple          │
│  Reminders.app  │  ← iCloud 同步
└─────────────────┘
         │
         ▼
┌─────────────────┐
│  iOS/iPadOS     │
│  Reminders      │
└─────────────────┘
```

## 🔧 常用命令示例

### 查看今日提醒
```bash
remindctl today
```

### 创建带截止日期的提醒
```bash
remindctl add --title "团队会议" --list Work --due "2026-03-05 14:00"
```

### 查看特定列表
```bash
remindctl list Projects
```

### JSON 输出（便于脚本处理）
```bash
remindctl week --json | jq '.[] | .title'
```

## ⚠️ 使用边界

### ✅ 适用场景

- 用户明确提到 "提醒" 或 "Reminders app"
- 创建需要同步到 iPhone/iPad 的个人待办
- 管理 Apple Reminders 列表
- 希望任务出现在手机提醒事项中

### ❌ 不适用场景

| 场景 | 替代方案 |
|------|----------|
| OpenClaw 定时提醒 | `cron` 工具 + systemEvent |
| 日历事件 | Apple Calendar |
| 项目/工作管理 | Notion, GitHub Issues |
| 一次性通知 | `cron` 工具 |
| "提醒我"但指 Clawdbot 警报 | 先确认用户意图 |

## 🔐 权限要求

1. **首次运行**需要授权：
   ```bash
   remindctl authorize
   ```

2. **系统权限**：
   - 需要访问 Reminders 数据库的权限
   - macOS 会弹出授权对话框

3. **iCloud**：
   - 需要 iCloud 登录以实现跨设备同步

## 📊 与其他任务管理工具对比

| 特性 | Apple Reminders | Notion | GitHub Issues |
|------|-----------------|--------|---------------|
| 平台 | macOS/iOS | 跨平台 | 跨平台 |
| 离线 | ✅ | ❌ | ❌ |
| 同步 | iCloud | 云端 | Git |
| 复杂度 | 低 | 高 | 中 |
| API | 有限 | 完整 | 完整 |
| OpenClaw 集成 | remindctl | notion skill | gh CLI |

## 🎓 最佳实践

### 1. 意图澄清
当用户说 "提醒我..." 时：
```
用户: "提醒我2小时后检查部署"

Agent: 您想要：
A) Apple Reminders（同步到手机）
B) Clawdbot 警报（我会在这里通知您）
```

### 2. 列表管理
建议按场景分列表：
- `Personal` - 个人事务
- `Work` - 工作任务
- `Shopping` - 购物清单
- `Health` - 健康提醒

### 3. 日期格式标准化
```bash
# 推荐 ISO 格式
remindctl add --title "任务" --due "2026-03-05 09:00"

# 避免模糊表达
remindctl add --title "任务" --due "下周"  # ❌ 不支持
```

## 🚀 高级用法

### 批量操作（通过 JSON）
```bash
# 导出本周提醒
remindctl week --json > reminders.json

# 处理后重新导入（需自行解析）
cat reminders.json | jq -r '.[] | .title'
```

### 与 OpenClaw 工作流集成
```
用户: "每周五下午5点提醒我写周报"

Agent 执行:
1. 确认使用 Apple Reminders
2. remindctl add --title "写周报" --list Work --due "friday 17:00"
3. 告知用户已创建
```

## 📈 限制与注意事项

1. **macOS 专属**
   - 无法在 Linux/Windows 上使用
   - 依赖 Apple 私有 API

2. **无循环提醒**
   - `remindctl` 本身不支持循环
   - 需要在 Apple Reminders app 中手动设置

3. **ID 不稳定**
   - 完成后 ID 可能变化
   - 建议立即操作，不要缓存 ID

4. **无子任务**
   - 不支持嵌套提醒
   - 复杂任务考虑 Notion

## 🔗 相关 Skills

| Skill | 用途 |
|-------|------|
| notion | 复杂项目管理 |
| trello | 看板式任务管理 |
| apple-notes | 笔记与轻量待办 |
| things-mac | 专业 GTD 工具 |

## ✅ 总结

Apple Reminders Skill 是一个轻量级、macOS 专属的任务管理工具。它的核心价值在于：

1. **无缝同步** - 通过 iCloud 自动同步到 iOS 设备
2. **简单易用** - 命令简洁，学习成本低
3. **原生集成** - 直接操作 Apple 生态应用
4. **快速响应** - 适合即时创建提醒

**推荐使用场景**：个人轻量待办、需要手机同步的提醒、快速任务捕获。

**不推荐场景**：复杂项目管理、团队协作、需要循环/子任务的场景。

---

*分析时间: 2026-03-02 06:02 by Glint 🔺*
