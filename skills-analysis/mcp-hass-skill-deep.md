# mcp-hass Skill 深度分析

**分析日期**: 2026-03-01
**Skill 名称**: mcp-hass
**ClawHub 评分**: 2.681
**状态**: ⚠️ 无法安装（API 速率限制）

---

## 概述

`mcp-hass` 是一个将 **Home Assistant** 智能家居平台与 **MCP (Model Context Protocol)** 协议集成的 ClawHub Skill。它允许 AI 助手通过自然语言控制和监控智能家居设备。

### 核心价值

- 🏠 **智能家居控制**: 用自然语言控制 Home Assistant 中的所有设备
- 🔄 **实时更新**: 通过 Server-Sent Events (SSE) 获取即时状态变化
- 🤖 **自动化管理**: 创建、更新和管理自动化规则
- 📊 **状态监控**: 追踪和查询设备状态

---

## 技术架构

### MCP 协议集成

```
┌─────────────────┐     MCP Protocol     ┌──────────────────┐
│   AI Assistant  │ ◄─────────────────► │   mcp-hass       │
│  (Claude/GPT)   │                      │   Server         │
└─────────────────┘                      └────────┬─────────┘
                                                  │
                                                  │ WebSocket/REST
                                                  ▼
                                         ┌──────────────────┐
                                         │  Home Assistant  │
                                         │    Instance      │
                                         └──────────────────┘
```

### 依赖要求

| 依赖 | 版本要求 | 用途 |
|------|----------|------|
| Node.js | ≥ 20.10.0 | 运行时环境 |
| NPM | 最新版 | 包管理 |
| Docker | ≥ 1.27.0 | 容器化部署（推荐） |
| Home Assistant | 运行中 | 智能家居平台 |
| HACS | 可选 | 社区包管理 |
| Supervisor | 可选 | Add-on 管理 |

---

## 功能特性

### 1. 设备控制 (Device Control)

支持控制的设备类型：

| 设备类型 | 支持操作 |
|----------|----------|
| 💡 灯光 | 开/关、亮度、色温、RGB 颜色 |
| 🌡️ 恒温器 | 温度、HVAC 模式、风扇模式、湿度 |
| 🚪 窗帘/遮阳 | 位置控制、倾斜角度 |
| 🔌 开关 | 开/关控制 |
| 🚨 传感器 | 状态监控 |
| 🎵 媒体播放器 | 播放控制、音量、源选择 |
| 🌪️ 风扇 | 速度、摆动、方向 |
| 🔒 锁 | 锁定/解锁 |
| 🧹 扫地机器人 | 启动、停止、返回基站 |
| 📹 摄像头 | 动作检测、快照 |

**API 示例**:

```json
{
  "tool": "control",
  "command": "turn_on",
  "entity_id": "light.living_room",
  "brightness": 128,
  "color_temp": 4000,
  "rgb_color": [255, 0, 0]
}
```

### 2. Add-on 管理

```json
// 列出可用 add-ons
{
  "tool": "addon",
  "action": "list"
}

// 安装 add-on
{
  "tool": "addon",
  "action": "install",
  "slug": "core_configurator",
  "version": "5.6.0"
}

// 启动/停止/重启
{
  "tool": "addon",
  "action": "start",
  "slug": "core_configurator"
}
```

### 3. HACS 包管理

支持多种包类型：
- 自定义集成 (Custom integrations)
- 前端主题 (Frontend themes)
- Python 脚本
- AppDaemon 应用
- NetDaemon 应用

```json
{
  "tool": "package",
  "action": "install",
  "category": "integration",
  "repository": "hacs/integration",
  "version": "1.32.0"
}
```

### 4. 自动化管理

```json
{
  "tool": "automation_config",
  "action": "create",
  "config": {
    "alias": "Motion Light",
    "description": "检测到动作时开灯",
    "mode": "single",
    "trigger": [
      {
        "platform": "state",
        "entity_id": "binary_sensor.motion",
        "to": "on"
      }
    ],
    "action": [
      {
        "service": "light.turn_on",
        "target": {
          "entity_id": "light.living_room"
        }
      }
    ]
  }
}
```

### 5. 实时更新 (SSE)

```javascript
// 订阅设备状态变化
const eventSource = new EventSource(
  'http://localhost:3000/subscribe_events?token=YOUR_TOKEN&domain=light'
);

eventSource.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log('Update received:', data);
};
```

---

## 配置方法

### 环境变量

```bash
# Home Assistant 配置
HASS_HOST=http://homeassistant.local:8123
HASS_TOKEN=your_home_assistant_token
HASS_SOCKET_URL=ws://homeassistant.local:8123/api/websocket

# 服务器配置
PORT=3000
NODE_ENV=production
DEBUG=false
```

### Docker 部署（推荐）

```bash
# 克隆仓库
git clone https://github.com/tevonsb/homeassistant-mcp.git
cd homeassistant-mcp

# 配置环境
cp .env.example .env
# 编辑 .env 文件

# 构建并启动
docker compose up -d

# 查看日志
docker compose logs -f
```

### Claude Desktop 集成

