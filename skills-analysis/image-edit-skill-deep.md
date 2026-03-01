# Image Editing Skill - 深度分析报告

**Skill 名称:** Image Editing  
**评分:** 3.158 (ClawHub)  
**安装日期:** 2026-03-02  
**分析者:** Glint 🔺

---

## 一、概述

`image-edit` 是一个专注于 AI 图像编辑的综合技能，提供六大核心编辑能力：

1. **Inpainting（图像修复）** - 移除/替换图像中的对象
2. **Outpainting（外扩）** - 扩展图像边界
3. **Background Removal（背景移除）** - 提取主体，透明背景
4. **Upscaling（超分辨率）** - 提升图像分辨率
5. **Restoration（图像修复）** - 修复模糊、老照片、人脸
6. **Style Transfer（风格迁移）** - 改变图像风格

---

## 二、核心功能详解

### 2.1 Inpainting（图像修复）

**用途：** 移除不需要的对象，或替换图像中的特定区域

**技术原理：**
- 使用掩码（mask）标记需要修改的区域
- AI 根据周围上下文填充被移除的区域
- 可选提供文本提示描述填充内容

**支持工具：**

| 工具 | 类型 | 特点 | 成本 |
|------|------|------|------|
| DALL-E 2 | 云端 API | OpenAI 支持，质量稳定 | $0.02/图 |
| Stable Diffusion Inpaint | 本地/云端 | 开源，可自定义 | 免费 |
| IOPaint | 本地 | 专为对象移除设计 | 免费 |

**代码示例（Stable Diffusion）：**

```python
from diffusers import StableDiffusionInpaintPipeline
import torch

pipe = StableDiffusionInpaintPipeline.from_pretrained(
    "runwayml/stable-diffusion-inpainting",
    torch_dtype=torch.float16
)
pipe.to("cuda")

result = pipe(
    prompt="A fluffy cat",  # 可选：描述填充内容
    image=init_image,
    mask_image=mask,
    num_inference_steps=30,
    guidance_scale=7.5
).images[0]
```

**最佳实践：**
- 掩码边缘要略微扩展，覆盖对象边缘
- 提供上下文描述有助于生成更自然的内容
- 大面积修改建议分多次进行
- 生成后检查边缘，必要时手动修饰

---

### 2.2 Outpainting（外扩）

**用途：** 将图像扩展到原始边界之外

**技术原理：**
- 将原图放置在更大的画布上
- 掩码标记空白区域
- AI 生成与原图风格一致的新内容

**典型应用场景：**
- 将竖屏照片转为横屏
- 将正方形图片扩展为 16:9 电影画幅
- 扩展风景照片的视野

**代码示例（DALL-E 2）：**

```python
from openai import OpenAI
from PIL import Image

client = OpenAI()

# 创建扩展画布
original = Image.open("photo.png")
extended = Image.new("RGBA", (1024, 1024), (0, 0, 0, 0))
extended.paste(original, (256, 256))  # 居中放置

# 创建掩码（透明区域 = 编辑区域）
mask = Image.new("RGBA", (1024, 1024), (255, 255, 255, 255))
mask.paste(Image.new("RGBA", original.size, (0, 0, 0, 0)), (256, 256))

response = client.images.edit(
    model="dall-e-2",
    image=to_bytes(extended),
    mask=to_bytes(mask),
    prompt="Continue the landscape with mountains in the distance"
)
```

**关键技巧：**
- 画布扩展不要一次性过大（建议每次 2-3 倍）
- 提供风格描述保持一致性
- 注意光照方向的连续性

---

### 2.3 Background Removal（背景移除）

**用途：** 提取图像主体，生成透明背景 PNG

**支持工具：**

| 工具 | 类型 | 特点 | 成本 |
|------|------|------|------|
| remove.bg | 云端 API | 商业级质量 | $0.20/图（50 张/月免费）|
| ClipDrop | 云端 API | Stability AI 出品 | 按量计费 |
| rembg | 本地 | 开源，支持 GPU 加速 | 免费 |

**代码示例（rembg 本地）：**

```python
from rembg import remove
from PIL import Image

# 单张图片
output = remove(Image.open("input.jpg"))
output.save("output.png")

# 批量处理
from pathlib import Path
for img_path in Path("input/").glob("*.jpg"):
    result = remove(Image.open(img_path))
    result.save(f"output/{img_path.stem}.png")
```

**模型选择：**
- `u2net` - 通用场景（默认）
- `u2net_human_seg` - 人物优化
- `silueta` - 更快速度，更小模型

**质量优化：**
- 边缘羽化：在 alpha 通道应用高斯模糊
- 头发/毛发：使用专门的 matting 模型
- 手动修整：检查复杂边缘

