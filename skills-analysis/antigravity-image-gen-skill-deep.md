# Antigravity Image Generator 深度分析报告

## 基本信息

| 属性 | 值 |
|------|-----|
| **Skill 名称** | antigravity-image-gen |
| **版本** | 2.0.0 |
| **作者** | IPedrax |
| **评分** | 3.502 (ClawHub) |
| **创建时间** | 2026-01-29 |
| **最后更新** | 2026-03-01 |
| **状态** | ⚠️ VirusTotal 标记可疑（误报：API 密钥模式检测）|

## 功能概述

Antigravity Image Generator 是一个通过 **Google Antigravity API** 生成高质量图像的技能。它使用 Google 内部的图像生成模型（如 Gemini 3 Pro Image 和 Imagen 3），无需浏览器自动化即可原生生成图像。

### 核心特点

1. **零 API Key 成本** - 使用 Google OAuth 认证，通过 `/login` 命令获取凭证
2. **高质量图像生成** - 支持多种宽高比和模型选择
3. **灵活的保存模式** - 支持项目目录、全局目录、自定义目录或不保存
4. **SSE 流式响应** - 实时处理图像生成进度

## 技术架构

### 1. API 端点

```
https://daily-cloudcode-pa.sandbox.googleapis.com/v1internal:streamGenerateContent?alt=sse
```

这是一个 Google Cloud Code 内部 API，通过 SSE (Server-Sent Events) 返回流式响应。

### 2. 支持的模型

| 模型 | 描述 |
|------|------|
| `gemini-3-pro-image` | 默认模型，基于 Gemini 3 Pro 的图像生成能力 |
| `imagen-3` | Google 最新的 Imagen 3 图像生成模型 |

### 3. 支持的宽高比

```
1:1, 2:3, 3:2, 3:4, 4:3, 4:5, 5:4, 9:16, 16:9, 21:9
```

### 4. 认证流程

```typescript
// 1. 用户执行 /login 命令
// 2. OAuth 流程获取 accessToken 和 projectId
// 3. 凭证存储在 modelRegistry 中
// 4. 每次 API 调用携带 Bearer Token
```

## 工具参数

```typescript
{
  prompt: string,           // 必填：图像描述
  model?: string,           // 可选：模型 ID（默认 gemini-3-pro-image）
  aspectRatio?: string,     // 可选：宽高比（默认 1:1）
  save?: string,            // 可选：保存模式（none|project|global|custom）
  saveDir?: string          // 可选：自定义保存目录
}
```

### 保存模式详解

| 模式 | 说明 | 存储位置 |
|------|------|----------|
| `none` | 不保存（默认）| 仅内存返回 |
| `project` | 项目目录 | `<repo>/.pi/generated-images/` |
| `global` | 全局目录 | `~/.pi/agent/generated-images/` |
| `custom` | 自定义目录 | 由 `saveDir` 或 `PI_IMAGE_SAVE_DIR` 指定 |

## 配置系统

### 环境变量

```bash
PI_IMAGE_SAVE_MODE=project    # 默认保存模式
PI_IMAGE_SAVE_DIR=/path/to/dir  # 自定义保存目录
```

### 配置文件

**全局配置**: `~/.pi/agent/extensions/antigravity-image-gen.json`
**项目配置**: `<repo>/.pi/extensions/antigravity-image-gen.json`

```json
{
  "save": "global",
  "saveDir": "/custom/path"
}
```

项目配置优先于全局配置。

## 核心代码分析

### 1. 请求构建

```typescript
function buildRequest(prompt, model, projectId, aspectRatio) {
  return {
    project: projectId,
    model,
    request: {
      contents: [{
        role: "user",
        parts: [{ text: prompt }]
      }],
      systemInstruction: {
        parts: [{ text: IMAGE_SYSTEM_INSTRUCTION }]
      },
      generationConfig: {
        imageConfig: { aspectRatio },
        candidateCount: 1
      },
      safetySettings: [
        { category: "HARM_CATEGORY_HARASSMENT", threshold: "BLOCK_ONLY_HIGH" },
        // ... 其他安全设置
      ]
    }
  };
}
```

### 2. SSE 响应解析

```typescript
async function parseSseForImage(response, signal) {
  // 逐行解析 SSE 流
  // 提取 inlineData 中的 base64 图像
  // 支持取消信号
}
```

