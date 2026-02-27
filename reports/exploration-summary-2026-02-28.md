# ClawHub Skills 探索汇总报告

> 📅 报告日期: 2026-02-28
> 🔗 仓库: https://github.com/kongshan001/clawhub-lab

---

## 执行摘要

本次探索完成了以下工作：

1. ✅ 创建了 `clawhub-lab` Git 仓库
2. ✅ 推送到 GitHub (kongshan001/clawhub-lab)
3. ✅ 搜索了多个类别的 Skills
4. ✅ 深度分析了 4 个重要 Skills
5. ✅ 设置了每周自动探索任务

---

## ClawHub 热门 Skills 排行

### 开发工具类 Top 5

| 排名 | Skill | 评分 | 状态 |
|------|-------|------|------|
| 1 | github | 3.777 | ✅ 已分析 |
| 2 | openclaw-github-assistant | 3.606 | 待探索 |
| 3 | github-cli | 3.476 | 待探索 |
| 4 | github-mcp | 3.442 | 待探索 |
| 5 | code | - | ✅ 已分析 |

### 图像处理类 Top 5

| 排名 | Skill | 评分 | 状态 |
|------|-------|------|------|
| 1 | antigravity-image-gen | 3.567 | 待探索 |
| 2 | image-cog | 3.549 | 待探索 |
| 3 | image | 3.547 | 待探索 |
| 4 | gemini-image-simple | 3.498 | 待探索 |
| 5 | qwen-image | 3.492 | 待探索 |

### 数据分析类 Top 5

| 排名 | Skill | 评分 | 状态 |
|------|-------|------|------|
| 1 | data-analyst | 3.513 | 待探索 |
| 2 | data-analysis | 3.498 | 待探索 |
| 3 | data-model-designer | 3.463 | 待探索 |
| 4 | data-reconciliation-exceptions | 3.453 | 待探索 |
| 5 | data-lineage-tracker | 3.431 | 待探索 |

### Web 相关类 Top 5

| 排名 | Skill | 评分 | 状态 |
|------|-------|------|------|
| 1 | web-deploy-github | 3.475 | 待探索 |
| 2 | web | 3.425 | 待探索 |
| 3 | web-pilot | 3.411 | 待探索 |
| 4 | desearch-web-search | 3.400 | 待探索 |
| 5 | ai-web-automation | 3.377 | 待探索 |

---

## 深度分析完成的 Skills

### 1. Code Skill

- **功能**: 编码工作流指导
- **文件**: planning.md, execution.md, verification.md
- **示例**: OpenGL 三角形演示程序
- **状态**: ✅ 完整分析 + 代码示例

### 2. GitHub Skill

- **功能**: GitHub 操作集成 (gh CLI)
- **依赖**: gh CLI
- **主要用途**: PR/Issue/CI 管理
- **状态**: ✅ 完整分析

### 3. Coding Agent Skill

- **功能**: 委托编码任务给 AI 代理
- **支持**: Codex, Claude Code, OpenCode, Pi
- **关键**: 必须使用 pty:true
- **状态**: ✅ 完整分析

### 4. Healthcheck Skill

- **功能**: 主机安全加固
- **用途**: 安全审计、防火墙配置
- **命令**: openclaw security audit
- **状态**: ✅ 完整分析

---

## OpenClaw 内置 Skills 清单

共 **54** 个内置 Skills：

```
1password          apple-notes        apple-reminders
bear-notes         blogwatcher        blucli
bluebubbles        camsnap            canvas
clawhub            code               coding-agent
discord            eightctl           food-order
gemini             gh-issues          gifgrep
github             gog                goplaces
healthcheck        himalaya           imsg
mcporter           model-usage        nano-banana-pro
nano-pdf           notion             obsidian
openai-image-gen   openai-whisper     openai-whisper-api
openhue            oracle             ordercli
peekaboo           sag                session-logs
sherpa-onnx-tts    skill-creator      slack
songsee            sonoscli           spotify-player
summarize          test-runner        things-mac
tmux               trello             video-frames
voice-call         wacli              weather
```

---

## 仓库结构

```
clawhub-lab/
├── README.md
├── AGENT_TASKS.md
├── .gitignore
├── reports/
│   └── clawhub-skills-guide.md
├── skills-analysis/
│   ├── github-skill-analysis.md
│   ├── github-skill-deep.md
│   ├── coding-agent-skill-deep.md
│   └── healthcheck-skill-deep.md
└── code-examples/
    └── hello-triangle/
        ├── hello_triangle.cpp
        ├── CMakeLists.txt
        └── screenshot.png
```

---

## Git 提交历史

```
25c1342 Initial commit: ClawHub Lab setup with code skill analysis
b663aad Add GitHub skill analysis and update task tracking
(待提交) Add deep analysis for github, coding-agent, healthcheck skills
```

---

## 下一步计划

1. 等待 ClawHub API 速率限制解除
2. 安装并分析 data-analyst skill
3. 探索 web-pilot 和 ai-web-automation
4. 添加更多代码示例

---

*报告由 Glint 🔺 生成*
