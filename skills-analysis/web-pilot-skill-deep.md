# Web Pilot Skill 深度分析报告

**分析日期**: 2026-03-01  
**Skill 版本**: Latest (评分 3.613)  
**分析者**: Glint 🔺

---

## 概述

Web Pilot 是一个**零 API Key** 的网页搜索、阅读和浏览器自动化技能。基于 Playwright + Chromium，提供四大核心功能：

1. **多引擎网页搜索** - DuckDuckGo / Brave / Google
2. **页面内容提取** - 支持 JS 渲染、Cookie 弹窗自动关闭
3. **持久化浏览器会话** - 20+ 种交互操作
4. **文件下载** - 自动文件名检测、PDF 文本提取

---

## 核心架构

### 1. 搜索模块 (`google_search.py`)

```python
# 三种搜索引擎支持
ENGINES = {
    "duckduckgo": search_duckduckgo,  # 最可靠，无 CAPTCHA
    "brave": search_brave,             # 更多结果
    "google": search_google,           # 容易被 CAPTCHA 拦截
}
```

**特点**：
- 直接抓取 HTML 端点，无需 API Key
- 自动分页处理
- 结果去重
- 统一 JSON 输出格式：`[{title, url, snippet}, ...]`

### 2. 页面阅读 (`read_page.py`)

- 基于 Playwright + Chromium
- 使用 DOM TreeWalker 提取干净文本
- 自动关闭 Cookie 同意弹窗（20+ 选择器 + 文本模式匹配）
- 支持多格式输出：JSON / Markdown / Text

### 3. 浏览器会话 (`browser_session.py`)

**架构设计**：
```
┌─────────────────┐     Unix Socket      ┌─────────────────┐
│   CLI Client    │ ◄─────────────────► │  Browser Server │
│ (browser_session│    /tmp/web-pilot-   │   (Forked Child │
│     .py)        │    browser.sock)     │   + Chromium)   │
└─────────────────┘                      └─────────────────┘
```

**通信协议**：4 字节长度前缀帧格式

**支持的 20+ 操作**：

| 类别 | 操作 |
|------|------|
| 导航 | open, navigate, back, forward, reload |
| 内容 | extract, links, search, eval |
| 交互 | click, fill, scroll, wait |
| 截图 | screenshot (viewport/full/element/range), pdf |
| 标签页 | tab new/list/switch/close |
| 工具 | status, dismiss-cookies, close |

### 4. 文件下载 (`download_file.py`)

- 流式下载到磁盘
- 自动检测文件名（URL / Headers）
- PDF 文本提取（需 pdfplumber 或 PyPDF2）

---

## 关键技术亮点

### 1. Cookie 弹窗自动关闭

```javascript
// 20+ CSS 选择器 + 文本模式匹配
const selectors = [
    'button[id*="accept" i]', '.cc-btn.cc-dismiss',
    '#onetrust-accept-btn-handler', ...
];
const patterns = [
    /^accept all$/i, /got it/i, /alle akzeptieren/i, ...
];
```

支持多语言（英语、德语、法语），同时检查主框架和 iframe。

### 2. 智能内容提取

```javascript
// 优先级：article > main > [role="main"] > .content > body
const mainEl = document.querySelector('article')
    || document.querySelector('main')
    || document.querySelector('[role="main"]')
    || ...
```

使用 TreeWalker 过滤无关元素（SCRIPT, STYLE, NAV, FOOTER 等）。

### 3. 部分截图（Range Mode）

```bash
# 截取两个元素之间的区域
python3 browser_session.py screenshot /tmp/range.png \
    --from "#Overview" --to "#end"
```

使用全页截图 + PIL crop 实现像素级精确裁剪。

---

## 依赖项

| 依赖 | 用途 | 必需 |
|------|------|------|
| requests | HTTP 请求 | ✅ |
| beautifulsoup4 | HTML 解析 | ✅ |
| playwright | 浏览器自动化 | ✅ |
| Chromium | 浏览器引擎 | ✅ |
| Pillow | 截图裁剪 | ⚠️ (range 截图需要) |
| pdfplumber / PyPDF2 | PDF 文本提取 | ❌ (可选) |

安装命令：
```bash
pip install requests beautifulsoup4 playwright Pillow
playwright install chromium
```

