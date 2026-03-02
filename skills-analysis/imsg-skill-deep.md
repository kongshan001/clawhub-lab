# imsg Skill 深度分析报告

**分析日期**: 2026-03-02  
**Skill 版本**: 内置  
**评分**: N/A (内置 Skill)  
**分析者**: Glint 🔺

---

## 📋 概览

**imsg** 是一个 macOS 专用的 iMessage/SMS 命令行工具，通过 Messages.app 实现消息的读取和发送功能。

### 核心价值

- 📱 **原生集成**：直接使用 macOS Messages.app
- 💬 **双向通信**：支持读取历史和发送新消息
- 📎 **多媒体支持**：可发送文本和附件
- 🔍 **聊天搜索**：列出和查找聊天记录

---

## 🎯 适用场景

### ✅ 应该使用

1. **发送 iMessage/SMS**
   - 发送消息到手机号或 Apple ID
   - 支持文本和附件
   - 可指定服务类型（iMessage/SMS）

2. **读取消息历史**
   - 查看聊天记录
   - 获取附件信息
   - 监控新消息

3. **管理聊天**
   - 列出最近的聊天
   - 搜索特定联系人

### ❌ 不应该使用

1. **其他平台消息**
   - Telegram → 使用 `message` 工具 + `channel:telegram`
   - Signal → 使用 Signal channel
   - WhatsApp → 使用 WhatsApp channel
   - Discord → 使用 `message` 工具 + `channel:discord`

2. **群组管理**
   - 不支持添加/删除成员
   - 不支持群组管理功能

3. **批量操作**
   - 批量发送需要用户明确确认
   - 避免垃圾消息行为

---

## 🔧 技术架构

### 依赖项

- **操作系统**: macOS（仅限）
- **应用**: Messages.app（需要登录）
- **权限**:
  - Full Disk Access（终端）
  - Automation permission（Messages.app 发送权限）

### 安装方式

```bash
brew install steipete/tap/imsg
```

### 核心命令

#### 1. 列出聊天

```bash
imsg chats --limit 10 --json
```

返回最近的聊天列表，包含聊天 ID、显示名称等信息。

#### 2. 查看历史

```bash
# 基本历史
imsg history --chat-id 1 --limit 20 --json

# 包含附件信息
imsg history --chat-id 1 --limit 20 --attachments --json
```

#### 3. 监控新消息

```bash
imsg watch --chat-id 1 --attachments
```

实时监控指定聊天的新消息。

#### 4. 发送消息

```bash
# 纯文本
imsg send --to "+14155551212" --text "Hello!"

# 带附件
imsg send --to "+14155551212" --text "Check this out" \
  --file /path/to/image.jpg

# 指定服务类型
imsg send --to "+14155551212" --text "Hi" --service imessage
imsg send --to "+14155551212" --text "Hi" --service sms
```

---

## 🔒 安全与隐私

### 权限要求

1. **Full Disk Access**
   - 终端需要完全磁盘访问权限
   - 用于读取 Messages.app 数据库

2. **Automation Permission**
   - Messages.app 自动化权限
   - 用于发送消息

### 安全规则

1. **确认机制**
   ```
   ⚠️ 总是确认收件人和消息内容后再发送
   ```

2. **未知号码**
   ```
   ⚠️ 不要向未知号码发送消息，除非用户明确批准
   ```

3. **附件验证**
   ```
   ⚠️ 发送附件前确认文件路径存在
   ```

4. **速率限制**
   ```
   ⚠️ 自我限制，避免发送垃圾消息
   ```

---

## 💡 实战示例

### 场景 1: 发送通知给家人

**用户请求**: "Text mom that I'll be late"

```bash
# 步骤 1: 查找妈妈的聊天
imsg chats --limit 20 --json | jq '.[] | select(.displayName | contains("Mom"))'

# 步骤 2: 确认
# "Found Mom at +1555123456. Send 'I'll be late' via iMessage?"

# 步骤 3: 发送（确认后）
imsg send --to "+1555123456" --text "I'll be late"
```

### 场景 2: 查看最近的聊天

```bash
# 列出最近 10 个聊天
imsg chats --limit 10 --json

# 查看特定聊天的最近 20 条消息
imsg history --chat-id 5 --limit 20 --json
```

### 场景 3: 发送图片

```bash
# 发送带图片的消息
imsg send --to "+14155551212" \
  --text "Check out this photo" \
  --file ~/Pictures/vacation.jpg
```

### 场景 4: 监控工作聊天

```bash
# 实时监控工作群组消息
imsg watch --chat-id 12 --attachments
```

