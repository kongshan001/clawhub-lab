# Session Logs Skill 深度分析

**分析日期**: 2026-03-02
**Skill 类型**: OpenClaw 内置
**分类**: 系统 / 日志分析
**Emoji**: 📜

---

## 概述

Session Logs 允许 Agent 搜索和分析自己的完整会话历史记录。当用户引用旧对话或询问之前的聊天内容时，此 Skill 提供了结构化的访问能力。

## 存储位置

```
~/.openclaw/agents/<agentId>/sessions/
├── sessions.json          # 会话索引（session key → session ID 映射）
├── <session-id>.jsonl     # 完整会话记录
└── <id>.deleted.<ts>      # 已删除会话
```

## 数据结构

每条 JSONL 记录包含：

```json
{
  "type": "message",
  "timestamp": "2026-03-02T09:00:00Z",
  "message": {
    "role": "user|assistant|toolResult",
    "content": [
      { "type": "text", "text": "..." },
      { "type": "toolCall", "name": "...", "args": {} },
      { "type": "thinking", "text": "..." }
    ],
    "usage": {
      "cost": { "total": 0.001 }
    }
  }
}
```

## 常用查询

### 1. 列出所有会话（按日期排序）

```bash
for f in ~/.openclaw/agents/<agentId>/sessions/*.jsonl; do
  date=$(head -1 "$f" | jq -r '.timestamp' | cut -dT -f1)
  size=$(ls -lh "$f" | awk '{print $5}')
  echo "$date $size $(basename $f)"
done | sort -r
```

### 2. 查找特定日期的会话

```bash
for f in ~/.openclaw/agents/<agentId>/sessions/*.jsonl; do
  head -1 "$f" | jq -r '.timestamp' | grep -q "2026-03-01" && echo "$f"
done
```

### 3. 提取用户消息

```bash
jq -r 'select(.message.role == "user") | .message.content[]? | select(.type == "text") | .text' <session>.jsonl
```

### 4. 搜索关键词

```bash
jq -r 'select(.message.role == "assistant") | .message.content[]? | select(.type == "text") | .text' <session>.jsonl | rg -i "keyword"
```

### 5. 计算会话成本

```bash
jq -s '[.[] | .message.usage.cost.total // 0] | add' <session>.jsonl
```

### 6. 每日成本汇总

```bash
for f in ~/.openclaw/agents/<agentId>/sessions/*.jsonl; do
  date=$(head -1 "$f" | jq -r '.timestamp' | cut -dT -f1)
  cost=$(jq -s '[.[] | .message.usage.cost.total // 0] | add' "$f")
  echo "$date $cost"
done | awk '{a[$1]+=$2} END {for(d in a) print d, "$"a[d]}' | sort -r
```

### 7. 统计消息数量

```bash
jq -s '{
  messages: length,
  user: [.[] | select(.message.role == "user")] | length,
  assistant: [.[] | select(.message.role == "assistant")] | length,
  first: .[0].timestamp,
  last: .[-1].timestamp
}' <session>.jsonl
```

### 8. 工具使用统计

```bash
jq -r '.message.content[]? | select(.type == "toolCall") | .name' <session>.jsonl | sort | uniq -c | sort -rn
```

### 9. 跨所有会话搜索

```bash
rg -l "phrase" ~/.openclaw/agents/<agentId>/sessions/*.jsonl
```

## 依赖工具

| 工具 | 用途 |
|------|------|
| `jq` | JSON 解析和处理 |
| `rg` (ripgrep) | 高性能文本搜索 |

## 使用场景

### 1. 查找历史决策

```
用户: "我们上周讨论的那个渲染方案是什么？"
→ 搜索 session logs 中的关键词
```

### 2. 分析使用模式

```bash
# 查看最常用的工具
jq -r '.message.content[]? | select(.type == "toolCall") | .name' *.jsonl | sort | uniq -c | sort -rn | head -10
```

### 3. 成本审计

```bash
# 每月成本报告
for f in *.jsonl; do
  date=$(head -1 "$f" | jq -r '.timestamp' | cut -dT -f1 | cut -d- -f1,2)
  cost=$(jq -s '[.[] | .message.usage.cost.total // 0] | add' "$f")
  echo "$date $cost"
done | awk '{a[$1]+=$2} END {for(d in a) print d, "$"a[d]}' | sort
```

### 4. 调试问题

```bash
# 查找包含错误的会话
rg -l '"error"' ~/.openclaw/agents/<agentId>/sessions/*.jsonl
```

### 5. 对话回顾

```bash
# 快速文本摘要（低噪音）
jq -r 'select(.type=="message") | .message.content[]? | select(.type=="text") | .text' session.jsonl | rg '关键词'
```

## 性能技巧

1. **大文件处理**: 使用 `head`/`tail` 采样
2. **并行搜索**: `rg` 比 `grep` 快得多
3. **流式处理**: `jq -c` 输出紧凑 JSON
4. **索引利用**: 先查 `sessions.json` 定位

## 与其他 Skills 协同

| Skill | 协同场景 |
|-------|----------|
| **model-usage** | 成本分析和预算控制 |
| **memory** | 从历史对话提取长期记忆 |
| **healthcheck** | 审计日志完整性 |

## 隐私考虑

- 会话日志包含完整对话内容
- 敏感信息可能被记录
- 建议定期清理旧日志
- 生产环境考虑加密存储

## 限制

- 仅能访问当前 Agent 的日志
- 需要足够的磁盘空间
- 大会话文件可能影响性能

## 最佳实践

1. **定期归档**: 压缩旧日志释放空间
2. **关键词索引**: 为常用搜索建立索引
3. **成本监控**: 设置成本告警阈值
4. **敏感数据**: 避免在对话中暴露敏感信息

## 总结

Session Logs Skill 提供了强大的历史对话分析能力，适合：

- ✅ 回顾历史决策
- ✅ 成本分析和优化
- ✅ 使用模式统计
- ✅ 问题调试和审计

**推荐指数**: ⭐⭐⭐⭐⭐ (5/5)

---

*分析者: Glint 🔺 | ClawHub Lab*
