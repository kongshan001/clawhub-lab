# PDF / Video / Email Skills 探索报告

**日期**: 2026-03-02 21:32
**类型**: Skills 发现报告（基于搜索结果）
**状态**: ⚠️ API 速率限制 - 无法安装

---

## 📋 概述

本次探索发现了 **30+ 个高评分 Skills**，覆盖 PDF 处理、视频操作、邮件管理三大领域。由于 ClawHub API 持续速率限制，无法安装验证，以下分析基于搜索结果和命名推测。

---

## 🔍 搜索结果摘要

### PDF Skills（10 个）

| Skill | 评分 | 状态 | 用途推测 |
|-------|------|------|----------|
| **nano-pdf** | 3.693 | ✅ 已分析（批量报告） | 自然语言 PDF 编辑 |
| **pdf** | 3.657 | ⚠️ 待安装 | 通用 PDF 操作 |
| **pdf-extract** | 3.556 | ⚠️ 待安装 | PDF 内容提取 |
| **pdf-text-extractor** | 3.540 | ⚠️ 待安装 | PDF 文本提取器 |
| **pymupdf-pdf-parser-clawdbot-skill** | 3.504 | ⚠️ 待安装 | PyMuPDF PDF 解析 |
| **pdf-form-filler** | 3.484 | ⚠️ 待安装 | PDF 表单填充 |
| **pdf-generator** | 3.442 | ⚠️ 待安装 | PDF 生成器 |
| **compress-pdf** | 3.411 | ⚠️ 待安装 | PDF 压缩 |
| **pdf-co** | 3.396 | ⚠️ 待安装 | PDF.co API 集成 |
| **make-pdf-safe** | 3.371 | ⚠️ 待安装 | PDF 安全化处理 |

### Video Skills（10 个）

| Skill | 评分 | 状态 | 用途推测 |
|-------|------|------|----------|
| **video-frames** | 3.619 | ✅ 已分析 | 视频帧提取 |
| **demo-video** | 3.497 | ⚠️ 待安装 | Demo 视频创建 |
| **video-subtitles** | 3.473 | ⚠️ 待安装 | 视频字幕处理 |
| **video-cog** | 3.457 | ⚠️ 待安装 | Video Cog 平台集成 |
| **video-generation** | 3.428 | ⚠️ 待安装 | 视频生成 |
| **flyworks-avatar-video** | 3.405 | ⚠️ 待安装 | 虚拟头像视频 |
| **openclaw-aisa-image-video-models** | 3.377 | ⚠️ 待安装 | Gemini 3 Pro + Qwen Wan 2.6 |
| **douyin-video-fetch** | 3.358 | ⚠️ 待安装 | 抖音视频获取 |
| **video-watcher** | 3.348 | ⚠️ 待安装 | 视频监控 |
| **openclaw-aisa-llm-image-video** | 3.347 | ⚠️ 待安装 | LLM 图像视频生成 |

### Email Skills（10 个）

| Skill | 评分 | 状态 | 用途推测 |
|-------|------|------|----------|
| **imap-smtp-email** | 3.610 | ⚠️ 待安装 | IMAP/SMTP 邮件协议 |
| **email-daily-summary** | 3.557 | ⚠️ 待安装 | 邮件每日摘要 |
| **react-email-skills** | 3.472 | ⚠️ 待安装 | React Email 技能 |
| **email-best-practices** | 3.458 | ⚠️ 待安装 | 邮件最佳实践 |
| **sendclaw-email** | 3.451 | ⚠️ 待安装 | 免费邮箱创建 |
| **email-marketing** | 3.445 | ⚠️ 待安装 | 邮件营销 |
| **email-send** | 3.433 | ⚠️ 待安装 | 邮件发送 |
| **openclaw-email-bypass** | 3.388 | ⚠️ 待安装 | 邮件绕过 |
| **porteden-email** | 3.367 | ⚠️ 待安装 | PortEden 安全邮件 |
| **email-design** | 3.325 | ⚠️ 待安装 | 邮件设计 |

---

## 🎯 高优先级推荐

### 1. PDF Skills

**推荐理由**: PDF 是办公自动化的核心需求

**Top 3**:
1. **pdf** (3.657) - 通用 PDF 操作
2. **pdf-extract** (3.556) - 内容提取
3. **pdf-text-extractor** (3.540) - 文本提取

**应用场景**:
- 📄 文档处理自动化
- 📊 数据提取和分析
- 📝 表单自动填充
- 🗜️ 文档压缩优化

### 2. Video Skills

**推荐理由**: 视频处理需求日益增长

**Top 3**:
1. **video-frames** (3.619) - ✅ 已分析
2. **demo-video** (3.497) - Demo 创建
3. **video-subtitles** (3.473) - 字幕处理

**应用场景**:
- 🎬 视频内容分析
- 📹 视频生成和编辑
- 🌐 多语言字幕
- 🎭 虚拟人视频

### 3. Email Skills

**推荐理由**: 邮件是办公通信的核心

**Top 3**:
1. **imap-smtp-email** (3.610) - 邮件协议
2. **email-daily-summary** (3.557) - 每日摘要
3. **react-email-skills** (3.472) - React Email

**应用场景**:
- 📧 邮件自动化处理
- 📊 邮件摘要生成
- 📈 营销邮件发送
- 🛡️ 安全邮件访问

---

## 🔬 技术架构推测

### PDF Skills 架构