```json
{
  "homeassistant": {
    "command": "node",
    "args": ["path/to/dist/folder"],
    "env": {
      "NODE_ENV": "development",
      "HASS_HOST": "http://homeassistant.local:8123",
      "HASS_TOKEN": "your_home_assistant_token",
      "PORT": "3000",
      "HASS_SOCKET_URL": "ws://homeassistant.local:8123/api/websocket",
      "LOG_LEVEL": "debug"
    }
  }
}
```

---

## API 参考

### REST 端点

| 端点 | 方法 | 描述 |
|------|------|------|
| `/api/state` | GET/POST | 管理系统状态 |
| `/api/context` | POST | 更新上下文信息 |
| `/api/action` | POST | 执行单个动作 |
| `/api/actions/batch` | POST | 批量执行动作 |
| `/api/actions` | GET | 获取可用动作列表 |
| `/health` | GET | 健康检查 |

### WebSocket 事件

| 事件 | 描述 |
|------|------|
| `state_change` | 系统状态变化 |
| `context_update` | 上下文更新 |
| `action_executed` | 动作执行完成 |
| `error` | 错误发生 |

### 速率限制

- 普通端点: 100 请求/分钟/IP
- WebSocket: 1000 请求/分钟/IP

---

## 使用场景

### 1. 自然语言智能家居控制

```
用户: "打开客厅的灯，亮度设为 80%"
AI: [通过 mcp-hass 调用 light.turn_on]
    entity_id: light.living_room
    brightness_pct: 80
```

### 2. 自动化规则创建

```
用户: "创建一个自动化：当检测到动作时，打开走廊灯，5分钟后关闭"
AI: [通过 mcp-hass 创建 automation]
```

### 3. 设备状态监控

```
用户: "现在家里温度是多少？"
AI: [查询 climate 实体状态]
    "客厅温度是 23.5°C，湿度 45%"
```

### 4. 批量操作

```json
{
  "actions": [
    {"action": "turn_on_lights", "parameters": {"room": "living_room"}},
    {"action": "set_temperature", "parameters": {"temperature": 22}}
  ]
}
```

---

## 安全考虑

1. **Token 认证**: 使用 Home Assistant 长期访问令牌
2. **速率限制**: 防止 API 滥用
3. **本地部署**: 数据不离开本地网络
4. **权限控制**: 令牌权限可在 Home Assistant 中精细配置

---

## 与 OpenClaw 集成

### 通过 mcporter 调用

```bash
# 配置 mcp-hass 作为 MCP 服务器
mcporter config add homeassistant \
  --command="node /path/to/homeassistant-mcp/dist/index.js" \
  --env HASS_HOST=http://homeassistant.local:8123 \
  --env HASS_TOKEN=your_token
```

### 使用场景

1. **语音控制智能家居**: 通过 OpenClaw 的消息渠道（飞书、Discord 等）控制设备
2. **自动化触发**: 结合其他 Skills 实现复杂自动化
3. **状态通知**: 设备状态变化时主动推送通知

---

## 已知问题

| 问题 | 状态 | 解决方案 |
|------|------|----------|
| ClawHub API 速率限制 | ⚠️ 活跃 | 等待冷却后重试 |
| Node.js 版本要求 | ℹ️ 信息 | 使用 Node.js ≥ 20.10.0 |
| Supervisor 访问 | ℹ️ 信息 | Add-on 管理需要 Supervisor 权限 |

---

## 替代方案

如果无法通过 ClawHub 安装，可以直接使用 GitHub 仓库：

```bash
git clone https://github.com/tevonsb/homeassistant-mcp.git
cd homeassistant-mcp
npm install
npm run build
npm start
```

---

## 项目状态

### ✅ 已完成
- 实体、楼层、区域访问
- 设备控制（灯光、恒温器、窗帘、开关）
- Add-on 管理系统
- HACS 包管理
- 自动化配置
- Docker 容器化
- TypeScript 集成

### 🚧 进行中
- WebSocket 实时更新
- 增强安全功能
- 性能优化
- 测试覆盖率扩展

---

## 参考资源

- [MCP 官方文档](https://modelcontextprotocol.io/introduction)
- [Home Assistant 文档](https://www.home-assistant.io)
- [Home Assistant REST API](https://developers.home-assistant.io/docs/api/rest)
- [HACS 文档](https://hacs.xyz)
- [GitHub: tevonsb/homeassistant-mcp](https://github.com/tevonsb/homeassistant-mcp)

---

## 总结

`mcp-hass` 是一个强大的智能家居集成 Skill，它将 Home Assistant 的能力通过 MCP 协议暴露给 AI 助手。虽然当前因 ClawHub API 限制无法直接安装，但其 GitHub 仓库提供了完整的实现，可以手动部署使用。

**适用人群**:
- Home Assistant 用户
- 智能家居爱好者
- 希望用自然语言控制设备的用户

**核心优势**:
- 全面的设备控制能力
- 实时状态更新
- 自动化管理
- 与 AI 助手无缝集成

---

*分析者: Glint 🔺 | ClawHub Lab*
