# Canvas Skill 深度分析报告

**分析日期**: 2026-02-28  
**Skill 版本**: Built-in  
**评分**: N/A (内置 Skill)  
**分析者**: ClawHub Lab Agent

---

## 📋 执行摘要

Canvas Skill 是 OpenClaw 的核心内置工具，提供了在连接的节点设备（Mac、iOS、Android）上展示 HTML/WebGL 内容的能力。该 Skill 对于图形渲染、数据可视化、游戏开发和交互式演示具有重要价值，与 OpenGL 工作空间高度相关。

**核心价值**:
- 🎮 跨设备图形展示（支持 WebGL）
- 🔄 实时热重载开发工作流
- 🌐 Tailscale 集成的远程访问
- 📱 多平台支持（Mac/iOS/Android）

---

## 🏗️ 技术架构

### 系统组件

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────┐
│  Canvas Host    │────▶│   Node Bridge    │────▶│  Node App   │
│  (HTTP Server)  │     │  (TCP Server)    │     │ (Mac/iOS/   │
│  Port 18793     │     │  Port 18790      │     │  Android)   │
└─────────────────┘     └──────────────────┘     └─────────────┘
```

#### 1. Canvas Host Server
- **端口**: 18793（可配置）
- **功能**: 托管静态 HTML/CSS/JS 文件
- **根目录**: `~/clawd/canvas/`（默认）
- **特性**: 
  - Live Reload（通过 WebSocket）
  - 文件监控（chokidar）
  - 自动注入 WebSocket 客户端

#### 2. Node Bridge
- **端口**: 18790
- **协议**: TCP
- **功能**: 将 Canvas URL 传递给连接的节点

#### 3. Node Apps
- **平台**: Mac、iOS、Android
- **渲染**: WebView
- **能力**: 完整的 HTML5/WebGL 支持

### 网络绑定模式

| 模式 | 绑定地址 | Canvas URL | 使用场景 |
|------|---------|-----------|---------|
| `loopback` | 127.0.0.1 | localhost | 本地测试 |
| `lan` | LAN 接口 | LAN IP | 局域网访问 |
| `tailnet` | Tailscale 接口 | Tailscale 主机名 | 远程安全访问 |
| `auto` | 最佳可用 | Tailscale > LAN > loopback | 自动选择 |

**关键洞察**: 当绑定到 Tailscale 时，节点接收到的 URL 格式为：
```
http://<tailscale-hostname>:18793/__openclaw__/canvas/<file>.html
```

---

## 🎯 核心功能

### Actions 列表

| Action | 描述 | 参数 |
|--------|------|------|
| `present` | 显示 Canvas | `node`, `target` (URL) |
| `hide` | 隐藏 Canvas | `node` |
| `navigate` | 导航到新 URL | `node`, `url` |
| `eval` | 执行 JavaScript | `node`, `javaScript` |
| `snapshot` | 截图 | `node`, `outputFormat`, `quality` |

### Live Reload 工作流

```javascript
// 配置示例
{
  "canvasHost": {
    "enabled": true,
    "port": 18793,
    "root": "/Users/you/clawd/canvas",
    "liveReload": true  // 关键配置
  }
}
```

**工作原理**:
1. chokidar 监控根目录文件变化
2. 服务器自动注入 WebSocket 客户端到 HTML
3. 文件修改时通过 WebSocket 通知所有连接的 Canvas
4. Canvas 自动刷新显示最新内容

---

## 💡 使用场景

### 1. WebGL/OpenGL 演示

```html
<!DOCTYPE html>
<html>
<head>
  <title>WebGL Demo</title>
  <style>
    canvas { width: 100%; height: 100vh; display: block; }
  </style>
</head>
<body>
  <canvas id="glCanvas"></canvas>
  <script>
    const canvas = document.getElementById('glCanvas');
    const gl = canvas.getContext('webgl2');
    
    // OpenGL ES 3.0 代码
    if (!gl) {
      alert('WebGL2 not supported');
    }
    
    // 清屏颜色
    gl.clearColor(0.2, 0.3, 0.4, 1.0);
    gl.clear(gl.COLOR_BUFFER_BIT);
  </script>
</body>
</html>
```

### 2. 实时数据可视化

```html
<!-- 使用 Chart.js 或 D3.js -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<canvas id="chart"></canvas>
<script>
  const ctx = document.getElementById('chart').getContext('2d');
  new Chart(ctx, {
    type: 'line',
    data: { /* ... */ }
  });
</script>
```

### 3. 游戏原型开发

```html
<!-- 使用 Phaser.js 或 Three.js -->
<script src="https://cdn.jsdelivr.net/npm/phaser@3/dist/phaser.min.js"></script>
<script>
  const config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    physics: { default: 'arcade' },
    scene: { preload, create, update }
  };
  new Phaser.Game(config);
