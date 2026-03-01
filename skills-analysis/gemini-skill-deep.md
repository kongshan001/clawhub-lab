# Gemini Skill 深度分析

**分析日期**: 2026-03-01  
**Skill 名称**: gemini  
**评分**: N/A (内置 Skill)  
**分类**: AI 模型 / 多模态处理

---

## 概述

Gemini Skill 是 OpenClaw 内置的 Google Gemini AI 集成工具，提供一次性 Q&A、摘要和内容生成功能。通过 Gemini CLI 封装，支持多种输出格式和模型选择。

## 核心功能

### 1. 一次性问答 (One-shot Q&A)
```bash
gemini "Answer this question..."
```

### 2. 模型选择
```bash
gemini --model <name> "Prompt..."
```

支持的模型（根据 Google AI 文档）：
- `gemini-1.5-flash` - 快速响应
- `gemini-1.5-pro` - 高级推理
- `gemini-2.0-flash-exp` - 实验版本

### 3. 多格式输出
```bash
gemini --output-format json "Return JSON"
```

支持的格式：
- `text` (默认)
- `json`
- `markdown`

### 4. 扩展管理
```bash
gemini --list-extensions
gemini extensions <command>
```

## 技术架构

### 依赖项
- **CLI 工具**: `gemini-cli` (通过 Homebrew 安装)
- **认证**: OAuth 2.0 (首次运行需交互式登录)
- **API**: Google AI Studio API

### 安装方式

#### macOS (推荐)
```bash
brew install gemini-cli
```

#### Linux (替代方案)
由于 Homebrew 在 Linux 上需要额外配置，可以考虑：
1. 使用 `npm` 安装官方 SDK
2. 使用 `pip install google-generativeai`
3. 直接调用 REST API

### 配置要求
- Google AI Studio API Key
- 网络访问 `https://ai.google.dev/`

## 使用场景

### ✅ 适合的场景
1. **快速问答**: 单次查询，无需上下文
2. **文本摘要**: 长文档快速总结
3. **内容生成**: 创意写作、代码片段
4. **JSON 提取**: 结构化数据提取
5. **多模态理解**: 图像 + 文本混合输入（需 CLI 支持）

### ❌ 不适合的场景
1. **多轮对话**: 不保持会话状态
2. **流式输出**: CLI 不支持实时流
3. **敏感数据**: 需要考虑 API 数据隐私

## 与 OpenClaw 集成

### 自动激活条件
根据 SKILL.md 的 description：
> "Gemini CLI for one-shot Q&A, summaries, and generation."

当用户请求：
- 快速问答
- 文本摘要
- 内容生成

### 工作流程
```
用户请求 → OpenClaw 识别意图 → 激活 gemini skill → 
调用 CLI → 返回结果 → 格式化输出
```

### 示例对话
```
User: "用 Gemini 总结这篇文章..."
OpenClaw: [激活 gemini skill]
          gemini "Summarize this article: [content]"
          → 返回摘要
```

## 性能与限制

### 性能特点
- **响应速度**: 取决于 Gemini API（通常 1-3 秒）
- **并发限制**: API 配额限制（免费版：60 RPM）
- **上下文窗口**: Gemini 1.5 Pro 支持 1M tokens

### 已知限制
1. **安装依赖**: Linux 系统需要额外配置
2. **认证流程**: 首次使用需要交互式登录
3. **安全警告**: 避免使用 `--yolo` 标志

### 安全建议
- ✅ 使用 OAuth 认证
- ✅ 限制敏感数据传输
- ❌ 不要使用 `--yolo` 绕过安全检查
- ❌ 不要在 prompt 中包含机密信息

## 与其他 Skills 对比

| Skill | 模型 | 用途 | 优势 |
|-------|------|------|------|
| **gemini** | Gemini 1.5 | 通用 Q&A | 多模态、长上下文 |
| openai-image-gen | DALL-E | 图像生成 | 高质量图像 |
| openai-whisper | Whisper | 语音转文字 | 高精度 ASR |
| summarize | 多种 | 文本摘要 | OpenClaw 原生 |

## 代码示例

### 示例 1: 快速问答
```bash
# 简单问题
gemini "What is the capital of France?"

# 指定模型
gemini --model gemini-1.5-pro "Explain quantum computing"
```

### 示例 2: JSON 提取
```bash
# 提取结构化数据
gemini --output-format json "Extract names and emails from: John (john@example.com), Jane (jane@example.com)"

# 输出示例
{
  "people": [
    {"name": "John", "email": "john@example.com"},
    {"name": "Jane", "email": "jane@example.com"}
  ]
}
```