---

### 2.4 Upscaling（超分辨率）

**用途：** 提升图像分辨率，最高可达 4-8 倍

**支持工具：**

| 工具 | 放大倍数 | 质量 | 速度 | 成本 |
|------|---------|------|------|------|
| Real-ESRGAN | 4x | 优秀 | 快 | 免费 |
| Topaz Gigapixel | 6x | 卓越 | 中等 | $99（一次性）|
| Magnific AI | 2-4x | 最佳（创意增强）| 中等 | 按量计费 |

**代码示例（Real-ESRGAN）：**

```python
from realesrgan import RealESRGAN
import torch

model = RealESRGAN(torch.device("cuda"), scale=4)
model.load_weights("weights/RealESRGAN_x4plus.pth")

result = model.predict(input_image)
```

**放大策略：**
- 单次放大不超过 4 倍
- 根据内容选择模型（通用 vs 动漫）
- 人像启用 face_enhance 选项
- 放大后检查是否有 AI 产生的伪影

**处理顺序：**
1. 先修复人脸（GFPGAN/CodeFormer）
2. 去除噪点/伪影
3. 超分辨率放大
4. 适度锐化

---

### 2.5 Restoration（图像修复）

**用途：** 修复模糊、老照片、受损图像

**主要能力：**

#### 人脸修复
- **GFPGAN** - 开源人脸修复
- **CodeFormer** - 可调节保真度

```python
from gfpgan import GFPGANer

restorer = GFPGANer(
    model_path="GFPGANv1.4.pth",
    upscale=2,
    arch="clean",
    channel_multiplier=2
)

_, _, output = restorer.enhance(
    input_img,
    has_aligned=False,
    only_center_face=False,
    paste_back=True
)
```

**CodeFormer 保真度滑块：**
- 低（0.1-0.3）- 更强的增强，可能改变面部特征
- 高（0.7-0.9）- 保持原始特征，增强程度较低

#### 老照片修复
- 去除划痕
- 人脸修复
- 色彩增强

#### 去噪
- Real-ESRGAN（scale=1 仅去噪）
- OpenCV 快速去噪

#### 黑白上色
- DeOldify
- Replicate API

**完整修复流程：**
1. 去除划痕/损伤
2. 去噪
3. 修复人脸
4. 上色（如果是黑白）
5. 超分辨率放大
6. 适度锐化

---

### 2.6 Style Transfer（风格迁移）

**用途：** 改变图像风格，保留内容结构

**技术方案：**

#### 1. img2img（Stable Diffusion）

```python
from diffusers import StableDiffusionImg2ImgPipeline

pipe = StableDiffusionImg2ImgPipeline.from_pretrained(
    "runwayml/stable-diffusion-v1-5",
    torch_dtype=torch.float16
)

result = pipe(
    prompt="oil painting style, impressionist",
    image=init_image,
    strength=0.6,  # 0=无变化，1=完全重新生成
    guidance_scale=7.5
).images[0]
```

**Strength 参数：**
- 0.3-0.4 - 轻度风格化，保留大部分细节
- 0.5-0.6 - 平衡转换
- 0.7-0.8 - 重度风格化，可能丢失细节

#### 2. ControlNet（精确控制）

```python
from diffusers import StableDiffusionControlNetPipeline, ControlNetModel

controlnet = ControlNetModel.from_pretrained(
    "lllyasviel/sd-controlnet-canny",
    torch_dtype=torch.float16
)
pipe = StableDiffusionControlNetPipeline.from_pretrained(
    "runwayml/stable-diffusion-v1-5",
    controlnet=controlnet
)

# 提取边缘作为结构引导
import cv2
canny = cv2.Canny(image, 100, 200)

result = pipe(
    prompt="anime style illustration",
    image=canny,
    num_inference_steps=30
).images[0]
```

**ControlNet 模式：**
- `canny` - 边缘检测
- `depth` - 深度图
- `pose` - 人体姿态
- `lineart` - 线稿

#### 3. IP-Adapter（风格参考）

使用参考图像作为风格指南，实现精确的风格复制。

**常见风格关键词：**

| 风格 | 关键词 |
|------|--------|
| 油画 | oil painting, brushstrokes, impasto |
| 水彩 | watercolor, soft edges, wet medium |
| 动漫 | anime style, cel shaded, studio ghibli |
| 素描 | pencil drawing, graphite, sketch |
| 3D 渲染 | 3D render, octane, blender |
| 像素艺术 | pixel art, 8-bit, retro |
| 照片级 | hyperrealistic, photography, DSLR |

---

## 三、工具生态

### 3.1 云端 API

