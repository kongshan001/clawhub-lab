# Image Generation Skill 深度分析

## 基本信息

| 属性 | 值 |
|------|-----|
| 名称 | Image Generation |
| Slug | image-generation |
| 版本 | 1.0.2 |
| 评分 | 3.602 |
| 主页 | https://clawic.com/skills/image-generation |
| 描述 | AI 图像生成，支持提示工程、风格控制和多提供商指南 |

## 核心功能

### 1. 多提供商支持

该 Skill 整合了 **7 大主流 AI 图像生成平台**：

| 提供商 | 优势 | 定价 |
|--------|------|------|
| **OpenAI DALL-E** | 文字渲染、指令遵循、图像编辑 | $0.02-0.08/图 |
| **Midjourney** | 艺术质量、照片级真实感 | $10-60/月 |
| **Stable Diffusion** | 完全控制、隐私、免费本地运行 | 免费 (需 GPU) |
| **Flux** | 照片级、快速生成、Apache 2.0 | ~$0.03-0.05/图 |
| **Leonardo AI** | 游戏资产、角色设计、风格一致 | ~$0.02-0.10/图 |
| **Ideogram** | 文字渲染最佳、Logo 设计 | $7-48/月 |
| **Replicate** | 多模型测试、按需付费 | ~$0.003-0.05/图 |

### 2. 提示工程指南

```markdown
[Subject] + [Style] + [Lighting] + [Composition] + [Quality modifiers]
```

**示例：**
```
A red fox in a snowy forest, digital art style, golden hour lighting,
close-up portrait, highly detailed, 8k resolution
```

### 3. 任务导向推荐

| 任务 | 最佳提供商 |
|------|-----------|
| 照片级真实感 | Midjourney, Flux Pro |
| 图像中文字 | Ideogram, DALL-E 3 |
| 快速迭代 | Flux Schnell, Leonardo |
| 最大控制 | Stable Diffusion |
| 图像修复/编辑 | DALL-E 3, Stable Diffusion |
| 预算 API | Replicate, Leonardo |

## 架构设计

### 持久化结构

```
~/image-generation/
├── memory.md      # 当前提供商、风格、项目
└── history.md     # 历史生成记录（可选）
```

### 文档模块

| 文件 | 内容 |
|------|------|
| `prompting.md` | 提示词技术、权重、种子 |
| `api-patterns.md` | API 处理模式 |
| `openai.md` | DALL-E 3 / GPT Image |
| `midjourney.md` | Discord 参数、非官方 API |
| `stable-diffusion.md` | 本地部署、img2img、inpainting |
| `flux.md` | Schnell/Dev/Pro 模型 |
| `leonardo.md` | 游戏资产、Alchemy 增强 |
| `ideogram.md` | 文字渲染、Logo 设计 |
| `replicate.md` | 多模型网关 |

## 核心规则

### 1. 先检查记忆
读取 `~/image-generation/memory.md` 了解用户偏好

### 2. 先草稿后成品
- 从 512x512 或 1024x1024 开始验证提示词
- 生成 4+ 个变体
- 只对最佳结果放大

### 3. 提示词结构
- 主体优先："A red fox" 而非 "In the forest there is a red fox"
- 具体风格："cinematic lighting" 而非 "good lighting"
- 匹配比例：1:1 人像，16:9 风景

### 4. 更新记忆
| 事件 | 动作 |
|------|------|
| 用户选择提供商 | 保存到 memory.md |
| 风格效果好 | 记录到 memory.md |
| 新项目开始 | 添加到 memory.md |

## 常见问题与解决方案

| 问题 | 解决方案 |
|------|----------|
| 手/手指错误 | 重新生成或使用 inpainting |
| 文字乱码 | 使用 Ideogram 或后期添加文字 |
| 面部扭曲 | 添加 "detailed face" 到提示词 |
| 风格不一致 | 锁定种子，使用参考图 |
| 水印出现 | 检查模型训练数据，使用干净模型 |

## 代码示例

### OpenAI DALL-E 3

```python
from openai import OpenAI
client = OpenAI()

response = client.images.generate(
    model="dall-e-3",
    prompt="A white siamese cat wearing a beret",
    size="1024x1024",
    quality="hd",
    n=1
)
image_url = response.data[0].url
```

### Flux Schnell (本地)

```python
import torch
from diffusers import FluxPipeline

pipe = FluxPipeline.from_pretrained(
    "black-forest-labs/FLUX.1-schnell",
    torch_dtype=torch.bfloat16
)
pipe.enable_model_cpu_offload()

image = pipe(
    prompt="A cat holding a sign that says hello world",
    height=1024, width=1024,
    guidance_scale=0,
    num_inference_steps=4
).images[0]
```

### Stable Diffusion XL

```python
import torch
from diffusers import StableDiffusionXLPipeline

pipe = StableDiffusionXLPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    torch_dtype=torch.float16
)
pipe.to("cuda")

image = pipe(
    prompt="A majestic lion in a field of flowers",
    negative_prompt="blurry, low quality, distorted",
    num_inference_steps=30,
    guidance_scale=7.5
).images[0]
```

### Replicate (多模型)

```python
import replicate

output = replicate.run(
    "black-forest-labs/flux-schnell",
    input={
        "prompt": "A sunset over mountains",
        "width": 1024, "height": 1024
    }
)
print(output[0])  # Image URL
```

## 安全与隐私

### 外部端点

| 提供商 | 端点 | 发送数据 |
|--------|------|----------|
| OpenAI | api.openai.com | 提示词 |
| Midjourney | discord.com | 提示词 |
| Stability AI | api.stability.ai | 提示词 |
| Replicate | api.replicate.com | 提示词 |
| Leonardo | cloud.leonardo.ai | 提示词 |
| Ideogram | api.ideogram.ai | 提示词 |

### 数据处理

- **离开设备：** 仅提示词发送到选定提供商
- **本地保留：** 偏好设置存储在 `~/image-generation/`
- **不上传：** 生成的图像（由提供商处理存储）

## 评分分析

### 3.602 分的构成

1. **文档完整性** - 7 个提供商完整指南 ✅
2. **实用价值** - 多平台整合 + 提示工程 ✅
3. **代码示例** - 每个提供商都有可运行代码 ✅
4. **架构设计** - 记忆系统 + 历史追踪 ✅
5. **安全透明** - 端点披露 + 数据流说明 ✅

## 使用场景

### 适合
- 需要 AI 生成图像的任何场景
- 想要比较不同平台效果
- 需要提示词工程指导
- 本地部署需求（Stable Diffusion）

### 不适合
- 纯视频生成（需其他 Skill）
- 3D 模型生成（需 3d-cog）
- 图像后期编辑（需 image-edit）

## 与其他 Skills 对比

| Skill | 评分 | 特点 |
|-------|------|------|
| **image-generation** | 3.602 | 多平台整合 + 提示工程指南 |
| antigravity-image-gen | 3.567 | 零成本生成（特定方法） |
| openai-image-gen | 内置 | OpenAI 专用 |
| nano-banana-pro | 已分析 | Gemini 3 Pro Image |

## 安装命令

```bash
clawhub install image-generation
```

## 总结

**Image Generation** 是一个高价值的综合型 Skill，它不是单一平台的封装，而是：

1. **多平台指南** - 7 大提供商的完整文档
2. **提示词教育** - 系统化的提示工程技术
3. **架构规范** - 记忆系统 + 历史追踪
4. **代码模板** - 即用型代码示例

对于需要 AI 图像生成能力的 Agent 来说，这是一个**必备参考 Skill**。

---

*分析日期: 2026-03-02 by Glint 🔺*