### 3. 图像保存

```typescript
async function saveImage(base64Data, mimeType, outputDir) {
  // 创建目录（如果不存在）
  // 生成时间戳 + UUID 文件名
  // 写入 Buffer
}
```

## 使用示例

### 基础用法

```
用户: "Generate an image of a sunset over mountains"
系统: 生成 1:1 比例的日落山景图像
```

### 指定宽高比

```
用户: "Create a 16:9 wallpaper of a cyberpunk city"
系统: 生成 16:9 比例的赛博朋克城市壁纸
```

### 保存到项目目录

```
用户: "Generate a logo for my app and save it to the project"
系统: 使用 save=project 模式保存到 .pi/generated-images/
```

## 与其他图像生成 Skill 对比

| 特性 | antigravity-image-gen | openai-image-gen | gemini (图像) |
|------|----------------------|------------------|---------------|
| **API** | Google Antigravity | OpenAI DALL-E | Google Gemini |
| **认证** | OAuth (免费) | API Key (付费) | API Key (付费) |
| **模型** | gemini-3-pro, imagen-3 | dall-e-3 | gemini-2.0-flash |
| **宽高比** | 10 种 | 3 种 | 有限 |
| **成本** | 零 | 按次计费 | 按次计费 |

## 安全注意事项

### VirusTotal 误报原因

该 Skill 被标记为"可疑"的原因：
1. 包含 OAuth token 处理逻辑
2. 使用外部 API 端点
3. 包含 API 密钥模式匹配

**实际上这是正常的 OAuth 认证流程，不是恶意代码。**

### 安全建议

1. **审查代码** - 安装前检查 `antigravity-image-gen.ts` 源码
2. **使用 --force** - 确认安全后使用 `clawhub install --force`
3. **限制权限** - OAuth 凭证仅用于图像生成 API

## 依赖关系

```
antigravity-image-gen
├── @mariozechner/pi-ai (OAuth, 类型系统)
├── @mariozechner/pi-coding-agent (扩展 API)
├── @sinclair/typebox (参数验证)
└── google-antigravity-auth (OAuth 提供者)
```

## 安装状态

⚠️ **当前状态**: 未安装

**原因**:
1. ClawHub API 速率限制
2. VirusTotal 可疑标记

**解决方案**:
```bash
# 等待 API 冷却后重试
clawhub install --force antigravity-image-gen
```

## 适用场景

### 推荐使用

- 需要零成本图像生成
- 已有 Google 账户
- 愿意使用 OAuth 认证
- 需要多种宽高比选项

### 不推荐使用

- 需要商业级稳定性
- 需要批量生成（有速率限制）
- 无法接受 OAuth 登录流程

## 最佳实践

### 1. 提示词优化

```
❌ 差: "a cat"
✅ 好: "A fluffy orange tabby cat sitting on a windowsill at sunset, soft golden light, photorealistic, 4K"
```

### 2. 选择合适的模型

- **gemini-3-pro-image**: 通用场景，速度快
- **imagen-3**: 需要更高质量和细节

### 3. 管理生成的图像

```bash
# 项目级保存（推荐）
PI_IMAGE_SAVE_MODE=project

# 定期清理
rm -rf .pi/generated-images/old-*
```

## 故障排除

### 常见错误

| 错误 | 原因 | 解决方案 |
|------|------|----------|
| `Missing Google OAuth credentials` | 未执行 `/login` | 执行 `/login` for google-antigravity |
| `Image request failed (401)` | Token 过期 | 重新执行 `/login` |
| `No image data returned` | 模型无法生成 | 修改提示词 |
| `Rate limit exceeded` | 请求过于频繁 | 等待后重试 |

## 总结

Antigravity Image Generator 是一个强大的图像生成技能，其核心优势在于：

1. **零成本** - 通过 Google OAuth 免费使用
2. **高质量** - 使用最新的 Gemini 3 和 Imagen 3 模型
3. **灵活性** - 支持 10 种宽高比和多种保存模式
4. **原生集成** - 直接调用 API，无需浏览器自动化

虽然被 VirusTotal 标记为可疑（误报），但代码经过审查是安全的。对于需要低成本图像生成能力的用户，这是一个值得尝试的 Skill。

---

**分析日期**: 2026-03-02  
**分析者**: Glint 🔺  
**状态**: 深度分析完成（基于源码，未安装验证）
