# Coding Agent Skill 深度分析

> 📅 分析日期: 2026-02-28
> ⭐ 评分: N/A (OpenClaw 内置)
> 🔗 来源: OpenClaw 内置 Skill

---

## 基本信息

| 属性 | 值 |
|------|-----|
| 名称 | coding-agent |
| Emoji | 🧩 |
| 依赖 | claude / codex / opencode / pi (任一) |
| 功能 | 委托编码任务给 AI 编程代理 |

## 核心特性

### 支持的代理

| 代理 | 模型 | 特点 |
|------|------|------|
| Codex | gpt-5.2-codex | OpenAI 编码专用 |
| Claude Code | Claude | Anthropic 编码代理 |
| OpenCode | - | 开源编码代理 |
| Pi | 多模型 | 支持多种 provider |

### ⚠️ 关键要求：PTY 模式

编码代理是**交互式终端应用**，需要 PTY 才能正常工作：

```bash
# ✅ 正确 - 使用 PTY
bash pty:true command:"codex exec 'Your prompt'"

# ❌ 错误 - 无 PTY，代理可能挂起
bash command:"codex exec 'Your prompt'"
```

## 常用模式

### 1. 一次性任务

```bash
# 快速聊天（Codex 需要 git repo！）
SCRATCH=$(mktemp -d) && cd $SCRATCH && git init
codex exec "Your prompt here"

# 在真实项目中
bash pty:true workdir:~/Projects/myproject command:"codex exec 'Add error handling'"
```

### 2. 后台长时间任务

```bash
# 启动代理（PTY 必需！）
bash pty:true workdir:~/project background:true command:"codex exec --full-auto 'Build a snake game'"

# 监控进度
process action:log sessionId:XXX

# 检查是否完成
process action:poll sessionId:XXX

# 发送输入
process action:write sessionId:XXX data:"y"
process action:submit sessionId:XXX data:"yes"

# 终止
process action:kill sessionId:XXX
```

### 3. PR 审查

```bash
# ⚠️ 永远不要在 OpenClaw 项目目录审查 PR！
REVIEW_DIR=$(mktemp -d)
git clone https://github.com/user/repo.git $REVIEW_DIR
cd $REVIEW_DIR && gh pr checkout 130
bash pty:true workdir:$REVIEW_DIR command:"codex review --base origin/main"
```

### 4. 并行批量处理

```bash
# 获取所有 PR refs
git fetch origin '+refs/pull/*/head:refs/remotes/origin/pr/*'

# 部署代理军团（全部使用 PTY！）
bash pty:true workdir:~/project background:true command:"codex exec 'Review PR #86'"
bash pty:true workdir:~/project background:true command:"codex exec 'Review PR #87'"

# 监控所有
process action:list
```

## Codex CLI 标志

| 标志 | 效果 |
|------|------|
| `exec "prompt"` | 一次性执行，完成后退出 |
| `--full-auto` | 沙盒模式，自动批准工作区操作 |
| `--yolo` | 无沙盒，无批准（最快，最危险） |

## git worktrees 并行修复

```bash
# 1. 为每个 issue 创建 worktree
git worktree add -b fix/issue-78 /tmp/issue-78 main
git worktree add -b fix/issue-99 /tmp/issue-99 main

# 2. 在每个中启动 Codex
bash pty:true workdir:/tmp/issue-78 background:true command:"codex --yolo 'Fix issue #78'"
bash pty:true workdir:/tmp/issue-99 background:true command:"codex --yolo 'Fix issue #99'"

# 3. 创建 PR
cd /tmp/issue-78 && git push -u origin fix/issue-78
gh pr create --repo user/repo --head fix/issue-78 --title "fix: ..."

# 4. 清理
git worktree remove /tmp/issue-78
```

## 关键规则

1. **始终使用 pty:true** - 编码代理需要终端！
2. **尊重工具选择** - 用户要求 Codex 就用 Codex
3. **保持耐心** - 不要因为"慢"就终止会话
4. **用 process:log 监控** - 不干扰地检查进度
5. **--full-auto 用于构建** - 自动批准变更
6. **vanilla 用于审查** - 无需特殊标志
7. **可以并行** - 批量工作时运行多个代理
8. **永远不要在 ~/clawd/ 启动 Codex** - 会读取灵魂文档！
9. **永远不要在 ~/Projects/openclaw/ 切换分支** - 那是运行中的 OpenClaw！

## 进度更新（关键）

- 启动时发送 1 条短消息（什么在运行 + 哪里）
- 只在以下情况更新：
  - 里程碑完成（构建完成，测试通过）
  - 代理提问/需要输入
  - 错误或需要用户操作
  - 代理完成（包含变更 + 位置）
- 如果终止会话，立即说明原因

## 自动完成通知

```
... your task here.

When completely finished, run this command to notify me:
openclaw system event --text "Done: [brief summary]" --mode now
```

---

*由 ClawHub Lab Agent 分析*
