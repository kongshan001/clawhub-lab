# Oracle Skill 深度分析报告

**分析日期**: 2026-03-02  
**Skill 名称**: oracle  
**版本**: N/A (CLI Tool)  
**评分**: N/A (OpenClaw 内置 Skill)  
**官网**: https://askoracle.dev  
**Emoji**: 🧿  

---

## 一、核心定位

Oracle 是一个**代码上下文打包工具**，将用户的 prompt + 选定的文件打包成一个"one-shot"请求，发送给其他 AI 模型（如 GPT-5.2 Pro、Claude、Gemini 等），使模型能够基于真实的代码仓库上下文回答问题。

**核心理念**: "一次完整请求，零上下文记忆"

---

## 二、主要特性

### 2.1 双引擎模式

| 引擎 | 特点 | 使用场景 |
|------|------|----------|
| **browser** (默认) | 通过浏览器自动化，支持 GPT-5.2 Pro 长时间思考（10分钟-1小时） | 复杂问题分析、代码重构建议 |
| **api** | 通过 OpenAI API 快速响应 | 需要快速反馈、多模型对比 |

**自动选择逻辑**:
- 如果设置了 `OPENAI_API_KEY` → 使用 API 模式
- 否则 → 使用浏览器模式

### 2.2 智能文件附加

**支持模式**:
```bash
# 包含所有 src 文件
--file "src/**"

# 排除测试文件
--file "src/**" --file "!src/**/*.test.ts"

# 多个路径
--file src/index.ts --file docs --file README.md
```

**自动过滤规则**:
- 默认忽略: `node_modules`, `dist`, `coverage`, `.git`, `.turbo`, `.next`, `build`, `tmp`
- 遵循 `.gitignore` 规则
- 不跟随符号链接
- 过滤 dotfiles（除非明确指定）
- 拒绝 > 1MB 的文件

### 2.3 会话管理

**会话存储**: `~/.oracle/sessions`  
**会话 ID**: 可通过 `--slug` 自定义（3-5 个单词）

**关键命令**:
```bash
# 列出最近 72 小时的会话
oracle status --hours 72

# 重新附加到会话
oracle session <id> --render
```

**重要**: 浏览器模式的 GPT-5.2 Pro 运行可能超时或分离，**不要重新运行**，而是重新附加到现有会话。

### 2.4 Token 预览

**dry-run 模式**:
```bash
# 预览摘要（不消耗 token）
oracle --dry-run summary -p "<task>" --file "src/**"

# 预览完整内容
oracle --dry-run full -p "<task>" --file "src/**"

# 查看文件报告
oracle --dry-run summary --files-report -p "<task>" --file "src/**"
```

---

## 三、典型工作流程

### 3.1 标准流程（Golden Path）

1. **精简文件集**: 选择最少的文件，但包含所有真相
2. **预览**: 使用 `--dry-run` + `--files-report` 检查 token 消耗
3. **执行**: 使用浏览器模式 + GPT-5.2 Pro
4. **重新附加**: 如果超时，附加到存储的会话

### 3.2 浏览器模式运行

```bash
# 主流程
oracle --engine browser --model gpt-5.2-pro \
  -p "Analyze the authentication flow and suggest improvements" \
  --file "src/**" --file "!**/*.test.*"
```

**特点**:
- 支持长时间运行（10分钟-1小时是正常的）
- 会话存储在本地，可以重新附加
- 浏览器附件策略: `--browser-attachments auto|never|always`
  - `auto`: 前 60k 字符内联，之后上传文件

### 3.3 手动粘贴回退

```bash
# 渲染并复制到剪贴板
oracle --render --copy -p "<task>" --file "src/**"
```

---

## 四、Prompt 最佳实践

Oracle 启动时**零项目知识**，模型无法推断技术栈、构建工具或约定。因此需要：

### 4.1 高信号 Prompt 模板

```markdown
# 项目简报
- 技术栈: [stack]
- 构建命令: [build command]
- 测试命令: [test command]
- 平台约束: [constraints]

# 目录结构
- 关键目录: [key directories]
- 入口文件: [entrypoints]
- 配置文件: [config files]
- 边界: [boundaries]

# 具体问题
- 精确描述问题
- 已尝试的方法
- 错误文本（逐字）
- 约束条件（"不要修改 X"，"必须保持公共 API"）
- 期望输出（"返回补丁计划 + 测试"，"给出 3 个选项及权衡"）
```

### 4.2 穷尽式 Prompt 恢复模式

对于长时间调查，编写独立的 prompt + 文件集，以便几天后重新运行：

- 6-30 句项目简报 + 目标
- 复现步骤 + 精确错误 + 已尝试方法
- 附加所有需要的上下文文件

**关键**: Oracle 运行是"one-shot"，模型不记住之前的运行。"恢复上下文"意味着用相同的 prompt + `--file` 集合重新运行。

---

## 五、安全考虑

### 5.1 敏感文件处理

**不要附加**:
- `.env` 文件
- 密钥文件
- 认证令牌
- 任何包含凭证的文件

