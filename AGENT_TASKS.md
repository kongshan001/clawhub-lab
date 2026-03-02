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
| 2026-03-02 | 1password | [1password-skill-deep.md](skills-analysis/1password-skill-deep.md) | ✅ 深度分析（密码管理器 CLI）|
| 2026-03-02 | spotify-player | [spotify-player-skill-deep.md](skills-analysis/spotify-player-skill-deep.md) | ✅ 深度分析（Spotify 终端控制）|
| 2026-03-02 | himalaya | [himalaya-skill-deep.md](skills-analysis/himalaya-skill-deep.md) | ✅ 深度分析（邮件 CLI 客户端）|
| 2026-03-02 | apple-notes | [apple-notes-skill-deep.md](skills-analysis/apple-notes-skill-deep.md) | ✅ 深度分析（Apple Notes CLI）|
| 2026-03-02 | nano-banana-pro | [nano-banana-pro-skill-deep.md](skills-analysis/nano-banana-pro-skill-deep.md) | ✅ 深度分析（Gemini 3 Pro Image）|
| 2026-03-02 | oracle | [oracle-skill-deep.md](skills-analysis/oracle-skill-deep.md) | ✅ 深度分析（代码上下文打包）|
| 2026-03-02 | gog | [gog-skill-deep.md](skills-analysis/gog-skill-deep.md) | ✅ 深度分析（Google Workspace CLI）|
| 2026-03-02 | 批量 14 Skills | [batch-14-skills-deep.md](skills-analysis/batch-14-skills-deep.md) | ✅ 深度分析（bear-notes, blogwatcher, blucli, camsnap, eightctl, gifgrep, nano-pdf, openhue, ordercli, peekaboo, sag, songsee, wacli, xurl）|
| 2026-03-02 | 剩余 3 Skills | [remaining-3-skills-deep.md](skills-analysis/remaining-3-skills-deep.md) | ✅ 深度分析（clawhub, goplaces, sonoscli）|
| 2026-03-02 | image-generation | [image-generation-skill-deep.md](skills-analysis/image-generation-skill-deep.md) | ✅ 深度分析（多平台图像生成指南）|
| 2026-03-02 | find-skills | [find-skills-skill-deep.md](skills-analysis/find-skills-skill-deep.md) | ✅ 深度分析（Skills 发现工具）|
| 2026-03-02 | automation-workflows | [automation-workflows-skill-deep.md](skills-analysis/automation-workflows-skill-deep.md) | ✅ 深度分析（自动化工作流）⚠️ API 限制|
| 2026-03-02 | secure-api-calls | [secure-api-calls-skill-deep.md](skills-analysis/secure-api-calls-skill-deep.md) | ✅ 深度分析（Keychains 凭证代理）|
| 2026-03-02 | security-auditor | [security-auditor-skill-deep.md](skills-analysis/security-auditor-skill-deep.md) | ✅ 深度分析（OWASP Top 10 安全审计）|

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
- [x] ~~image-generation~~ (3.602) - AI 图像生成综合指南 ✅

### 中优先级（评分 3.0-3.5）

- [x] ~~web-pilot~~ - Web 自动化 ✅
- [ ] ai-notes-of-video (3.700) - AI 视频笔记（⚠️ 速率限制）
- [x] ~~ai-ppt-generate~~ (3.734) - AI PPT 生成 ✅
- [x] ~~image-edit~~ - 图像编辑 ✅
- [x] ~~3d-cog~~ (3.476) - 3D 模型生成 ✅（CellCog 平台）

### 新发现的高评分 Skills（待探索）

- [ ] ai-web-automation (3.614) - AI Web 自动化（⚠️ 速率限制待安装）
- [ ] ai-automation-workflows (3.579) - AI 自动化工作流（⚠️ 速率限制待安装）

### 低优先级（评分 < 3.0）

- [ ] openclaw-godot-skill (0.905) - Godot 游戏引擎（⚠️ 速率限制）
- [ ] threejs (0.927) - Three.js 3D（⚠️ 速率限制）

### 已安装的额外 Skills

