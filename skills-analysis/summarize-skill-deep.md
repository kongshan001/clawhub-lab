# Summarize Skill 深度分析报告

**分析日期**: 2026-03-01
**Skill 版本**: 内置
**评分**: N/A (内置 Skill)
**来源**: https://summarize.sh

---

## 概述

**Summarize** 是一个快速 CLI 工具，用于摘要 URLs、本地文件和 YouTube 视频。它是处理 "总结这个链接/视频" 类请求的最佳回退方案。

---

## 核心功能

### 1. 多源摘要

```bash
# URL 摘要
summarize "https://example.com" --model google/gemini-3-flash-preview

# 本地文件摘要
summarize "/path/to/file.pdf" --model google/gemini-3-flash-preview

# YouTube 视频摘要
summarize "https://youtu.be/dQw4w9WgXcQ" --youtube auto
```

### 2. YouTube 转录提取

**最佳实践**: 无需 `yt-dlp` 即可提取 YouTube 视频转录

```bash
# 仅提取转录（不摘要）
summarize "https://youtu.be/dQw4w9WgXcQ" --youtube auto --extract-only
```

**工作流建议**:
1. 如果转录太长，先返回紧凑摘要
2. 然后询问用户需要展开哪个部分/时间范围

### 3. 输出长度控制

| 参数 | 说明 |
|------|------|
| `--length short` | 短摘要 |
| `--length medium` | 中等摘要 |
| `--length long` | 长摘要 |
| `--length xl` | 超长摘要 |
| `--length xxl` | 极长摘要 |
| `--length <chars>` | 指定字符数 |

### 4. 高级功能

```bash
# JSON 输出（机器可读）
summarize "https://example.com" --json

# 使用 Firecrawl 后备（针对被屏蔽的网站）
summarize "https://blocked-site.com" --firecrawl always

# 限制输出 tokens
summarize "https://example.com" --max-output-tokens 500
```

---

## 支持的 AI 模型

| Provider | 环境变量 | 示例模型 |
|----------|----------|----------|
| Google | `GEMINI_API_KEY` | `google/gemini-3-flash-preview` (默认) |
| OpenAI | `OPENAI_API_KEY` | `openai/gpt-4o` |
| Anthropic | `ANTHROPIC_API_KEY` | `anthropic/claude-3-opus` |
| xAI | `XAI_API_KEY` | `xai/grok-beta` |

**别名支持**: `GOOGLE_GENERATIVE_AI_API_KEY`, `GOOGLE_API_KEY` 也被识别

---

## 配置

### 配置文件

```json
// ~/.summarize/config.json
{
  "model": "openai/gpt-5.2"
}
```

### 可选服务

| 服务 | 环境变量 | 用途 |
|------|----------|------|
| Firecrawl | `FIRECRAWL_API_KEY` | 被屏蔽网站的后备提取 |
| Apify | `APIFY_API_TOKEN` | YouTube 转录后备方案 |

---

## 触发短语

当用户说以下内容时，**立即**使用此 Skill：

- "use summarize.sh"
- "what's this link/video about?"
- "summarize this URL/article"
- "transcribe this YouTube/video"

---

## 安装要求

```bash
# macOS (Homebrew)
brew install steipete/tap/summarize

# 验证安装
which summarize
```

---

## 实用场景

### 1. 快速文章摘要

```bash
summarize "https://arxiv.org/abs/2301.00001" --length short
```

### 2. PDF 文档总结

```bash
summarize "./report.pdf" --model openai/gpt-4o
```

### 3. YouTube 视频笔记

```bash
# 先获取摘要
summarize "https://youtu.be/VIDEO_ID" --youtube auto --length medium

# 需要完整转录时
summarize "https://youtu.be/VIDEO_ID" --youtube auto --extract-only
```

### 4. 批量处理（脚本）

```bash
#!/bin/bash
# 批量摘要脚本
for url in "$@"; do
  echo "=== $url ==="
  summarize "$url" --length short --json | jq '.summary'
done
```

---

## 与其他 Skill 的对比

| Skill | 用途 | 优势 |
|-------|------|------|
| **summarize** | URL/文件/YouTube 摘要 | 快速、多源、支持转录 |
| gemini | 多模态分析 | 支持图像、视频帧 |
| openai-whisper | 音频转录 | 高质量语音识别 |

**推荐组合**:
- `summarize` + `gemini` = 完整的多媒体分析
- `summarize` + `openai-whisper` = 音频文件深度转录

---

## 限制与注意事项

1. **YouTube 转录**: 依赖视频是否有字幕/转录文本
2. **API 密钥**: 需要配置至少一个 AI 提供商的密钥
3. **文件大小**: 大文件可能需要更多 tokens
4. **网络依赖**: URL 摘要需要网络连接

---

## 最佳实践

### 1. 模型选择

- **快速摘要**: `google/gemini-3-flash-preview` (默认，速度快)
- **高质量摘要**: `openai/gpt-4o` 或 `anthropic/claude-3-opus`
- **长文档**: 使用 `--max-output-tokens` 控制成本

### 2. 处理大内容

```bash
# 先获取短摘要了解主题
summarize "$URL" --length short

# 需要详情时展开
summarize "$URL" --length long
```

### 3. 错误处理

```bash
# 如果默认提取失败，尝试 Firecrawl
summarize "$URL" --firecrawl auto
```

---

## 总结评分

| 维度 | 评分 | 说明 |
|------|------|------|
| **实用性** | ⭐⭐⭐⭐⭐ | 处理 URL/视频摘要的必备工具 |
| **易用性** | ⭐⭐⭐⭐⭐ | 简单的 CLI，触发短语清晰 |
| **灵活性** | ⭐⭐⭐⭐ | 支持多种模型和输出格式 |
| **文档质量** | ⭐⭐⭐⭐ | SKILL.md 简洁但完整 |
| **依赖管理** | ⭐⭐⭐ | 需要外部 API 密钥 |

**总评**: 🏆 **核心工具** - 每个 OpenClaw 用户都应掌握的 Skill

---

*分析者: Glint 🔺*
*ClawHub Lab - OpenClaw Skills 深度分析系列*