---

## 🔄 与其他工具集成

### 与 OpenClaw 工作流集成

```bash
# 示例：每日提醒
# 结合 cron 或 OpenClaw 的定时任务

# 早上提醒
imsg send --to "+1555123456" --text "Good morning! Don't forget your meeting at 10am"
```

### 与其他 Skills 配合

1. **weather skill** + **imsg**
   ```bash
   # 获取天气后发送
   weather_result=$(weather new-york)
   imsg send --to "+1555123456" --text "Weather today: $weather_result"
   ```

2. **notion skill** + **imsg**
   ```bash
   # 从 Notion 获取任务列表后发送
   # (伪代码，需要实际的 Notion API 调用)
   tasks=$(notion get-tasks --today)
   imsg send --to "+1555123456" --text "Today's tasks: $tasks"
   ```

---

## 📊 优缺点分析

### ✅ 优点

1. **原生体验**
   - 直接使用 macOS 原生 Messages.app
   - 无需第三方 API 或服务

2. **简单直接**
   - 命令行界面简洁
   - JSON 输出便于解析

3. **多媒体支持**
   - 支持发送附件
   - 可查看聊天中的附件信息

4. **实时监控**
   - `watch` 命令支持实时监控

### ❌ 缺点

1. **平台限制**
   - 仅支持 macOS
   - 无法在 Linux/Windows 使用

2. **权限要求高**
   - 需要 Full Disk Access
   - 需要 Automation permission

3. **功能有限**
   - 不支持群组管理
   - 不支持高级搜索

4. **依赖本地应用**
   - 需要 Messages.app 登录
   - 无法远程使用

---

## 🎓 最佳实践

### 1. 权限设置

```bash
# 确保正确设置权限
# System Preferences → Privacy & Security → Full Disk Access
# 添加 Terminal (or your terminal app)

# System Preferences → Privacy & Security → Automation
# 允许终端控制 Messages.app
```

### 2. 错误处理

```bash
# 检查命令是否存在
if ! command -v imsg &> /dev/null; then
    echo "imsg not installed. Installing..."
    brew install steipete/tap/imsg
fi

# 检查发送结果
if imsg send --to "+14155551212" --text "Test"; then
    echo "Message sent successfully"
else
    echo "Failed to send message"
fi
```

### 3. 批量操作的安全模式

```bash
# 安全的批量发送（带确认）
for contact in "${contacts[@]}"; do
    read -p "Send to $contact? (y/n) " -n 1 -r
    echo
    if [[ $REPLY =~ ^[Yy]$ ]]; then
        imsg send --to "$contact" --text "$message"
    fi
done
```

---

## 🔍 故障排查

### 问题 1: 权限错误

```
Error: Access denied to Messages.app
```

**解决方案**:
1. 检查 Full Disk Access 权限
2. 检查 Automation 权限
3. 重启终端应用

### 问题 2: 找不到聊天

```
Error: Chat not found
```

**解决方案**:
1. 使用 `imsg chats` 列出所有聊天
2. 确认 chat-id 正确
3. 检查 Messages.app 是否已登录

### 问题 3: 发送失败

```
Error: Failed to send message
```

**解决方案**:
1. 检查收件人号码格式（国际格式：+1XXXXXXXXXX）
2. 确认 Messages.app 可以正常发送
3. 检查网络连接

---

## 🚀 未来展望

### 潜在改进方向

1. **跨平台支持**
   - 通过远程 macOS 主机使用
   - Web API 接口

2. **增强功能**
   - 群组管理
   - 高级搜索
   - 消息模板

3. **集成增强**
   - 与更多 OpenClaw Skills 深度集成
   - 自动化工作流模板

---

## 📚 参考资源

- **官方主页**: https://imsg.to
- **安装源**: steipete/tap/imsg (Homebrew)
- **Skill 位置**: `/usr/lib/node_modules/openclaw/skills/imsg/`

---

## 📝 总结

**imsg** 是一个专门为 macOS 用户设计的 iMessage/SMS 自动化工具，适合需要通过命令行管理消息的用户。

### 适用性评估

- **macOS 用户**: ⭐⭐⭐⭐⭐ (5/5)
- **Linux 用户**: ❌ (不支持)
- **Windows 用户**: ❌ (不支持)

### 推荐指数

**⭐⭐⭐⭐ (4/5)**

- 功能实用但平台受限
- 权限要求较高
- 适合深度 macOS 用户

---

*分析完成于 2026-03-02 09:15 (Asia/Shanghai)*  
*Glint 🔺 - 图形精灵*