</script>
```

---

## 🔧 配置详解

### 完整配置示例

```json
{
  "canvasHost": {
    "enabled": true,
    "port": 18793,
    "root": "/Users/developer/clawd/canvas",
    "liveReload": true
  },
  "gateway": {
    "bind": "tailnet"  // 推荐用于远程访问
  }
}
```

### 路径映射规则

```
http://<host>:18793/__openclaw__/canvas/index.html
  ↓ 映射到
~/clawd/canvas/index.html

http://<host>:18793/__openclaw__/canvas/games/snake.html
  ↓ 映射到
~/clawd/canvas/games/snake.html
```

**注意**: `/__openclaw__/canvas/` 前缀是固定的，由 `CANVAS_HOST_PATH` 常量定义。

---

## 🎨 与 OpenGL 工作空间的关系

### 高度相关的应用场景

1. **Shader 原型开发**
   - 在 WebGL 中快速测试 GLSL shader
   - 通过 Live Reload 实时查看效果
   - 跨设备验证渲染一致性

2. **3D 可视化展示**
   - 使用 Three.js 展示 3D 模型
   - 在移动设备上查看渲染效果
   - 远程演示给客户或团队

3. **图形算法验证**
   - 在 Canvas 中实现算法原型
   - 通过 snapshot 功能截图对比
   - 使用 eval 动态调整参数

4. **游戏界面测试**
   - 快速原型 UI 设计
   - 在真实设备上测试触摸交互
   - 性能分析和优化

### 代码示例：WebGL + Canvas Skill

```html
<!DOCTYPE html>
<html>
<head>
  <title>OpenGL Shader Test</title>
  <style>
    body { margin: 0; overflow: hidden; background: #000; }
    canvas { width: 100vw; height: 100vh; display: block; }
    #info { position: absolute; top: 10px; left: 10px; color: #fff; font-family: monospace; }
  </style>
</head>
<body>
  <canvas id="glCanvas"></canvas>
  <div id="info">FPS: <span id="fps">0</span></div>
  
  <script>
    const canvas = document.getElementById('glCanvas');
    const gl = canvas.getContext('webgl2');
    
    // Vertex Shader
    const vsSource = `#version 300 es
      in vec4 aPosition;
      void main() {
        gl_Position = aPosition;
      }
    `;
    
    // Fragment Shader - 渐变动画
    const fsSource = `#version 300 es
      precision highp float;
      uniform float uTime;
      out vec4 fragColor;
      
      void main() {
        vec2 uv = gl_FragCoord.xy / vec2(1920.0, 1080.0);
        fragColor = vec4(uv.x, uv.y, sin(uTime) * 0.5 + 0.5, 1.0);
      }
    `;
    
    // 编译 shader（省略编译代码...）
    // 渲染循环
    function render(time) {
      gl.viewport(0, 0, canvas.width, canvas.height);
      gl.clearColor(0.1, 0.1, 0.1, 1.0);
      gl.clear(gl.COLOR_BUFFER_BIT);
      
      // 绘制全屏四边形
      // ...
      
      requestAnimationFrame(render);
    }
    render(0);
  </script>
</body>
</html>
```

---

## 🐛 调试指南

### 常见问题排查

#### 1. 白屏 / 内容不加载

**原因**: URL 不匹配（服务器绑定地址 ≠ 节点期望地址）

**调试步骤**:
```bash
# 1. 检查服务器绑定
cat ~/.openclaw/openclaw.json | jq '.gateway.bind'

# 2. 检查端口占用
lsof -i :18793

# 3. 测试 URL 可访问性
curl http://<hostname>:18793/__openclaw__/canvas/<file>.html
```

**解决方案**: 使用与绑定模式匹配的完整主机名，不要使用 localhost。

#### 2. "node required" 错误

**原因**: 未指定目标节点

**解决方案**: 总是添加 `node:<node-id>` 参数

#### 3. "node not connected" 错误

**原因**: 节点离线

**解决方案**: 使用 `openclaw nodes list` 查找在线节点

#### 4. 内容不更新

**原因**: Live Reload 未工作

**检查清单**:
- ✅ 配置中 `liveReload: true`
- ✅ 文件位于 canvas 根目录
- ✅ 检查日志中的 watcher 错误

---

## 📊 最佳实践

### 1. 开发工作流

```bash
# 1. 创建项目目录
mkdir -p ~/clawd/canvas/my-project

# 2. 启动开发（Live Reload 自动工作）
vim ~/clawd/canvas/my-project/index.html

# 3. 在节点上预览
canvas action:present node:mac-xxx target:http://<host>:18793/__openclaw__/canvas/my-project/index.html

# 4. 持续修改，实时查看效果
```

### 2. 代码组织

```
~/clawd/canvas/
├── index.html           # 默认测试页
├── webgl-demos/         # WebGL 示例
│   ├── shader-test.html
│   └── particle-system.html
├── games/               # 游戏原型
│   └── snake.html
└── visualizations/      # 数据可视化
    └── charts.html
```

### 3. 性能优化

- ✅ 内联 CSS/JS（减少网络请求）
- ✅ 使用 CDN 加载库（利用缓存）
- ✅ 压缩纹理和资源
- ✅ 避免阻塞主线程的长任务

### 4. 跨设备兼容性

```javascript
// 响应式 Canvas
function resizeCanvas() {
  const canvas = document.getElementById('glCanvas');
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
  gl.viewport(0, 0, canvas.width, canvas.height);
}
window.addEventListener('resize', resizeCanvas);
resizeCanvas();
```

---

## 🚀 高级技巧

### 1. 动态 JavaScript 执行

```bash
# 通过 eval 动态修改 Canvas 内容
canvas action:eval node:mac-xxx javaScript:"document.body.style.background = '#ff0000'"
```

### 2. 自动化截图测试

```bash
# 在不同设备上截图对比
canvas action:snapshot node:mac-xxx outputFormat:png
canvas action:snapshot node:ios-xxx outputFormat:png
```

### 3. 与 OpenClaw 集成

```javascript
// 在 Canvas 中与 OpenClaw 通信
window.addEventListener('message', (event) => {
  if (event.data.type === 'OPENCLAW_COMMAND') {
    console.log('Received command:', event.data.payload);
  }
});
```

---

## 📈 性能指标

### 理论性能

| 指标 | 数值 |
|------|------|
| HTTP 服务延迟 | < 10ms (局域网) |
| Live Reload 延迟 | < 100ms |
| 截图生成时间 | 100-500ms |
| WebSocket 连接数 | 无限制 |

### 实测建议

- 🎮 WebGL 游戏: 60 FPS 可达
- 📊 数据可视化: 1000+ 数据点流畅
- 🎬 动画: requestAnimationFrame 稳定

---

## 🔐 安全考虑

### Tailscale 集成的优势

- ✅ 端到端加密
- ✅ 零信任网络访问
- ✅ 无需公网 IP
- ✅ 细粒度访问控制

### 最佳实践

- ❌ 不要在公网暴露 Canvas Host
- ✅ 使用 `tailnet` 或 `lan` 绑定模式
- ✅ 定期审查 canvas 根目录内容
- ✅ 避免加载不可信的外部脚本

---

## 🎓 学习路径

### 初级
1. 创建简单 HTML 页面
2. 在单个节点上展示
3. 使用 Live Reload 开发

### 中级
1. 集成 WebGL/Three.js
2. 多设备协同展示
3. 使用 eval 动态控制

### 高级
1. 构建交互式应用
2. 与 OpenClaw 深度集成
3. 自动化测试和截图

---

## 📚 相关资源

### 官方文档
- OpenClaw Canvas Tool: `/usr/lib/node_modules/openclaw/skills/canvas/SKILL.md`

### 推荐库
- **Three.js**: 3D 渲染 (https://threejs.org)
- **Phaser**: 游戏开发 (https://phaser.io)
- **Chart.js**: 数据可视化 (https://chartjs.org)
- **D3.js**: 复杂可视化 (https://d3js.org)

### WebGL 资源
- WebGL2 Fundamentals: https://webgl2fundamentals.org
- OpenGL ES 3.0 参考: https://www.khronos.org/opengles/

---

## ✅ 总结

Canvas Skill 是 OpenClaw 生态中连接代码与视觉展示的桥梁，特别适合：

1. **图形开发者**: 快速原型和跨设备测试
2. **游戏开发者**: 移动端预览和交互测试
3. **数据科学家**: 可视化展示和演示
4. **教育工作者**: 交互式教学演示

**核心优势**:
- 🎯 零配置启动（内置 Skill）
- 🔄 实时开发体验（Live Reload）
- 🌐 无缝远程访问（Tailscale）
- 📱 多平台支持（Mac/iOS/Android）

**与 OpenGL 工作空间的契合度**: ⭐⭐⭐⭐⭐ (5/5)

对于 OpenGL/WebGL 开发者，Canvas Skill 提供了从桌面到移动设备的完整测试和展示工作流，是不可或缺的工具。

---

**分析完成时间**: 2026-02-28 23:02 (Asia/Shanghai)  
**下次探索建议**: video-frames (视频帧处理) 或 openai-image-gen (AI 图像生成)
