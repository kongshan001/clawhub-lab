# tmux Skill 深度分析报告

**分析日期**: 2026-03-01  
**Skill 类型**: OpenClaw 内置 Skill  
**分类**: 开发工具 / 会话管理  
**评分**: N/A (内置)

---

## 📋 概述

tmux Skill 是 OpenClaw 内置的 tmux 会话远程控制工具，通过发送按键和抓取 pane 输出来管理交互式 CLI 应用。它对于管理 Claude Code、Codex 等长时间运行的交互式会话至关重要。

### 核心能力

| 能力 | 描述 |
|------|------|
| 会话列表 | 列出所有 tmux 会话 |
| 输出捕获 | 从 tmux pane 抓取输出（包括滚动历史） |
| 按键发送 | 向 tmux 会话发送按键和文本 |
| 窗口/Pane 导航 | 切换窗口和 pane |
| 会话管理 | 创建、终止、重命名会话 |

---

## 🔧 技术架构

### 依赖要求

- **操作系统**: macOS, Linux
- **必需命令**: `tmux`

### 目标格式

```
session:window.pane
```

示例：
- `shared` - 会话名
- `shared:0` - 会话的窗口 0
- `shared:0.0` - 窗口 0 的 pane 0

---

## 📖 使用场景

### ✅ 适用场景

1. **监控 Claude/Codex 会话**
   - 检查后台工作状态
   - 捕获输出日志

2. **交互式终端应用**
   - 向 REPL 发送命令
   - 与 TUI 应用交互

3. **长时间运行的进程**
   - 监控构建进度
   - 检查后台任务

4. **并行工作会话**
   - 管理 worker-2 到 worker-8 等多个会话

### ❌ 不适用场景

| 场景 | 替代方案 |
|------|----------|
| 一次性 shell 命令 | 使用 `exec` 工具 |
| 启动新的后台进程 | 使用 `exec` + `background:true` |
| 非交互式脚本 | 使用 `exec` 工具 |
| 非 tmux 进程 | 使用 `exec` 或 `process` 工具 |
| 创建新 tmux 会话 | 使用 `exec` + `tmux new-session` |

---

## 💻 代码示例

### 1. 列出所有会话

```bash
tmux list-sessions
# 或简写
tmux ls
```

### 2. 捕获 Pane 输出

```bash
# 最后 20 行
tmux capture-pane -t shared -p | tail -20

# 完整滚动历史
tmux capture-pane -t shared -p -S -

# 指定窗口和 pane
tmux capture-pane -t shared:0.0 -p
```

### 3. 发送按键

```bash
# 发送文本（不按 Enter）
tmux send-keys -t shared "hello"

# 发送文本 + Enter
tmux send-keys -t shared "y" Enter

# 特殊按键
tmux send-keys -t shared Enter
tmux send-keys -t shared Escape
tmux send-keys -t shared C-c    # Ctrl+C
tmux send-keys -t shared C-d    # Ctrl+D (EOF)
tmux send-keys -t shared C-z    # Ctrl+Z (suspend)
```

### 4. 窗口/Pane 导航

```bash
# 选择窗口
tmux select-window -t shared:0

# 选择 pane
tmux select-pane -t shared:0.1

# 列出窗口
tmux list-windows -t shared
```

### 5. 会话管理

```bash
# 创建新会话
tmux new-session -d -s newsession

# 终止会话
tmux kill-session -t sessionname

# 重命名会话
tmux rename-session -t old new
```

---

## 🤖 Claude Code 会话管理模式

### 检查是否需要输入

```bash
# 查找提示符
tmux capture-pane -t worker-3 -p | tail -10 | grep -E "❯|Yes.*No|proceed|permission"
```

### 批准 Claude Code 提示

```bash
# 发送 'y' 和 Enter
tmux send-keys -t worker-3 'y' Enter

# 或选择编号选项
tmux send-keys -t worker-3 '2' Enter
```

### 检查所有会话状态

```bash
for s in shared worker-2 worker-3 worker-4 worker-5 worker-6 worker-7 worker-8; do
  echo "=== $s ==="
  tmux capture-pane -t $s -p 2>/dev/null | tail -5
done
```

### 向会话发送任务

```bash
tmux send-keys -t worker-4 "Fix the bug in auth.js" Enter
```

---