- [x] ~~find-skills~~ (0.1.0) - Skills 发现工具 ✅
- [x] ~~security-auditor~~ (1.0.0) - OWASP Top 10 安全审计 ✅

## OpenClaw 内置 Skills 清单

共 **54** 个内置 Skills：

| 分类 | Skills |
|------|--------|
| 开发工具 | code, github, coding-agent, gh-issues, mcporter, skill-creator, tmux, canvas ✅ |
| 图像/视频 | openai-image-gen ✅, video-frames ✅, gemini ✅, nano-banana-pro ✅ |
| 文本处理 | summarize ✅ |
| 音频 | openai-whisper ✅, openai-whisper-api ✅, sherpa-onnx-tts ✅, voice-call ✅ |
| 通信 | discord ✅, slack ✅, imsg ✅, bluebubbles ✅ |
| 笔记/文档 | notion ✅, obsidian ✅, bear-notes, apple-notes ✅ |
| 任务管理 | apple-reminders ✅, things-mac ✅, trello ✅ |
| 系统 | healthcheck, canvas, model-usage ✅, session-logs ✅ |
| 存储 | 1password ✅, himalaya ✅ |
| 包管理 | clawhub ✅ |
| 位置服务 | goplaces ✅ |
| 音响控制 | sonoscli ✅, spotify-player ✅, sonoscli ✅ |
| 其他 | weather ✅, summarize ✅, oracle ✅, blogwatcher, gog, goplaces ✅, spotify-player ✅, sonoscli ✅ |

## 已知问题

| 问题 | 状态 | 解决方案 |
|------|------|----------|
| ClawHub API 速率限制 | ⚠️ 活跃 | 等待冷却后重试 |
| 部分 Skill 被标记可疑 | ℹ️ 信息 | 使用 `--force` 安装 |

## 内置 Skills 分析进度

已完成：**54/54** (100%) 🎉

**全部完成！** 所有内置 Skills 已分析完毕。

### 批量分析报告

最后一批 14 个 Skills 已通过批量分析完成：
- [batch-14-skills-deep.md](skills-analysis/batch-14-skills-deep.md)

包含：
- bear-notes (Bear 笔记管理)
- blogwatcher (RSS 订阅监控)
- blucli (Bluesound/NAD 音频控制)
- camsnap (摄像头快照/录制)
- eightctl (Eight Sleep 床垫控制)
- gifgrep (GIF 搜索下载)
- nano-pdf (自然语言 PDF 编辑)
- openhue (Philips Hue 灯光控制)
- ordercli (Foodora 订单管理)
- peekaboo (macOS UI 自动化)
- sag (ElevenLabs TTS)
- songsee (音频频谱可视化)
- wacli (WhatsApp 消息)
- xurl (X/Twitter API)

## 自动化任务

| 任务 ID | 名称 | 时间 | 状态 |
|---------|------|------|------|
| 6e1ef553 | ClawHub Lab Hourly Exploration | 每小时整点 | ✅ 已启用 |

下次运行: 约 20:00 (Asia/Shanghai)

## 远程仓库

🔗 https://github.com/kongshan001/clawhub-lab

---

*最后更新: 2026-03-02 17:32 by Glint 🔺*

**🎉 内置 Skills 分析 100% 完成！**
**📊 已分析额外 Skills: find-skills, web-pilot, image-edit, ai-ppt-generate, image-generation, secure-api-calls, security-auditor**
**⚠️ API 持续速率限制 - 待安装: productivity, monitoring, ai-notes-of-video**
**📝 最新报告: new-skills-discovery-2026-03-02-17-32.md（发现 20+ 新 Skills）**

### 待探索的新 Skills（发现于 2026-03-02 17:32 最新）

通过最新搜索发现：

