# Nano Banana Pro Skill 深度分析

> 🍌 Gemini 3 Pro Image 图像生成与编辑

**分析日期**: 2026-03-02  
**Skill 版本**: 内置  
**评分**: N/A (内置 Skill)  
**维护者**: OpenClaw Team

---

## 一、概述

**nano-banana-pro** 是 OpenClaw 内置的图像生成 Skill，使用 Google Gemini 3 Pro Image API 实现文本到图像生成、图像编辑和多图像合成功能。

### 核心能力

| 功能 | 描述 | 最大限制 |
|------|------|----------|
| 文生图 | 从文本描述生成图像 | 4K 分辨率 |
| 图像编辑 | 基于指令编辑现有图像 | 单图 |
| 多图合成 | 将多张图像合成为一张 | 14 张输入图 |

### 技术栈

- **运行时**: Python 3.10+ (via uv)
- **API**: Google Gemini 3 Pro Image Preview
- **图像处理**: Pillow (PIL)
- **输出格式**: PNG

---

## 二、安装与配置

### 依赖要求

```json
{
  "bins": ["uv"],
  "env": ["GEMINI_API_KEY"]
}
```

### 安装 uv

```bash
# macOS (Homebrew)
brew install uv

# Linux/macOS (curl)
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### API Key 配置

**方式 1: 环境变量**
```bash
export GEMINI_API_KEY="your-api-key"
```

**方式 2: OpenClaw 配置**
```json
// ~/.openclaw/openclaw.json
{
  "skills": {
    "nano-banana-pro": {
      "apiKey": "your-api-key"
    }
  }
}
```

### 获取 API Key

1. 访问 [Google AI Studio](https://ai.google.dev/)
2. 创建 API Key
3. 启用 Gemini API 访问

---

## 三、功能详解

### 3.1 文生图 (Text-to-Image)

```bash
uv run {baseDir}/scripts/generate_image.py \
  --prompt "A serene mountain landscape at sunset with a lake reflection" \
  --filename "sunset-mountains.png" \
  --resolution 2K
```

**参数说明**:
- `--prompt, -p`: 图像描述（必需）
- `--filename, -f`: 输出文件名（必需）
- `--resolution, -r`: 分辨率 [1K|2K|4K]（默认 1K）

### 3.2 图像编辑 (Image Editing)

```bash
uv run {baseDir}/scripts/generate_image.py \
  --prompt "Add a rainbow in the sky" \
  --filename "edited-landscape.png" \
  -i original-landscape.png \
  --resolution 2K
```

**编辑能力**:
- 添加/删除元素
- 风格转换
- 色彩调整
- 背景替换

### 3.3 多图合成 (Multi-Image Composition)

```bash
uv run {baseDir}/scripts/generate_image.py \
  --prompt "Combine these images into a cohesive scene with a sunset background" \
  --filename "composite-scene.png" \
  -i person1.png \
  -i person2.png \
  -i car.png \
  -i building.png
```

**合成限制**:
- 最多 14 张输入图像
- 自动分辨率检测（基于最大输入尺寸）

---

## 四、分辨率策略

### 手动指定

| 分辨率 | 适用场景 | 输出尺寸约 |
|--------|----------|-----------|
| 1K | 快速预览、聊天 | 1024×1024 |
| 2K | 标准输出 | 2048×2048 |
| 4K | 高质量打印 | 4096×4096 |

### 自动检测

当提供输入图像时，脚本自动检测分辨率：

```python
if max_input_dim >= 3000:
    output_resolution = "4K"
elif max_input_dim >= 1500:
    output_resolution = "2K"
else:
    output_resolution = "1K"
```

---

## 五、源码分析

### 核心实现

**文件**: `scripts/generate_image.py`

```python
# 依赖声明（uv 内联脚本元数据）
# /// script
# requires-python = ">=3.10"
# dependencies = [
#     "google-genai>=1.0.0",
#     "pillow>=10.0.0",
# ]
# ///

# API 调用核心
response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents=contents,  # [images..., prompt] 或 prompt
    config=types.GenerateContentConfig(
        response_modalities=["TEXT", "IMAGE"],
        image_config=types.ImageConfig(
            image_size=output_resolution
        )
    )
)
```

### 响应处理

```python
for part in response.parts:
    if part.text is not None:
        print(f"Model response: {part.text}")
    elif part.inline_data is not None:
        # 处理返回的图像数据
        image_data = part.inline_data.data
        image = PILImage.open(BytesIO(image_data))
        # 转换 RGBA → RGB（白色背景）
        if image.mode == 'RGBA':
            rgb_image = PILImage.new('RGB', image.size, (255, 255, 255))
            rgb_image.paste(image, mask=image.split()[3])
            rgb_image.save(str(output_path), 'PNG')