| 提供商 | 功能 | 定价 |
|--------|------|------|
| OpenAI (DALL-E 2) | Inpainting, Outpainting | $0.02/图 (1024x1024) |
| Stability AI | img2img, Inpainting | 按量计费 |
| ClipDrop | 背景移除、清理、重光照 | 按量计费 |
| remove.bg | 背景移除 | $0.20/图（50 张/月免费）|
| Replicate | 多种模型托管 | 按量计费 |

### 3.2 本地工具

| 工具 | 功能 | 安装方式 |
|------|------|---------|
| IOPaint | Inpainting（对象移除）| `pip install iopaint` |
| rembg | 背景移除 | `pip install rembg[gpu]` |
| Real-ESRGAN | 超分辨率 | `pip install realesrgan` |
| GFPGAN | 人脸修复 | `pip install gfpgan` |
| ComfyUI | 综合工作流平台 | GitHub 开源 |

### 3.3 桌面应用

| 应用 | 功能 | 价格 |
|------|------|------|
| Adobe Photoshop | 生成式填充，全套编辑 | $23/月 |
| Topaz Photo AI | 放大、去噪、锐化 | $199 |
| Affinity Photo | 手动编辑，AI 插件 | $70 |
| GIMP + 插件 | 免费，可扩展 | 免费 |

---

## 四、工作流原则

### 4.1 核心原则

1. **非破坏性编辑** - 始终保留原始文件
2. **分层处理** - 顺序组合多个编辑操作
3. **分辨率匹配** - 在原始分辨率编辑，最后放大
4. **掩码精度** - 更好的掩码 = 更好的结果
5. **迭代优化** - 必要时多次调整

### 4.2 掩码基础

- **白色** = 编辑区域
- **黑色** = 保留区域
- **灰色** = 部分混合（羽化）

**掩码创建方法：**
- 编辑器手动绘制
- SAM（Segment Anything）自动选择
- 颜色/亮度键控
- 边缘检测

### 4.3 典型工作流

#### 对象移除流程
1. 创建掩码覆盖不需要的对象
2. 运行 Inpainting（可选提供上下文提示）
3. 必要时融合边缘
4. 修饰伪影

#### 背景替换流程
1. 移除背景（获取透明 PNG）
2. 放置在新背景上
3. 匹配光照/颜色
4. 添加阴影增加真实感

#### 图像增强流程
1. 修复人脸（如有）
2. 去除伪影/噪点
3. 色彩校正
4. 超分辨率放大至最终分辨率

---

## 五、最佳实践

### 5.1 质量优化

- **羽化掩码** - 硬边缘看起来不自然
- **提供上下文提示** - 描述应该填充什么内容
- **多次迭代** - 大范围编辑可能需要多次细化
- **检查边缘** - 放大检查融合质量
- **匹配颗粒/噪点** - 添加胶片颗粒以匹配原图

### 5.2 性能优化

- **本地优先** - 免费且无速率限制
- **批量处理** - 使用脚本自动化重复任务
- **GPU 加速** - 大幅提升本地处理速度
- **选择合适的模型** - 不要过度使用大模型

### 5.3 成本控制

| 场景 | 推荐方案 | 成本 |
|------|---------|------|
| 少量高质量编辑 | 云端 API（DALL-E, remove.bg）| 按量计费 |
| 大量批量处理 | 本地工具（rembg, Real-ESRGAN）| 免费 |
| 复杂工作流 | ComfyUI + 本地模型 | 免费 |
| 商业生产 | Photoshop + Topaz | 订阅/买断 |

---

## 六、与其他 Skill 对比

| Skill | 功能 | 评分 | 定位 |
|-------|------|------|------|
| **image-edit** | 综合图像编辑 | 3.158 | 全能型图像编辑 |
| openai-image-gen | AI 图像生成 | - | 仅生成，不编辑 |
| gemini | 多模态理解 | - | 图像理解，非编辑 |
| imagemagick | 传统图像处理 | 0.972 | 命令行批处理 |

**image-edit 的优势：**
- 覆盖图像编辑的所有核心需求
- 提供云端和本地两种方案
- 详细的最佳实践指导
- 完整的工具生态

---

## 七、应用场景

### 7.1 个人用户

- **照片美化** - 移除背景人物、修复老照片
- **社交媒体** - 制作透明背景头像、扩展图片比例
- **艺术创作** - 风格迁移、创意编辑

### 7.2 商业应用

- **电商** - 产品图片背景移除、统一风格
- **营销** - 批量处理广告素材
- **印刷** - 低分辨率图片放大至印刷品质

### 7.3 开发者

- **数据增强** - 批量生成训练数据
- **图像预处理** - 为 AI 模型准备输入
- **自动化工作流** - 集成到 CI/CD 流程

---

## 八、代码示例