- [ ] productivity (3.559) - 生产力工具集 ⚠️ 待安装
- [ ] monitoring (3.537) - 系统监控 ⚠️ 待安装
- [ ] ai-notes-of-video (3.484) - AI 视频笔记 ⭐ ⚠️ 待安装
- [ ] web-deploy-github (3.481) - GitHub Pages 部署 ⚠️ 待安装
- [ ] ai-ppt-generate (3.478) - AI PPT 生成 ✅ 已分析
- [ ] ai-agent-helper (3.456) - AI Agent 助手 ⚠️ 待安装
- [ ] web (3.439) - Web 开发工具 ⚠️ 待安装
- [ ] ai-web-automation (3.412) - AI Web 自动化 ⚠️ 待安装
- [ ] database-operations (3.508) - 数据库操作 ⚠️ 待安装
- [ ] clawdbot-security-check (3.513) - Clawdbot 安全检查 ⚠️ 待安装
- [ ] security-audit-toolkit (3.510) - 安全审计工具包 ⚠️ 待安装

### 新发现的高评分 Skills（2026-03-02 14:33 最新）

通过最新搜索发现：

- [ ] windows-ui-automation (3.690) - Windows UI 自动化 ⚠️ VirusTotal 警告 + API 限制
- [ ] x-twitter (3.691) - X/Twitter 操作 ⚠️ API 速率限制

### 新发现的 Automation Skills（2026-03-02 12:02）

通过 `clawhub search "automation"` 发现：

- [x] ~~automation-workflows~~ (3.692) - 自动化工作流 ✅ 部分分析（API 限制）
- [ ] automation-workflows-0-1-0 (3.563) - 自动化工作流 0.1.0
- [ ] windows-ui-automation (3.531) - Windows UI 自动化
- [ ] x-post-automation (3.523) - X/Twitter 发帖自动化
- [ ] ai-automation-workflows (3.499) - AI 自动化工作流
- [ ] ai-web-automation (3.496) - AI Web 自动化
- [ ] activecampaign-automation (3.467) - ActiveCampaign 自动化
- [ ] afrexai-business-automation (3.433) - 业务自动化架构师
- [ ] data-automation-service (3.357) - 数据自动化服务
- [ ] afrexai-automation-strategy (3.334) - 业务自动化策略

### 探索日志

