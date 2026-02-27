# ClawHub Lab Agent 任务

## 任务描述

定期探索 ClawHub Skills 市场，分析、实践并整理报告。

## 执行频率

- 每周探索新 Skills（自动：每周一 9:00）
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

| 日期 | Skill | 报告 | 代码示例 | 状态 |
|------|-------|------|----------|------|
| 2026-02-28 | code | [clawhub-skills-guide.md](reports/clawhub-skills-guide.md) | [hello-triangle](code-examples/hello-triangle) | ✅ 完成 |
| 2026-02-28 | github | [github-skill-analysis.md](skills-analysis/github-skill-analysis.md) | - | ⏳ 概要 |
| 2026-02-28 | github | [github-skill-deep.md](skills-analysis/github-skill-deep.md) | - | ✅ 深度分析 |
| 2026-02-28 | coding-agent | [coding-agent-skill-deep.md](skills-analysis/coding-agent-skill-deep.md) | - | ✅ 深度分析 |
| 2026-02-28 | healthcheck | [healthcheck-skill-deep.md](skills-analysis/healthcheck-skill-deep.md) | - | ✅ 深度分析 |

## 待探索的 Skills

### 高优先级（评分 > 3.5）

- [x] ~~code~~ - 代码工作流 ✅
- [x] ~~github~~ - GitHub 操作 ✅
- [x] ~~coding-agent~~ - 编码代理 ✅
- [x] ~~healthcheck~~ - 安全审计 ✅
- [ ] openclaw-github-assistant (3.606) - GitHub 助手
- [ ] antigravity-image-gen (3.567) - AI 图像生成
- [ ] data-analyst (3.512) - 数据分析

### 中优先级（评分 3.0-3.5）

- [ ] web-pilot (3.411) - Web 自动化
- [ ] github-mcp (3.442) - GitHub MCP Server
- [ ] image-edit (3.454) - 图像编辑
- [ ] 3d-cog (3.331) - 3D 处理
- [ ] tex-render (3.343) - TeX 渲染

### 低优先级（评分 < 3.0）

- [ ] openclaw-godot-skill (0.905) - Godot 游戏引擎
- [ ] threejs (0.927) - Three.js 3D

## OpenClaw 内置 Skills 列表

系统已安装 54 个 Skills：

| 分类 | Skills |
|------|--------|
| 开发工具 | code, github, coding-agent, gh-issues, mcporter, skill-creator, tmux |
| 图像/视频 | openai-image-gen, video-frames, gemini |
| 音频 | openai-whisper, openai-whisper-api, sherpa-onnx-tts, voice-call |
| 通信 | discord, slack, imsg, bluebubbles |
| 笔记/文档 | notion, obsidian, bear-notes, apple-notes |
| 任务管理 | apple-reminders, things-mac, trello |
| 系统 | healthcheck, canvas, model-usage, session-logs |
| 存储 | 1password, himalaya |
| 其他 | weather, summarize, blogwatcher, gog, goplaces, spotify-player, sonoscli |

## 已知问题

| 问题 | 状态 | 解决方案 |
|------|------|----------|
| ClawHub API 速率限制 | ⚠️ 活跃 | 等待冷却后重试 |
| 部分 Skill 被标记可疑 | ℹ️ 信息 | 使用 `--force` 安装 |
| Subagent API 认证失败 | ⚠️ 已知 | 主会话直接执行 |

## 自动化任务

| 任务 ID | 名称 | 时间 | 状态 |
|---------|------|------|------|
| d5b907b9 | 每周探索 | 每周一 9:00 | ✅ 已启用 |

## 远程仓库

🔗 https://github.com/kongshan001/clawhub-lab

---

*最后更新: 2026-02-28 by Glint 🔺*