### 示例 3: 文本摘要
```bash
# 长文档摘要
cat long-article.txt | gemini "Summarize this text in 3 bullet points"
```

### 示例 4: 代码生成
```bash
# 生成代码片段
gemini "Write a Python function to calculate fibonacci numbers"
```

## 高级用法

### 1. 管道操作
```bash
# 结合其他 CLI 工具
curl -s https://example.com/api/data | gemini "Summarize this JSON response"
```

### 2. 批处理
```bash
# 处理多个文件
for file in *.txt; do
  gemini "Summarize: $(cat $file)" > "${file%.txt}-summary.md"
done
```

### 3. 集成到脚本
```bash
#!/bin/bash
# auto-commit-msg.sh - 自动生成 commit 消息

diff=$(git diff --cached)
message=$(gemini "Generate a git commit message for these changes: $diff")
git commit -m "$message"
```

## 扩展性

### Gemini Extensions
```bash
# 列出可用扩展
gemini --list-extensions

# 扩展可能包括：
# - Google Workspace 集成
# - 代码执行环境
# - 外部 API 连接器
```

### 自定义扩展
根据 Google AI 文档，可以创建自定义扩展：
1. 定义 OpenAPI 规范
2. 配置认证方式
3. 注册到 Gemini CLI

## 最佳实践

### 1. Prompt 工程
```bash
# ❌ 不好
gemini "Tell me about dogs"

# ✅ 更好
gemini "Provide a structured overview of dog breeds, including:
- Size categories
- Temperament traits
- Care requirements
Format as markdown with headers."
```

### 2. 错误处理
```bash
# 在脚本中添加错误检查
result=$(gemini "Your prompt" 2>&1)
if [ $? -eq 0 ]; then
  echo "Success: $result"
else
  echo "Error: $result"
fi
```

### 3. 缓存策略
```bash
# 缓存常用查询
cache_file="/tmp/gemini-cache-$(echo "$prompt" | md5sum | cut -d' ' -f1)"
if [ -f "$cache_file" ]; then
  cat "$cache_file"
else
  gemini "$prompt" | tee "$cache_file"
fi
```

## 故障排查

### 问题 1: CLI 未安装
```bash
# 检查安装
which gemini
# → 无输出表示未安装

# 解决方案
brew install gemini-cli
```

### 问题 2: 认证失败
```bash
# 重新认证
gemini
# → 按照提示完成 OAuth 流程
```

### 问题 3: API 配额超限
```bash
# 错误信息
Error: Rate limit exceeded

# 解决方案
# 1. 等待配额重置（通常 1 分钟）
# 2. 升级到付费计划
# 3. 减少请求频率
```

### 问题 4: Linux 安装问题
```bash
# 替代方案 1: 使用 Python SDK
pip install google-generativeai

# 替代方案 2: 使用 Node.js SDK
npm install @google/generative-ai

# 替代方案 3: 直接调用 API
curl -X POST \
  'https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=YOUR_API_KEY' \
  -H 'Content-Type: application/json' \
  -d '{"contents":[{"parts":[{"text":"Your prompt"}]}]}'
```

## 未来展望

### 短期改进
1. **Linux 原生支持**: 提供非 Homebrew 安装方式
2. **流式输出**: 支持实时响应流
3. **上下文保持**: 添加会话模式

### 长期规划
1. **多模态增强**: 更好的图像/视频处理
2. **本地模型**: 支持 Gemma 本地部署
3. **工具调用**: 函数调用能力

## 总结

### 优势
✅ 简单易用的一次性调用  
✅ 支持多种输出格式  
✅ 长上下文窗口（1M tokens）  
✅ 多模态能力（图像 + 文本）  
✅ 与 OpenClaw 无缝集成  

### 劣势
❌ Linux 安装依赖 Homebrew  
❌ 不支持多轮对话  
❌ API 配额限制  
❌ 需要网络连接  

### 推荐指数
⭐⭐⭐⭐ (4/5)

适合需要快速 AI 能力的场景，但对于复杂的多轮交互，建议使用 OpenClaw 原生的对话能力。

---

**分析者**: Glint 🔺  
**OpenClaw 版本**: Latest  
**Gemini CLI 版本**: N/A (未安装)  
**文档来源**: SKILL.md + Google AI 官方文档

## 参考资料

- [Google AI Studio](https://ai.google.dev/)
- [Gemini API 文档](https://ai.google.dev/docs)
- [Gemini CLI GitHub](https://github.com/google/generative-ai-tools)
- [OpenClaw Skills 文档](https://docs.openclaw.ai)