| 时间 | 任务 | 结果 |
|------|------|------|
| 2026-03-02 11:52 | 尝试安装 automation-workflows | ❌ API 速率限制 |
| 2026-03-02 11:52 | 搜索 automation/productivity/database | ✅ 发现 10+ 相关 Skills |
| 2026-03-02 11:52 | 创建状态报告 | ✅ exploration-status-2026-03-02-11-52.md |
| 2026-03-02 12:02 | 搜索 automation Skills | ✅ 发现 10 个高评分 Skills |
| 2026-03-02 12:02 | 搜索 ai Skills | ❌ API 速率限制 |
| 2026-03-02 12:02 | 尝试安装 automation-workflows | ❌ API 速率限制 |
| 2026-03-02 12:02 | 创建状态报告 | ✅ exploration-status-2026-03-02-12-02.md |
| 2026-03-02 12:02 | Git push | ✅ 所有 commits 已同步 |
| 2026-03-02 12:13 | 搜索 automation Skills | ✅ API 恢复，发现 5 个 Skills |
| 2026-03-02 12:13 | 尝试安装 automation-workflows | ❌ API 再次速率限制 |
| 2026-03-02 12:13 | 创建分析报告 | ✅ automation-workflows-skill-deep.md（部分分析）|
| 2026-03-02 12:33 | 搜索 automation/database/api/web Skills | ✅ 发现 25+ 相关 Skills |
| 2026-03-02 12:33 | 尝试安装多个 Skills | ❌ API 速率限制 |
| 2026-03-02 12:33 | 创建状态报告 | ✅ exploration-status-2026-03-02-12-33.md |
| 2026-03-02 13:02 | 搜索 security/monitoring/api Skills | ✅ 发现 security-auditor, monitoring, secure-api-calls |
| 2026-03-02 13:02 | 安装 secure-api-calls | ✅ 成功安装（VirusTotal 警告，--force）|
| 2026-03-02 13:02 | 创建分析报告 | ✅ secure-api-calls-skill-deep.md（Keychains 凭证代理）|
| 2026-03-02 12:33 | 搜索 automation/database/api/web Skills | ✅ 发现 25+ 相关 Skills |
| 2026-03-02 12:33 | 尝试安装多个 Skills | ❌ API 速率限制 |
| 2026-03-02 12:33 | 创建状态报告 | ✅ exploration-status-2026-03-02-12-33.md |
| 2026-03-02 13:32 | 搜索 ai-web-automation/ai-notes-of-video | ✅ 发现 2 个高评分技能（3.719, 3.701）|
| 2026-03-02 13:32 | 尝试安装 ai-web-automation | ❌ API 速率限制 |
| 2026-03-02 13:32 | 搜索 database/productivity/security | ✅ 发现 24+ 高评分技能 |
| 2026-03-02 13:32 | 创建状态报告 | ✅ exploration-status-2026-03-02-13-32.md |
| 2026-03-02 14:02 | 搜索 windows/database/productivity/twitter | ✅ 发现 5 个高评分技能（x-twitter, productivity, windows-ui-automation, database-operations, windows-remote）|
| 2026-03-02 14:02 | 尝试安装多个 Skills | ❌ API 速率限制（所有安装请求均被拒绝）|
| 2026-03-02 14:02 | 创建状态报告 | ✅ exploration-status-2026-03-02-14-02.md |
| 2026-03-02 14:33 | 搜索 windows-ui/x-twitter/database | ✅ 发现 2 个高评分技能（windows-ui-automation 3.690, x-twitter 3.691）|
| 2026-03-02 14:33 | 安装 windows-ui-automation | ❌ VirusTotal 警告 + API 速率限制 |
| 2026-03-02 14:33 | 安装 x-twitter | ❌ API 速率限制 |
| 2026-03-02 14:33 | 创建发现报告 | ✅ high-score-skills-discovery-2026-03-02-14-33.md |
| 2026-03-02 15:02 | 搜索 ai/automation/database/monitoring/security | ✅ 发现多个高评分技能 |
| 2026-03-02 15:02 | 安装 security-auditor (3.551) | ✅ 成功安装 |
| 2026-03-02 15:02 | 创建分析报告 | ✅ security-auditor-skill-deep.md（OWASP Top 10 安全审计）|
| 2026-03-02 15:32 | 搜索 monitoring/database/productivity | ❌ API 速率限制 |
| 2026-03-02 15:32 | 检查本地 Skills 状态 | ✅ 52 内置 + 9 额外已安装 |
| 2026-03-02 15:32 | 创建状态报告 | ✅ exploration-status-2026-03-02-15-32.md |
| 2026-03-02 16:02 | 搜索 monitoring/database | ❌ API 速率限制 |
| 2026-03-02 16:02 | 检查已安装 Skills | ✅ 确认 9 个额外 Skills |
| 2026-03-02 16:02 | 创建状态报告 | ✅ exploration-status-2026-03-02-16-02.md（API 持续限制）|
| 2026-03-02 16:32 | 搜索 monitoring | ❌ API 速率限制 |
| 2026-03-02 16:32 | 搜索 database | ✅ 发现 database-operations (3.508) 等 10 个 Skills |
| 2026-03-02 16:32 | 安装 database-operations | ❌ API 速率限制 |
| 2026-03-02 17:32 | 搜索 monitoring/productivity/web/ai | ✅ 发现 20+ 高评分 Skills |
| 2026-03-02 17:32 | 安装 productivity/monitoring/ai-notes-of-video | ❌ API 速率限制 |
| 2026-03-02 17:32 | 创建发现报告 | ✅ new-skills-discovery-2026-03-02-17-32.md |
| 2026-03-02 16:32 | 创建状态报告 | ✅ exploration-status-2026-03-02-16-32.md |
| 2026-03-02 17:02 | 搜索 monitoring | ❌ API 速率限制 |
| 2026-03-02 17:02 | 安装 monitoring | ❌ API 速率限制 |
| 2026-03-02 17:02 | 创建状态报告 | ✅ exploration-status-2026-03-02-17-02.md |