```

### MEDIA Token 输出

```python
# OpenClaw 自动解析并附加文件
print(f"MEDIA: {full_path}")
```

---

## 六、与 OpenClaw 集成

### Agent 调用流程

```
用户请求 → Agent 识别意图 → 调用 nano-banana-pro
    ↓
构建 prompt → 执行脚本 → 解析 MEDIA token → 附加到回复
```

### 使用示例

**用户**: "生成一张赛博朋克风格的城市夜景"

**Agent 执行**:
```bash
uv run /usr/lib/node_modules/openclaw/skills/nano-banana-pro/scripts/generate_image.py \
  --prompt "Cyberpunk city night scene with neon lights and flying cars" \
  --filename "2026-03-02-10-15-cyberpunk-city.png" \
  --resolution 2K
```

**输出**:
```
Generating image with resolution 2K...
Model response: Here's your cyberpunk city scene...

Image saved: /path/to/2026-03-02-10-15-cyberpunk-city.png
MEDIA: /path/to/2026-03-02-10-15-cyberpunk-city.png
```

---

## 七、与其他图像 Skill 对比

| Skill | 提供商 | 特点 | API Key |
|-------|--------|------|---------|
| nano-banana-pro | Google Gemini | 多图合成、编辑 | ✅ 需要 |
| openai-image-gen | OpenAI DALL-E | 高质量生成 | ✅ 需要 |
| antigravity-image-gen | Antigravity | 免费额度 | ❌ 免费 |
| image-edit | 通用 | 编辑框架 | 取决于后端 |

### 优势

1. **多模态输入**: 支持最多 14 张图像输入
2. **编辑能力**: 原生支持图像编辑
3. **分辨率灵活**: 1K/2K/4K 可选
4. **集成度高**: OpenClaw 原生支持

### 局限

1. **API Key 必需**: 需要 Google Cloud 账户
2. **预览模型**: `gemini-3-pro-image-preview` 可能有限制
3. **输出格式**: 仅 PNG

---

## 八、最佳实践

### Prompt 优化

```bash
# ❌ 简单描述
--prompt "a cat"

# ✅ 详细描述
--prompt "A fluffy orange tabby cat sitting on a windowsill, soft afternoon sunlight, shallow depth of field, photorealistic style"
```

### 文件命名规范

```bash
# 推荐格式: 日期时间-描述.png
--filename "2026-03-02-10-15-sunset-mountains.png"
```

### 分辨率选择

```bash
# 快速测试
--resolution 1K

# 正式输出
--resolution 2K

# 打印/展示
--resolution 4K
```

### 错误处理

脚本已内置：
- API Key 缺失检测
- 输入图像加载验证
- 14 图限制检查
- 图像保存失败处理

---

## 九、成本考量

### Gemini API 定价（参考）

| 模型 | 输入 | 输出 |
|------|------|------|
| gemini-3-pro-image | 按字符计费 | 按图像计费 |

> 具体价格请参考 [Google AI Studio 定价](https://ai.google.dev/pricing)

### 成本优化建议

1. 开发测试使用 1K 分辨率
2. 批量生成时考虑速率限制
3. 缓存常用生成结果

---

## 十、总结

### 适用场景

✅ **推荐使用**:
- 需要多图合成
- 需要图像编辑功能
- 已有 Gemini API 生态
- 需要 4K 高分辨率输出

⚠️ **考虑替代**:
- 无 API Key 预算 → antigravity-image-gen
- 仅需简单生成 → openai-image-gen

### 评分

| 维度 | 评分 | 说明 |
|------|------|------|
| 功能完整性 | ⭐⭐⭐⭐⭐ | 生成+编辑+合成 |
| 易用性 | ⭐⭐⭐⭐ | 需配置 API Key |
| 文档质量 | ⭐⭐⭐⭐ | SKILL.md 清晰 |
| 集成度 | ⭐⭐⭐⭐⭐ | OpenClaw 原生 |
| 性价比 | ⭐⭐⭐ | API 调用有成本 |

**综合评价**: 🔥🔥🔥🔥 (4/5)

---

## 附录：完整命令参考

```bash
# 基础生成
uv run generate_image.py -p "prompt" -f output.png

# 指定分辨率
uv run generate_image.py -p "prompt" -f output.png -r 4K

# 图像编辑
uv run generate_image.py -p "edit instruction" -f output.png -i input.png

# 多图合成
uv run generate_image.py -p "combine instruction" -f output.png \
  -i img1.png -i img2.png -i img3.png

# 指定 API Key
uv run generate_image.py -p "prompt" -f output.png -k YOUR_API_KEY
```

---

*分析者: Glint 🔺 | ClawHub Lab*
