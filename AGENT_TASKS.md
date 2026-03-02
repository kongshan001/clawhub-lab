# ClawHub Lab Agent 任务

## 任务描述

定期探索 ClawHub Skills 市场，分析、实践并整理报告。

## 执行频率

- **每小时探索**（自动：整点触发）
- 按需分析热门 Skills
- 持续更新代码示例

## 任务模板

### 1. 发现新 Skills

```bash
clawhub search "<category>"
```

### 2. 分析 Skill

1. 安装 Skill
2. 阅读 SKILL.md 和辅助文档
3. 分析功能和依赖
4. 编写使用示例

### 3. 输出报告

- 保存到 `reports/` 目录
- 代码示例保存到 `code-examples/`
- 深度分析保存到 `skills-analysis/`

## 仓库结构

```
clawhub-lab/
├── reports/           # 分析报告 (Markdown)
├── code-examples/     # 代码示例
├── skills-analysis/   # Skills 深度分析
├── assets/            # 资源文件
└── AGENT_TASKS.md     # 本文件
```

## 已完成的探索

| 日期 | Skill | 报告 | 状态 |
|------|-------|------|------|
| 2026-02-28 | code | [skill-analysis-code-testrunner.md](reports/skill-analysis-code-testrunner.md) | ✅ 完整 + 代码示例 |
| 2026-02-28 | github | [github-skill-deep.md](skills-analysis/github-skill-deep.md) | ✅ 深度分析 |
| 2026-02-28 | coding-agent | [coding-agent-skill-deep.md](skills-analysis/coding-agent-skill-deep.md) | ✅ 深度分析 |
| 2026-02-28 | healthcheck | [healthcheck-skill-deep.md](skills-analysis/healthcheck-skill-deep.md) | ✅ 深度分析 |
| 2026-02-28 | gh-issues | [gh-issues-skill-deep.md](skills-analysis/gh-issues-skill-deep.md) | ✅ 深度分析 |
| 2026-02-28 | mcporter | [mcporter-skill-deep.md](skills-analysis/mcporter-skill-deep.md) | ✅ 深度分析 |
| 2026-02-28 | skill-creator | [skill-creator-skill-deep.md](skills-analysis/skill-creator-skill-deep.md) | ✅ 深度分析 |
| 2026-02-28 | canvas | [canvas-skill-deep.md](skills-analysis/canvas-skill-deep.md) | ✅ 深度分析 |
| 2026-03-01 | mcp-skill | [mcp-skill-deep.md](skills-analysis/mcp-skill-deep.md) | ✅ 深度分析（基于 MCP 协议）|
| 2026-03-01 | mcp-hass | [mcp-hass-skill-deep.md](skills-analysis/mcp-hass-skill-deep.md) | ✅ 深度分析（Home Assistant MCP）|
| 2026-03-01 | openai-image-gen | [openai-image-gen-skill-deep.md](skills-analysis/openai-image-gen-skill-deep.md) | ✅ 深度分析（内置 Skill）|
| 2026-03-01 | video-frames | [video-frames-skill-deep.md](skills-analysis/video-frames-skill-deep.md) | ✅ 深度分析（视频帧提取）|
| 2026-03-01 | gemini | [gemini-skill-deep.md](skills-analysis/gemini-skill-deep.md) | ✅ 深度分析（Google 多模态 AI）|
| 2026-03-01 | summarize | [summarize-skill-deep.md](skills-analysis/summarize-skill-deep.md) | ✅ 深度分析（URL/文件/YouTube 摘要）|
| 2026-03-01 | data-analyst | [data-analyst-skill-deep.md](skills-analysis/data-analyst-skill-deep.md) | ✅ 深度分析（数据分析）⚠️ 未安装（API 限制）|
| 2026-03-01 | openclaw-github-assistant | [openclaw-github-assistant-skill-deep.md](skills-analysis/openclaw-github-assistant-skill-deep.md) | ✅ 深度分析（GitHub 集成）⚠️ VirusTotal 误报|
| 2026-03-01 | web-pilot | [web-pilot-skill-deep.md](skills-analysis/web-pilot-skill-deep.md) | ✅ 深度分析（零 API Key 网页自动化）|
| 2026-03-01 | tmux | [tmux-skill-deep.md](skills-analysis/tmux-skill-deep.md) | ✅ 深度分析（会话远程控制）|
| 2026-03-01 | weather | [weather-skill-deep.md](skills-analysis/weather-skill-deep.md) | ✅ 深度分析（天气查询，零 API Key）|
| 2026-03-01 | openai-whisper + openai-whisper-api | [whisper-skills-deep.md](skills-analysis/whisper-skills-deep.md) | ✅ 深度分析（本地/云端语音识别）|
| 2026-03-02 | sherpa-onnx-tts | [sherpa-onnx-tts-skill-deep.md](skills-analysis/sherpa-onnx-tts-skill-deep.md) | ✅ 深度分析（本地离线 TTS）|
| 2026-03-02 | discord | [discord-skill-deep.md](skills-analysis/discord-skill-deep.md) | ✅ 深度分析（Discord 通信集成）|
| 2026-03-02 | slack | [slack-skill-deep.md](skills-analysis/slack-skill-deep.md) | ✅ 深度分析（Slack 团队协作）|
| 2026-03-02 | notion | [notion-skill-deep.md](skills-analysis/notion-skill-deep.md) | ✅ 深度分析（Notion API 集成）|
| 2026-03-02 | model-usage | [model-usage-skill-deep.md](skills-analysis/model-usage-skill-deep.md) | ✅ 深度分析（模型成本统计）|
| 2026-03-02 | image-edit | [image-edit-skill-deep.md](skills-analysis/image-edit-skill-deep.md) | ✅ 深度分析（AI 图像编辑）|
| 2026-03-02 | apple-reminders | [apple-reminders-skill-deep.md](skills-analysis/apple-reminders-skill-deep.md) | ✅ 深度分析（macOS 任务管理）|
| 2026-03-02 | ai-ppt-generate | [ai-ppt-generate-skill-deep.md](skills-analysis/ai-ppt-generate-skill-deep.md) | ✅ 深度分析（智能 PPT 生成）|
| 2026-03-02 | obsidian | [obsidian-skill-deep.md](skills-analysis/obsidian-skill-deep.md) | ✅ 深度分析（知识库自动化）|
| 2026-03-02 | voice-call | [voice-call-skill-deep.md](skills-analysis/voice-call-skill-deep.md) | ✅ 深度分析（语音通话集成）|
| 2026-03-02 | session-logs | [session-logs-skill-deep.md](skills-analysis/session-logs-skill-deep.md) | ✅ 深度分析（会话日志分析）|
| 2026-03-02 | things-mac | [things-mac-skill-deep.md](skills-analysis/things-mac-skill-deep.md) | ✅ 深度分析（Things 3 任务管理）|
| 2026-03-02 | trello | [trello-skill-deep.md](skills-analysis/trello-skill-deep.md) | ✅ 深度分析（Trello 看板管理）|
| 2026-03-02 | imsg | [imsg-skill-deep.md](skills-analysis/imsg-skill-deep.md) | ✅ 深度分析（iMessage/SMS macOS）|
| 2026-03-02 | antigravity-image-gen | [antigravity-image-gen-skill-deep.md](skills-analysis/antigravity-image-gen-skill-deep.md) | ✅ 深度分析（零成本 AI 图像生成）⚠️ 源码分析|
| 2026-03-02 | 3d-cog | [3d-cog-skill-deep.md](skills-analysis/3d-cog-skill-deep.md) | ✅ 深度分析（CellCog 3D 模型生成）⚠️ API 限制|
| 2026-03-02 | bluebubbles | [bluebubbles-skill-deep.md](skills-analysis/bluebubbles-skill-deep.md) | ✅ 深度分析（iMessage 集成）|

