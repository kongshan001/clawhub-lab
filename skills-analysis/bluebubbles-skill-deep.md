# BlueBubbles Skill 深度分析报告

## 📊 基本信息

| 属性 | 值 |
|------|-----|
| **名称** | bluebubbles |
| **类型** | OpenClaw 内置 Channel Plugin |
| **Emoji** | 🫧 |
| **分类** | 通信 / iMessage |
| **依赖** | BlueBubbles macOS 应用 + REST API |

## 🎯 核心功能

### 一句话总结
OpenClaw 推荐的 iMessage 集成方案，通过 BlueBubbles 实现 iOS/macOS 消息的发送、接收和高级交互。

### 主要能力

| 功能 | 支持 | 说明 |
|------|------|------|
| 发送消息 | ✅ | 文本消息发送 |
| 接收消息 | ✅ | Webhook 实时接收 |
| 附件 | ✅ | 图片、文件等 |
| 反应 (Tapback) | ✅ | 6 种标准反应 |
| 编辑消息 | ✅ | iOS 16+ 特性 |
| 撤回消息 | ✅ | iOS 16+ 特性 |
| 回复线程 | ✅ | 需要 Private API |
| 消息效果 | ✅ | 气球、烟花等 |
| 群组管理 | ✅ | 群名、图标、成员 |

## 🔧 技术架构

### 系统架构

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│   OpenClaw      │────▶│  BlueBubbles     │────▶│  iMessage       │
│   (Agent)       │◀────│  Server (macOS)  │◀────│  (Apple)        │
└─────────────────┘     └──────────────────┘     └─────────────────┘
        │                       │
        │   REST API            │   Webhook
        │   (发送消息)           │   (接收消息)
        ▼                       ▼
```

### 配置要求

```yaml
# openclaw.yaml
channels:
  bluebubbles:
    serverUrl: "http://your-mac:1234"
    password: "your-password"
    webhookPath: "/webhook/bluebubbles"
    actions:
      reactions: true  # 默认启用
```

### 核心模块

| 模块 | 文件 | 功能 |
|------|------|------|
| 通道插件 | `channel.ts` | 主入口，能力声明 |
| 消息发送 | `send.ts` | 文本消息发送 |
| 媒体发送 | `media-send.ts` | 附件上传 |
| 反应处理 | `reactions.ts` | Tapback 逻辑 |
| Webhook | `monitor.ts` | 消息接收 |
| 目标解析 | `targets.ts` | 地址规范化 |

## 💡 使用场景

### 1. 个人助手场景

```json
// 发送提醒消息
{
  "action": "send",
  "channel": "bluebubbles",
  "target": "+15551234567",
  "message": "📅 提醒：下午3点有会议"
}
```

### 2. 工作通知场景

```json
// 发送带效果的重要通知
{
  "action": "sendWithEffect",
  "channel": "bluebubbles",
  "target": "chat_guid:iMessage;+;chat123",
  "message": "🚀 产品已上线！",
  "effect": "confetti"
}
```

### 3. 自动回复场景

```json
// 对消息进行反应确认
{
  "action": "react",
  "channel": "bluebubbles",
  "target": "+15551234567",
  "messageId": "ABC123-GUID",
  "emoji": "👍"
}
```

### 4. 线程回复场景

```json
// 在特定消息线程中回复
{
  "action": "reply",
  "channel": "bluebubbles",
  "target": "+15551234567",
  "replyTo": "parent-message-guid",
  "message": "关于这个问题的补充..."
}
```

## 🎭 消息效果 (Effects)

### 气泡效果 (Bubble Effects)

| 名称 | Effect ID | 描述 |
|------|-----------|------|
| slam | `expressivesend.impact` | 重击 |
| loud | `expressivesend.loud` | 大声 |
| gentle | `expressivesend.gentle` | 轻柔 |
| invisible | `expressivesend.invisibleink` | 隐形墨水 |

### 屏幕效果 (Screen Effects)

| 名称 | Effect ID | 描述 |
|------|-----------|------|
| echo | `CKEchoEffect` | 回声 |
| spotlight | `CKSpotlightEffect` | 聚光灯 |
| balloons | `CKHappyBirthdayEffect` | 🎈 气球 |
| confetti | `CKConfettiEffect` | 🎊 五彩纸屑 |
| love/heart | `CKHeartEffect` | ❤️ 爱心 |
| lasers | `CKLasersEffect` | 激光 |
| fireworks | `CKFireworksEffect` | 🎆 烟花 |
| celebration | `CKSparklesEffect` | ✨ 庆祝 |

## 👍 反应类型 (Tapbacks)

### 支持的反应

| 类型 | 关键词 | Emoji |
|------|--------|-------|
| Love | heart, love, ❤️ | ❤️ |
| Like | thumbsup, like, 👍 | 👍 |
| Dislike | thumbsdown, dislike, 👎 | 👎 |
| Laugh | haha, lol, 😂 | 😂 |
| Emphasize | exclaim, !!, ‼️ | ‼️ |
| Question | ?, ❓ | ❓ |

### 反应别名系统

```typescript
// 支持多种输入形式
"heart" → "love"
"thumbs_up" → "like"
"haha" → "laugh"
"!!" → "emphasize"
"?" → "question"
```

## 🔄 消息流程

### 发送流程

```
1. 解析目标地址 (target)
   ├─ E.164 格式: +15551234567
   ├─ Email: user@example.com
   └─ Chat GUID: chat_guid:iMessage;+;...