### 8.1 完整图像编辑流程

```python
from PIL import Image
from rembg import remove
from realesrgan import RealESRGAN
import torch

# 1. 加载图像
img = Image.open("photo.jpg")

# 2. 移除背景
no_bg = remove(img)
no_bg.save("no_background.png")

# 3. 放大（如果需要）
model = RealESRGAN(torch.device("cuda"), scale=4)
model.load_weights("weights/RealESRGAN_x4plus.pth")
upscaled = model.predict(no_bg)

# 4. 保存结果
upscaled.save("final_output.png")
```

### 8.2 批量背景移除

```python
from rembg import remove
from PIL import Image
from pathlib import Path
from concurrent.futures import ThreadPoolExecutor
import tqdm

def process_image(img_path):
    """处理单张图片"""
    output = remove(Image.open(img_path))
    output.save(f"output/{img_path.stem}.png")
    return img_path.name

# 批量处理
input_dir = Path("input/")
images = list(input_dir.glob("*.jpg"))

with ThreadPoolExecutor(max_workers=4) as executor:
    results = list(tqdm.tqdm(
        executor.map(process_image, images),
        total=len(images)
    ))

print(f"Processed {len(results)} images")
```

### 8.3 人脸修复 + 放大

```python
from gfpgan import GFPGANer
from realesrgan import RealESRGAN
import torch
import cv2

# 1. 加载图像
img = cv2.imread("old_photo.jpg", cv2.IMREAD_COLOR)

# 2. 人脸修复
restorer = GFPGANer(
    model_path="GFPGANv1.4.pth",
    upscale=2,
    arch="clean",
    channel_multiplier=2
)
_, _, restored = restorer.enhance(img, paste_back=True)

# 3. 放大
model = RealESRGAN(torch.device("cuda"), scale=4)
model.load_weights("weights/RealESRGAN_x4plus.pth")
final = model.predict(restored)

# 4. 保存
cv2.imwrite("restored_upscaled.jpg", final)
```

---

## 九、安装与配置

### 9.1 Skill 安装

```bash
clawhub install image-edit
```

安装位置：`/root/.openclaw/workspace/skills/image-edit/`

### 9.2 本地工具安装

```bash
# 背景移除
pip install rembg[gpu]

# 超分辨率
pip install realesrgan

# 人脸修复
pip install gfpgan

# Inpainting
pip install iopaint

# Stable Diffusion（可选）
pip install diffusers transformers accelerate
```

### 9.3 API 密钥配置

```bash
# OpenAI
export OPENAI_API_KEY="sk-..."

# Stability AI
export STABILITY_API_KEY="..."

# remove.bg
export REMOVEBG_API_KEY="..."

# ClipDrop
export CLIPDROP_API_KEY="..."
```

---

## 十、已知限制

| 限制 | 说明 | 解决方案 |
|------|------|---------|
| 云端 API 成本 | 按量计费，大量使用成本高 | 优先使用本地工具 |
| GPU 要求 | 本地工具需要 GPU 加速 | 使用云端 API 或 CPU 模式 |
| 掩码精度 | 自动掩码可能不完美 | 手动修整或使用 SAM |
| 风格迁移细节丢失 | 高强度可能丢失细节 | 降低 strength 参数 |
| 放大伪影 | AI 可能添加奇怪纹理 | 选择合适模型，后处理修饰 |

---

## 十一、总结

### 优势

✅ **功能全面** - 覆盖图像编辑六大核心领域  
✅ **工具丰富** - 云端 API + 本地工具 + 桌面应用  
✅ **文档详尽** - 每个技术都有独立的详细文档  
✅ **最佳实践** - 提供完整的质量优化指南  
✅ **代码示例** - Python 示例可直接使用  
✅ **成本可控** - 本地工具完全免费

### 劣势

⚠️ **学习曲线** - 需要了解多种工具和技术  
⚠️ **GPU 依赖** - 本地高性能处理需要 GPU  
⚠️ **掩码制作** - 高质量掩码需要手动操作  
⚠️ **API 成本** - 云端服务按量计费

### 推荐度

**⭐⭐⭐⭐⭐ (5/5)**

这是一个生产级的图像编辑 Skill，适合：
- 需要综合图像编辑能力的用户
- 希望了解 AI 图像编辑最佳实践的开发者
- 寻找免费本地工具替代云端 API 的用户

---

**分析日期:** 2026-03-02  
**Skill 版本:** Latest (ClawHub)  
**文档完整性:** ✅ 100% (6/6 文档)  
**代码示例:** ✅ Python, Bash  
**下一步:** 尝试集成到实际工作流，测试性能和质量

---

*愿每一张图片都焕然一新。* ✂️🔺