**原则**: 激进地编辑，只分享必需的内容。

### 5.2 远程浏览器主机

```bash
# 主机端
oracle serve --host 0.0.0.0 --port 9473 --token <secret>

# 客户端
oracle --engine browser \
  --remote-host <host:port> \
  --remote-token <secret> \
  -p "<task>" --file "src/**"
```

---

## 六、与 OpenClaw 的集成

### 6.1 依赖检查

```json
{
  "requires": { "bins": ["oracle"] },
  "install": [
    {
      "id": "node",
      "kind": "node",
      "package": "@steipete/oracle",
      "bins": ["oracle"],
      "label": "Install oracle (node)"
    }
  ]
}
```

### 6.2 在 OpenClaw 中的使用场景

1. **代码审查**: 将关键文件发送给 GPT-5.2 Pro 进行深度分析
2. **架构建议**: 基于完整代码上下文获取重构建议
3. **跨模型对比**: 使用 API 模式对比不同模型的回答
4. **长时间思考**: 利用浏览器模式的长时间运行能力处理复杂问题

---

## 七、使用示例

### 7.1 基础用法

```bash
# 查看帮助
oracle --help

# 如果未安装
npx -y @steipete/oracle --help
```

### 7.2 预览 Token 消耗

```bash
oracle --dry-run summary --files-report \
  -p "Review the error handling strategy" \
  --file "src/**" --file "!**/*.test.*"
```

### 7.3 浏览器模式运行

```bash
oracle --engine browser --model gpt-5.2-pro \
  -p "Suggest performance optimizations for the rendering pipeline" \
  --file "src/render/**" --file "src/shaders/**"
```

### 7.4 重新附加会话

```bash
# 列出最近会话
oracle status --hours 24

# 附加到特定会话
oracle session abc123 --render
```

---

## 八、优势与限制

### 8.1 优势

✅ **零上下文记忆**: 每次运行都是全新的，避免累积错误  
✅ **长时间思考**: 浏览器模式支持 GPT-5.2 Pro 的长时间分析  
✅ **智能文件过滤**: 自动忽略无关文件，减少噪音  
✅ **会话持久化**: 可以重新附加到长时间运行的会话  
✅ **多模型支持**: 支持切换不同的 AI 模型  
✅ **Token 透明**: dry-run 模式预览消耗  

### 8.2 限制

⚠️ **需要重新运行**: 每次都要重新打包上下文（非对话式）  
⚠️ **浏览器模式慢**: GPT-5.2 Pro 可能需要 10分钟-1小时  
⚠️ **文件大小限制**: > 1MB 的文件被拒绝  
⚠️ **不跟随符号链接**: 可能遗漏链接的文件  
⚠️ **依赖外部模型**: 需要 OpenAI API key 或浏览器自动化  

---

## 九、与其他工具的对比

| 工具 | 特点 | 适用场景 |
|------|------|----------|
| **Oracle** | One-shot 请求，零记忆 | 代码审查、架构建议 |
| **Coding Agent** | 交互式，持续上下文 | 迭代开发、重构 |
| **GitHub Copilot** | 实时补全，IDE 集成 | 日常编码 |
| **Cursor** | IDE 深度集成 | 实时协作开发 |

**关键差异**: Oracle 的"一次性完整上下文"模式，适合需要模型基于完整代码库做出判断的场景，而不适合需要多轮对话的迭代开发。

---

## 十、最佳实践总结

### 10.1 文件选择

✅ 选择最少的文件，但包含所有真相  
✅ 明确排除测试文件和无关文件  
✅ 使用 dry-run 预览 token 消耗  
✅ 检查 `.gitignore` 确保重要文件未被忽略  

### 10.2 Prompt 编写

✅ 提供完整的项目简报  
✅ 明确说明技术栈和约束  
✅ 包含精确的错误文本  
✅ 指定期望的输出格式  

### 10.3 会话管理

✅ 使用 `--slug` 自定义会话 ID  
✅ 超时后重新附加，不要重新运行  
✅ 定期清理旧会话（`oracle status --hours 168`）  

### 10.4 安全

✅ 永远不要附加 `.env` 或密钥文件  
✅ 激进地编辑敏感信息  
✅ 使用远程主机时设置强 token  

---

## 十一、结论

Oracle 是一个**强大的代码上下文打包工具**，特别适合需要 AI 模型基于完整代码库做出深度分析的场景。其"一次性完整请求"的设计理念，避免了对话式 AI 的上下文累积错误，同时通过浏览器模式支持 GPT-5.2 Pro 的长时间思考能力。

**推荐使用场景**:
- 代码审查和架构建议
- 性能优化分析
- 跨文件重构规划
- 错误诊断和修复方案

**不推荐使用场景**:
- 需要多轮对话的迭代开发（使用 Coding Agent）
- 实时代码补全（使用 GitHub Copilot）
- 快速问答（使用 API 模式）

---

**探索者**: Glint 🔺  
**探索日期**: 2026-03-02  
**探索状态**: ✅ 完成（基于 SKILL.md 文档分析）
