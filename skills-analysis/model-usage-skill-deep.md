# Model Usage Skill 深度分析报告

**分析日期**: 2026-03-02  
**Skill 名称**: model-usage  
**版本**: 内置 Skill  
**评分**: N/A (OpenClaw 内置)  
**分类**: 开发工具 / 成本分析

---

## 📊 概述

Model Usage 是一个专门用于分析 AI 模型使用成本的工具，通过解析 CodexBar CLI 的本地成本日志，提供按模型汇总的使用统计。支持 Codex 和 Claude 两种 provider，可帮助开发者追踪 API 使用情况和成本。

## 🔍 核心功能

### 1. 按模型汇总成本

- **当前模型模式**: 自动识别最近使用的模型（按成本最高的模型）
- **全量模式**: 列出所有模型的总成本
- **时间过滤**: 可限制统计最近 N 天的数据

### 2. 多 Provider 支持

- **Codex**: OpenAI Codex / GPT 系列
- **Claude**: Anthropic Claude 系列

### 3. 输出格式

- **文本格式**: 人类可读的摘要报告
- **JSON 格式**: 结构化数据，支持脚本化处理

## 🛠️ 技术架构

### 依赖关系

```
model-usage
├── CodexBar CLI (必需)
│   ├── codexbar cost 命令
│   └── 本地 JSONL 日志
├── Python 3.x
└── 标准库 (json, subprocess, argparse)
```

### 数据流

```
CodexBar CLI
    ↓
codexbar cost --format json
    ↓
JSON Payload (daily entries + modelBreakdowns)
    ↓
model_usage.py 解析
    ↓
聚合 + 排序
    ↓
文本/JSON 输出
```

### 关键算法

#### 当前模型识别逻辑

1. 获取所有 daily entries
2. 按日期排序（最新优先）
3. 查找最近的 `modelBreakdowns` 数据
4. 选择成本最高的模型
5. 回退到 `modelsUsed` 数组的最后一个

#### 成本聚合

```python
totals[model] = sum(entry.modelBreakdowns[].cost)
```

## 💡 使用场景

### 1. 日常成本监控

```bash
# 查看当前模型使用情况
python /usr/lib/node_modules/openclaw/skills/model-usage/scripts/model_usage.py \
  --provider codex --mode current

# 输出示例：
# Provider: codex
# Current model: gpt-4-turbo
# Latest model date: 2026-03-01
# Total cost (rows): $12.45
# Latest day cost: $2.30 (2026-03-01)
# Daily rows: 15
```

### 2. 多模型成本对比

```bash
# 列出所有模型的总成本
python /usr/lib/node_modules/openclaw/skills/model-usage/scripts/model_usage.py \
  --provider claude --mode all

# 输出示例：
# Provider: claude
# Models:
# - claude-3-opus: $45.20
# - claude-3-sonnet: $23.15
# - claude-2.1: $8.90
```

### 3. 时间范围分析

```bash
# 最近 7 天的使用情况
python /usr/lib/node_modules/openclaw/skills/model-usage/scripts/model_usage.py \
  --provider codex --mode all --days 7
```

### 4. 自动化报告（JSON 输出）

```bash
# 生成 JSON 报告供后续处理
python /usr/lib/node_modules/openclaw/skills/model-usage/scripts/model_usage.py \
  --provider codex --mode all --format json --pretty > usage-report.json

# 示例输出：
{
  "provider": "codex",
  "mode": "all",
  "models": [
    {
      "model": "gpt-4-turbo",
      "totalCostUSD": 45.67
    },
    {
      "model": "gpt-3.5-turbo",
      "totalCostUSD": 12.34
    }
  ]
}
```

### 5. 指定模型分析

```bash
# 分析特定模型的成本
python /usr/lib/node_modules/openclaw/skills/model-usage/scripts/model_usage.py \
  --provider codex --mode current --model gpt-4-turbo
```

## 📋 命令行参数详解

| 参数 | 说明 | 默认值 | 示例 |
|------|------|--------|------|
| `--provider` | AI provider | codex | `--provider claude` |
| `--mode` | 查询模式 | current | `--mode all` |
| `--model` | 指定模型名 | 自动检测 | `--model gpt-4-turbo` |
| `--input` | 输入文件路径 | 调用 codexbar | `--input cost.json` |
| `--days` | 时间范围（天） | 全部 | `--days 7` |
| `--format` | 输出格式 | text | `--format json` |
| `--pretty` | 美化 JSON | false | `--pretty` |

## 🔧 安装与配置

### 前置要求

1. **CodexBar CLI**
   ```bash
   # macOS (brew cask)
   brew install --cask steipete/tap/codexbar
   
   # 或通过应用安装
   # Preferences -> Advanced -> Install CLI
   ```

2. **Python 3.x**
   - 大多数系统已预装

### 数据源路径

- **Codex**: `~/.codex/sessions/*/*.jsonl`
- **Claude**: `~/.config/claude/projects/**/*.jsonl` 或 `~/.claude/projects/**/*.jsonl`

## ⚠️ 限制与注意事项

### 1. 平台限制

- **仅支持 macOS**（需要 CodexBar CLI）
- Linux 支持待定（SKILL.md 标记为 TODO）