---

## 使用场景

### 场景 1：研究搜索

```bash
# 搜索并获取结果
python3 scripts/google_search.py "OpenClaw AI agent" --pages 3 --engine brave

# 读取第一个结果
python3 scripts/read_page.py "https://..." --format markdown
```

### 场景 2：交互式浏览

```bash
# 打开并登录
python3 scripts/browser_session.py open "https://example.com/login"
python3 scripts/browser_session.py fill "input[name=email]" "user@example.com"
python3 scripts/browser_session.py fill "input[name=password]" "secret" --submit

# 等待加载并截图
python3 scripts/browser_session.py wait ".dashboard"
python3 scripts/browser_session.py screenshot /tmp/dashboard.png

# 关闭
python3 scripts/browser_session.py close
```

### 场景 3：多标签页操作

```bash
python3 scripts/browser_session.py open "https://news.ycombinator.com"
python3 scripts/browser_session.py tab new "https://reddit.com"
python3 scripts/browser_session.py tab list
python3 scripts/browser_session.py tab switch 0
python3 scripts/browser_session.py close
```

### 场景 4：文件下载

```bash
python3 scripts/download_file.py "https://example.com/report.pdf" --output ~/docs
```

---

## 与 OpenClaw 集成

### AI Agent 工作流

```
1. Search → 获取 URL 列表
2. Read / Open → 提取内容
3. Scroll / Click / Navigate → 交互
4. Search → 在页面中查找特定信息
5. Screenshot → 捕获视觉状态
6. Download → 下载关联文件
7. Close → 清理会话
```

### 最佳实践

1. **优先 DuckDuckGo** - 无 CAPTCHA 拦截
2. **及时关闭会话** - 释放资源
3. **使用 headless** - 服务器环境必须
4. **处理错误** - JSON 错误格式统一

---

## 无障碍特性

Web Pilot 对无障碍访问有特别支持：

- **屏幕阅读增强** - 提取干净结构化文本，去除广告和噪音
- **语音驱动浏览** - 配合 AI 助手实现自然语言控制
- **定向内容提取** - 无需视觉扫描即可获取特定内容
- **表单交互** - 通过命令填写和提交表单
- **Cookie 弹窗移除** - 自动关闭对屏幕阅读器不友好的弹窗

---

## 与类似工具对比

| 特性 | Web Pilot | OpenClaw browser tool | Playwright 直接使用 |
|------|-----------|----------------------|-------------------|
| 零 API Key | ✅ | ✅ | ✅ |
| 多引擎搜索 | ✅ 3种 | ❌ | ❌ |
| Cookie 自动关闭 | ✅ | ❌ | ❌ |
| 持久会话 | ✅ | ✅ | ✅ |
| 脚本化调用 | ✅ Python | ❌ 内置 | ✅ 多语言 |
| 无障碍设计 | ✅ 专门优化 | ❌ | ❌ |

---

## 潜在问题

1. **CAPTCHA 拦截** - Google 搜索经常被拦截
2. **资源占用** - Chromium 内存占用较大
3. **速率限制** - 频繁请求可能被搜索引擎限制
4. **动态内容** - 某些 SPA 可能需要额外等待

---

## 总结评分

| 维度 | 评分 | 说明 |
|------|------|------|
| 功能完整性 | ⭐⭐⭐⭐⭐ | 搜索、阅读、交互、下载全覆盖 |
| 易用性 | ⭐⭐⭐⭐ | 命令清晰，JSON 输出友好 |
| 稳定性 | ⭐⭐⭐⭐ | DuckDuckGo 可靠，Google 不稳定 |
| 无障碍 | ⭐⭐⭐⭐⭐ | 专门优化，支持语音驱动 |
| 文档质量 | ⭐⭐⭐⭐⭐ | README 详细，示例丰富 |

**总评**: ⭐⭐⭐⭐☆ (4.6/5)

---

## 推荐用途

- ✅ AI Agent 网页研究和浏览
- ✅ 自动化测试和数据采集
- ✅ 无障碍网页访问辅助
- ✅ 文档下载和内容提取
- ⚠️ 大规模爬虫（需注意速率限制）

---

*分析完成于 2026-03-01 20:05 by Glint 🔺*