```
PDF Skills 生态
├── 核心层
│   ├── pdf (通用操作)
│   ├── pdf-extract (内容提取)
│   └── pdf-generator (生成器)
│
├── 解析层
│   ├── pymupdf-pdf-parser (PyMuPDF)
│   └── pdf-text-extractor (文本提取)
│
└── 应用层
    ├── pdf-form-filler (表单填充)
    ├── compress-pdf (压缩)
    └── make-pdf-safe (安全化)
```

### Video Skills 架构

```
Video Skills 生态
├── 基础层
│   ├── video-frames (帧提取)
│   ├── video-subtitles (字幕)
│   └── video-watcher (监控)
│
├── 生成层
│   ├── video-generation (视频生成)
│   ├── demo-video (Demo 创建)
│   └── flyworks-avatar-video (虚拟人)
│
└── 平台集成
    ├── video-cog (Video Cog)
    ├── douyin-video-fetch (抖音)
    └── openclaw-aisa (Gemini + Qwen)
```

### Email Skills 架构

```
Email Skills 生态
├── 协议层
│   ├── imap-smtp-email (IMAP/SMTP)
│   └── porteden-email (安全邮件)
│
├── 处理层
│   ├── email-daily-summary (摘要)
│   ├── email-send (发送)
│   └── sendclaw-email (免费邮箱)
│
└── 应用层
    ├── email-marketing (营销)
    ├── email-design (设计)
    └── react-email-skills (React)
```

---

## 📊 技术栈分析

### PDF 技术栈

- **解析引擎**: PyMuPDF, pdf-parse
- **生成工具**: PDFKit, jsPDF
- **压缩算法**: FlateDecode, DCTDecode
- **安全机制**: 加密, 数字签名

### Video 技术栈

- **处理框架**: FFmpeg, MoviePy
- **AI 生成**: Gemini 3 Pro, Qwen Wan 2.6
- **字幕格式**: SRT, VTT, ASS
- **平台 API**: 抖音, Video Cog

### Email 技术栈

- **协议**: IMAP, SMTP, POP3
- **服务**: Gmail, Outlook, QQ Mail
- **模板**: React Email, MJML
- **安全**: OAuth2, TLS

---

## 🚀 使用场景示例

### 场景 1: PDF 文档自动化

```bash
# 安装
clawhub install pdf pdf-extract pdf-form-filler

# 使用示例（推测）
# 1. 提取 PDF 文本
# 2. 填充表单
# 3. 生成报告
```

### 场景 2: 视频内容分析

```bash
# 安装
clawhub install video-frames video-subtitles

# 使用示例（推测）
# 1. 提取关键帧
# 2. 生成字幕
# 3. 分析内容
```

### 场景 3: 邮件自动化

```bash
# 安装
clawhub install imap-smtp-email email-daily-summary

# 使用示例（推测）
# 1. 连接邮箱
# 2. 读取邮件
# 3. 生成摘要
```

---

## ⚠️ 限制和注意事项

### API 速率限制

- **持续时间**: 12+ 小时
- **影响范围**: 所有安装请求
- **解决方案**: 等待 4+ 小时冷却

### VirusTotal 警告

部分 Skills 被 VirusTotal Code Insight 标记为可疑：
- **pdf** (3.657) - ⚠️ 可疑
- **email-daily-summary** (3.557) - ⚠️ 可疑

**解决方案**: 使用 `--force` 参数安装，但需审查代码

---

## 📈 评分分布

### PDF Skills 评分

- **3.6+**: 1 个 (nano-pdf)
- **3.5-3.6**: 3 个 (pdf, pdf-extract, pdf-text-extractor)
- **3.4-3.5**: 2 个 (pymupdf, pdf-form-filler)
- **3.3-3.4**: 4 个 (pdf-generator, compress-pdf, pdf-co, make-pdf-safe)

### Video Skills 评分

- **3.6+**: 1 个 (video-frames)
- **3.4-3.6**: 5 个 (demo-video, video-subtitles, video-cog, video-generation, flyworks)
- **3.3-3.4**: 4 个 (openclaw-aisa 系列, douyin, video-watcher)

### Email Skills 评分

- **3.6+**: 1 个 (imap-smtp-email)
- **3.5-3.6**: 1 个 (email-daily-summary)
- **3.4-3.5**: 5 个 (react-email, email-best-practices, sendclaw, email-marketing, email-send)
- **3.3-3.4**: 3 个 (openclaw-email-bypass, porteden, email-design)

---

## 🎯 下一步行动

### 短期（4-6 小时后）

1. **重试安装** Top 5 Skills:
   - pdf (3.657)
   - imap-smtp-email (3.610)
   - email-daily-summary (3.557)
   - pdf-extract (3.556)
   - demo-video (3.497)

2. **深度分析** 已安装 Skills

3. **编写代码示例**

### 中期（1-2 天）

1. **批量分析** 所有 PDF/Video/Email Skills
2. **整合报告** 到主文档
3. **更新 AGENT_TASKS.md**

### 长期（1 周）

1. **建立 Skills 生态系统地图**
2. **创建 Skills 组合使用指南**
3. **发布 Skills 最佳实践**

---

## 📝 总结

本次探索发现了 **30 个高评分 Skills**，覆盖三大核心领域：

1. **PDF 处理** (10 个) - 文档自动化
2. **视频操作** (10 个) - 内容生成
3. **邮件管理** (10 个) - 通信自动化

**关键发现**:
- ✅ 搜索功能正常
- ❌ 安装功能受限（API 速率限制）
- ⚠️ 部分 Skills 被 VirusTotal 标记

**下一步**:
- 等待 API 限制解除
- 优先安装高评分 Skills
- 创建深度分析报告

---

**探索者**: Glint 🔺
**时间**: 2026-03-02 21:32
**状态**: ⚠️ API 速率限制 - 等待冷却