### 2. 数据范围

- **仅本地数据**：只统计本地 CLI 使用，不包括：
  - Web 界面使用
  - API 直接调用（非 CodexBar）
  - 其他客户端使用

- **如需全量数据**：使用 `codexbar usage` 而非 `codexbar cost`

### 3. Token 细节

- CodexBar cost 输出中 **tokens 不按模型拆分**
- 只能获取每个模型的 **成本** 数据
- 如需 tokens 细节，需查看原始 JSONL 日志

## 🎯 最佳实践

### 1. 定期成本审计

```bash
# 每周执行一次，生成报告
0 9 * * 1 python /path/to/model_usage.py --provider all --mode all --days 7 --format json > /tmp/weekly-usage.json
```

### 2. 成本异常检测

```bash
# 对比昨天和今天的成本
python model_usage.py --provider codex --mode current --days 1
python model_usage.py --provider codex --mode current --days 2
```

### 3. 模型优化决策

```bash
# 查看各模型成本占比
python model_usage.py --provider codex --mode all --format json --pretty | \
  jq '.models | sort_by(.totalCostUSD) | reverse | .[] | "\(.model): $\(.totalCostUSD)"'
```

### 4. 离线数据分析

```bash
# 导出原始数据供离线分析
codexbar cost --provider codex --format json > /tmp/codex-cost-raw.json

# 后续可多次分析，无需重复调用 codexbar
python model_usage.py --input /tmp/codex-cost-raw.json --mode all
```

## 📈 实际应用示例

### 场景 1: 团队成本分摊

```bash
# 1. 导出 JSON 数据
python model_usage.py --provider codex --mode all --format json --pretty > team-usage.json

# 2. 结合其他工具计算成本
# (假设有多个项目，需要按项目分摊成本)
```

### 场景 2: 模型迁移成本评估

```bash
# 对比不同模型的成本
python model_usage.py --provider codex --mode all

# 输出：
# - gpt-4-turbo: $120.50
# - gpt-3.5-turbo: $15.20

# 评估迁移到更便宜模型的成本节省
```

### 场景 3: 预算控制

```bash
# 设置预算阈值告警（结合 cron）
#!/bin/bash
COST=$(python model_usage.py --provider codex --mode current --days 30 --format json | jq -r '.totalCostUSD')
if (( $(echo "$COST > 100" | bc -l) )); then
  echo "Warning: Monthly cost exceeds $100 (current: \$$COST)"
  # 发送通知
fi
```

## 🔗 相关 Skills

- **session-logs**: 查看详细会话日志
- **healthcheck**: 系统健康检查（可能包含成本监控）
- **openai-image-gen**: 图像生成（可能有独立成本）

## 📚 参考资料

### CodexBar CLI 命令

```bash
# 查看使用快照（web + CLI）
codexbar usage --format json --pretty

# 查看本地成本
codexbar cost --format json --pretty
codexbar cost --provider codex --format json
codexbar cost --provider claude --format json
```

### Cost JSON 结构

```json
{
  "provider": "codex",
  "source": "local",
  "updatedAt": "2026-03-02T04:00:00Z",
  "sessionTokens": 50000,
  "sessionCostUSD": 1.23,
  "last30DaysTokens": 500000,
  "last30DaysCostUSD": 12.34,
  "daily": [
    {
      "date": "2026-03-01",
      "inputTokens": 10000,
      "outputTokens": 5000,
      "totalTokens": 15000,
      "totalCost": 2.50,
      "modelsUsed": ["gpt-4-turbo", "gpt-3.5-turbo"],
      "modelBreakdowns": [
        {"modelName": "gpt-4-turbo", "cost": 2.00},
        {"modelName": "gpt-3.5-turbo", "cost": 0.50}
      ]
    }
  ],
  "totals": {
    "totalInputTokens": 100000,
    "totalOutputTokens": 50000,
    "totalTokens": 150000,
    "totalCost": 25.00
  }
}
```

## 🎓 总结

### 优势

✅ **精准追踪**: 按模型细粒度统计成本  
✅ **自动化友好**: JSON 输出 + 脚本化参数  
✅ **零额外成本**: 仅分析本地日志，无需额外 API 调用  
✅ **多 Provider**: 支持 Codex 和 Claude  
✅ **灵活过滤**: 时间范围 + 模型筛选  

### 限制

⚠️ **平台限制**: 仅 macOS（需 CodexBar CLI）  
⚠️ **数据范围**: 仅本地 CLI 使用，不含 Web/API  
⚠️ **Token 细节**: 无法按模型拆分 tokens  
⚠️ **依赖外部工具**: 需要 CodexBar CLI 正常工作  

### 适用场景

- ✅ 个人开发者成本监控
- ✅ 团队 API 使用审计
- ✅ 模型成本对比分析
- ✅ 自动化预算控制
- ❌ 全平台使用统计（需用 `codexbar usage`）
- ❌ 企业级成本管理（需更复杂的工具）

---

**分析者**: Glint 🔺  
**工作空间**: OpenGL Workspace  
**ClawHub Lab**: https://github.com/kongshan001/clawhub-lab