## ⚠️ 最佳实践

### 安全发送输入

对于交互式 TUI（Claude Code、Codex 等），将文本和 Enter 分开发送：

```bash
tmux send-keys -t shared -l -- "Please apply the patch in src/foo.ts"
sleep 0.1
tmux send-keys -t shared Enter
```

### 关键注意事项

1. **使用 `-p` 打印到 stdout**：`capture-pane -p` 对于脚本至关重要
2. **`-S -` 捕获完整历史**：获取整个滚动缓冲区
3. **会话持久化**：tmux 会话在 SSH 断开后仍然存在

---

## 🔄 与其他工具的集成

### 与 exec 工具配合

```bash
# 创建会话
exec: tmux new-session -d -s mysession

# 发送命令
exec: tmux send-keys -t mysession "npm run dev" Enter

# 捕获输出
exec: tmux capture-pane -t mysession -p | tail -20
```

### 与 coding-agent Skill 配合

tmux 是管理 coding-agent 生成的 Claude Code / Codex 会话的理想工具：

1. coding-agent 启动交互式会话
2. tmux skill 监控和操控这些会话
3. 可以发送确认、取消或新指令

---

## 📊 能力矩阵

| 功能 | 支持度 | 备注 |
|------|--------|------|
| 会话列表 | ✅ 完整 | `tmux ls` |
| 输出捕获 | ✅ 完整 | 包括滚动历史 |
| 按键发送 | ✅ 完整 | 支持所有特殊键 |
| 窗口管理 | ✅ 完整 | 创建/切换/列出 |
| Pane 管理 | ✅ 完整 | 选择和导航 |
| 会话管理 | ✅ 完整 | 创建/终止/重命名 |
| 同步输入 | ✅ 支持 | `setw synchronize-panes` |

---

## 🎯 实际应用案例

### 案例 1: 并行 Codex 会话管理

```bash
# 场景：管理 8 个并行 Codex 工作会话

# 1. 检查所有会话状态
for i in {2..8}; do
  echo "=== worker-$i ==="
  tmux capture-pane -t worker-$i -p 2>/dev/null | tail -3
done

# 2. 向特定会话发送新任务
tmux send-keys -t worker-5 "Refactor the database layer" Enter

# 3. 批准所有等待的提示
for i in {2..8}; do
  tmux send-keys -t worker-$i 'y' Enter 2>/dev/null
done
```

### 案例 2: 监控构建进度

```bash
# 场景：长时间运行的构建过程

# 启动构建
tmux send-keys -t build "make -j$(nproc)" Enter

# 定期检查进度
tmux capture-pane -t build -p | tail -20

# 检测完成
if tmux capture-pane -t build -p | grep -q "Build succeeded"; then
  echo "Build complete!"
fi
```

### 案例 3: 交互式调试

```bash
# 场景：与 Node.js REPL 交互

# 发送代码片段
tmux send-keys -t debug "const result = calculateComplexValue();" Enter
sleep 0.5

# 查看结果
tmux capture-pane -t debug -p | tail -5

# 发送后续命令
tmux send-keys -t debug "console.log(result);" Enter
```

---

## 📈 性能考虑

| 操作 | 性能影响 | 建议 |
|------|----------|------|
| capture-pane | 低 | 可以频繁调用 |
| send-keys | 极低 | 无限制 |
| list-sessions | 极低 | 可以频繁调用 |
| 滚动历史捕获 | 中等 | 大历史时注意内存 |

---

## 🔒 安全注意事项

1. **敏感信息**: tmux 输出可能包含敏感信息，注意日志处理
2. **会话隔离**: 不同任务使用不同会话，避免混淆
3. **权限控制**: tmux 会话继承启动用户的权限

---

## 📝 总结

tmux Skill 是 OpenClaw 生态中不可或缺的会话管理工具：

**优势**:
- 完整的 tmux 控制能力
- 与 Claude Code / Codex 无缝集成
- 支持复杂的并行会话管理
- 轻量级，无额外依赖

**适用场景**:
- 管理交互式 AI 编码会话
- 监控长时间运行的进程
- 并行任务协调
- 远程会话管理

**与 exec 工具的分工**:
- `exec`: 一次性命令、后台进程
- `tmux skill`: 交互式会话、持续监控

---

*分析者: Glint 🔺 | ClawHub Lab*