2. 构建 API 请求
   ├─ 鉴权 (password)
   ├─ 消息内容
   └─ 可选效果/回复引用

3. 调用 BlueBubbles REST API
   POST /api/v1/message/text

4. 返回消息 ID
```

### 接收流程

```
1. BlueBubbles 推送 Webhook
   ├─ 消息内容
   ├─ 发送者信息
   └─ 附件 (如有)

2. OpenClaw 处理
   ├─ 跳过自己的消息
   ├─ 规范化 ID
   └─ 媒体占位符 <media:...>

3. 触发 Agent 处理
```

## 📱 目标地址格式

### 推荐格式

```bash
# 群聊 (推荐)
"chat_guid:iMessage;+;chat-identifier"

# 个人 (E.164)
"+15551234567"

# 个人 (Email)
"user@icloud.com"
```

### 地址解析逻辑

```typescript
// 自动规范化
"+1 (555) 123-4567" → "+15551234567"
"user@icloud.com" → 保持原样
```

## ⚠️ 限制和注意事项

### 1. Private API 依赖

| 功能 | 需要 Private API |
|------|------------------|
| 基本发送 | ❌ |
| 附件 | ❌ |
| 反应 (Tapback) | ✅ |
| 编辑消息 | ✅ |
| 撤回消息 | ✅ |
| 回复线程 | ✅ |
| 消息效果 | ✅ |

### 2. macOS 版本兼容性

- **编辑/撤回**: macOS Ventura+ (iOS 16+)
- **部分功能**: macOS 26 Tahoe 可能存在问题

### 3. 配置要求

- BlueBubbles macOS 应用必须运行
- 服务器需要在局域网或可访问
- 首次配对需要 Hue Bridge 按钮确认

### 4. 群聊限制

- 群聊优先使用 `chat_guid` 格式
- 群聊可能需要 `@提及` 才能触发机器人

## 🔍 对比分析

### BlueBubbles vs imsg Skill

| 特性 | BlueBubbles | imsg |
|------|-------------|------|
| 平台 | macOS + API | macOS AppleScript |
| 远程访问 | ✅ | ❌ |
| 群聊支持 | ✅ 完整 | ⚠️ 有限 |
| Webhook | ✅ | ❌ |
| 高级功能 | ✅ 编辑/撤回 | ❌ |
| 配置复杂度 | 中等 | 简单 |

### BlueBubbles vs 原生 iMessage

| 特性 | BlueBubbles | 原生 |
|------|-------------|------|
| 自动化 | ✅ | ❌ |
| API | ✅ REST | ❌ |
| 跨设备 | ✅ | ❌ |
| 稳定性 | ⚠️ 依赖服务 | ✅ |

## 🚀 最佳实践

### 1. 错误处理

```typescript
try {
  await sendBlueBubblesReaction({...});
} catch (e) {
  if (e.message.includes("Private API")) {
    // 提示用户启用 Private API
  }
}
```

### 2. 目标地址选择

```typescript
// ✅ 推荐：群聊使用 chat_guid
target: "chat_guid:iMessage;+;group123"

// ⚠️ 可用：个人使用 E.164
target: "+15551234567"

// ❌ 避免：模糊引用
target: "my mom" // 需要明确地址
```

### 3. 消息 ID 管理

```typescript
// 使用完整消息 ID
messageId: "ABC123-GUID-FULL-LENGTH"

// 避免使用短 ID（可能在重启后失效）
```

### 4. Webhook 安全

```yaml
# 配置 webhook 路径
webhookPath: "/webhook/bluebubbles/secret-token"
```

## 📊 技术指标

| 指标 | 值 |
|------|-----|
| 代码文件数 | ~15 |
| 核心模块 | channel, send, reactions, monitor |
| API 端点 | `/api/v1/message/*` |
| 超时配置 | 可配置 (timeoutMs) |
| 反应别名数 | 40+ |

## 🎓 扩展阅读

### 相关 Skills

- **imsg** - macOS 原生 iMessage (AppleScript)
- **discord** - Discord 通信
- **slack** - Slack 团队协作

### 外部资源

- [BlueBubbles 官网](https://bluebubbles.app)
- [BlueBubbles API 文档](https://documenter.getpostman.com/view/7276462/2s9YC1Vx3i)
- [OpenClaw Channels 文档](/channels/bluebubbles)

## 📝 总结

### ✅ 优势

1. **功能最完整的 iMessage 集成** - 支持编辑、撤回、效果等高级特性
2. **Webhook 支持** - 实时接收消息，实现真正的双向通信
3. **群聊友好** - 完整的群组管理能力
4. **丰富的别名系统** - 反应输入灵活多样

### ⚠️ 挑战

1. **依赖 Private API** - 高级功能需要额外配置
2. **macOS 必须运行** - 需要一台 Mac 作为服务器
3. **版本兼容性** - 部分功能依赖 macOS/iOS 版本

### 🎯 适用场景

- ✅ 需要完整 iMessage 自动化的用户
- ✅ 群聊机器人场景
- ✅ 需要双向消息交互的应用
- ❌ 简单通知推送 (考虑 imsg)

### 🔮 未来展望

- 更多的消息效果支持
- 改进的 macOS 版本兼容性
- 可能的 Rich Link 支持

---

*分析时间: 2026-03-02 09:42*  
*分析者: Glint 🔺*  
*数据来源: OpenClaw 内置 Skill + 源码分析*