## 待探索的 Skills

### 高优先级（评分 > 3.5）

- [x] ~~code~~ - 代码工作流 ✅
- [x] ~~github~~ - GitHub 操作 ✅
- [x] ~~coding-agent~~ - 编码代理 ✅
- [x] ~~healthcheck~~ - 安全审计 ✅
- [x] ~~gh-issues~~ - GitHub Issues 自动修复 ✅
- [x] ~~mcporter~~ - MCP 工具调用 ✅
- [x] ~~skill-creator~~ - Skill 创建 ✅
- [x] ~~mcp-skill~~ - MCP 协议集成 ✅
- [x] ~~mcp-hass~~ - Home Assistant MCP ✅
- [x] ~~openai-image-gen~~ - AI 图像生成（内置）✅
- [x] ~~data-analyst~~ - 数据分析 ✅
- [x] ~~openclaw-github-assistant~~ - GitHub 助手 ✅
- [x] ~~antigravity-image-gen~~ (3.567) - AI 图像生成 ✅（基于源码分析）

### 中优先级（评分 3.0-3.5）

- [x] ~~web-pilot~~ - Web 自动化 ✅
- [ ] ai-notes-of-video (3.700) - AI 视频笔记（⚠️ 速率限制）
- [x] ~~ai-ppt-generate~~ (3.734) - AI PPT 生成 ✅
- [x] ~~image-edit~~ - 图像编辑 ✅
- [x] ~~3d-cog~~ (3.476) - 3D 模型生成 ✅（CellCog 平台）

### 低优先级（评分 < 3.0）

- [ ] openclaw-godot-skill (0.905) - Godot 游戏引擎
- [ ] threejs (0.927) - Three.js 3D

## OpenClaw 内置 Skills 清单

共 **54** 个内置 Skills：

| 分类 | Skills |
|------|--------|
| 开发工具 | code, github, coding-agent, gh-issues, mcporter, skill-creator, tmux, canvas ✅ |
| 图像/视频 | openai-image-gen ✅, video-frames ✅, gemini ✅ |
| 文本处理 | summarize ✅ |
| 音频 | openai-whisper ✅, openai-whisper-api ✅, sherpa-onnx-tts ✅, voice-call ✅ |
| 通信 | discord ✅, slack ✅, imsg ✅, bluebubbles ✅ |
| 笔记/文档 | notion ✅, obsidian ✅, bear-notes, apple-notes |
| 任务管理 | apple-reminders ✅, things-mac ✅, trello ✅ |
| 系统 | healthcheck, canvas, model-usage ✅, session-logs ✅ |
| 存储 | 1password, himalaya |
| 其他 | weather ✅, summarize ✅, blogwatcher, gog, goplaces, spotify-player, sonoscli |

## 已知问题

| 问题 | 状态 | 解决方案 |
|------|------|----------|
| ClawHub API 速率限制 | ⚠️ 活跃 | 等待冷却后重试 |
| 部分 Skill 被标记可疑 | ℹ️ 信息 | 使用 `--force` 安装 |

## 自动化任务

| 任务 ID | 名称 | 时间 | 状态 |
|---------|------|------|------|
| 6e1ef553 | ClawHub Lab Hourly Exploration | 每小时整点 | ✅ 已启用 |

下次运行: 约 20:00 (Asia/Shanghai)

## 远程仓库

🔗 https://github.com/kongshan001/clawhub-lab

---

*最后更新: 2026-03-02 09:42 by Glint 🔺*
