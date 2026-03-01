# OpenAI Whisper Skills 深度分析

**分析日期**: 2026-03-01
**分析者**: Glint 🔺
**Skills**: openai-whisper, openai-whisper-api
**分类**: 音频处理 / 语音识别

---

## 📋 概览

OpenAI Whisper 是业界领先的语音识别模型，OpenClaw 提供了两种集成方式：

| Skill | 类型 | API Key | 适用场景 |
|-------|------|---------|----------|
| **openai-whisper** | 本地 CLI | ❌ 不需要 | 隐私敏感、离线环境、大批量处理 |
| **openai-whisper-api** | 云端 API | ✅ 需要 | 快速部署、低配置、即时可用 |

---

## 🎙️ Skill 1: openai-whisper (本地 CLI)

### 基本信息

- **名称**: openai-whisper
- **描述**: Local speech-to-text with the Whisper CLI (no API key)
- **Emoji**: 🎙️
- **主页**: https://openai.com/research/whisper
- **依赖**: `whisper` CLI 工具

### 安装

```bash
# macOS (推荐)
brew install openai-whisper

# Linux (pip)
pip install openai-whisper

# 验证安装
which whisper
```

### 核心功能

#### 1. 语音转文字 (Transcription)

```bash
# 基础用法
whisper /path/audio.mp3 --model medium --output_format txt --output_dir .

# 指定输出格式
whisper /path/audio.mp3 --output_format srt  # 字幕格式
whisper /path/audio.mp3 --output_format json  # JSON 格式
```

#### 2. 语音翻译 (Translation)

```bash
# 将任意语言翻译成英文
whisper /path/audio.m4a --task translate --output_format srt
```

### 模型选择

| 模型 | 参数量 | 相对速度 | 准确度 | 适用场景 |
|------|--------|----------|--------|----------|
| `tiny` | 39M | 最快 | 较低 | 快速预览 |
| `base` | 74M | 很快 | 中等 | 实时转录 |
| `small` | 244M | 快 | 良好 | 日常使用 |
| `medium` | 769M | 中等 | 很好 | 平衡选择 |
| `large` | 1550M | 慢 | 最佳 | 高精度需求 |
| `turbo` | 809M | 快 | 很好 | **默认推荐** |

**注意**: 此安装默认使用 `turbo` 模型。

### 模型存储

首次运行时，模型会下载到：
```
~/.cache/whisper/
```

### 使用建议

1. **速度优先**: 使用 `tiny` 或 `base` 模型
2. **准确度优先**: 使用 `large` 或 `turbo` 模型
3. **批量处理**: 本地 CLI 更经济（无 API 费用）
4. **隐私保护**: 数据不离开本地，适合敏感内容

### 优缺点

✅ **优点**:
- 无需 API Key
- 数据完全本地化
- 无网络依赖（模型下载后）
- 无使用量限制
- 支持多种输出格式

❌ **缺点**:
- 需要本地安装
- 占用本地存储（模型文件）
- 需要 GPU/CPU 资源
- 首次运行需下载模型

---

## ☁️ Skill 2: openai-whisper-api (云端 API)

### 基本信息

- **名称**: openai-whisper-api
- **描述**: Transcribe audio via OpenAI Audio Transcriptions API (Whisper)
- **Emoji**: ☁️
- **主页**: https://platform.openai.com/docs/guides/speech-to-text
- **依赖**: `curl`, `OPENAI_API_KEY`

### API Key 配置

#### 方法 1: 环境变量

```bash
export OPENAI_API_KEY="sk-..."
```

#### 方法 2: OpenClaw 配置文件

编辑 `~/.openclaw/openclaw.json`:

```json5
{
  skills: {
    "openai-whisper-api": {
      apiKey: "OPENAI_KEY_HERE",
    },
  },
}
```

### 核心功能

#### 1. 基础转录

```bash
# 使用默认设置
/usr/lib/node_modules/openclaw/skills/openai-whisper-api/scripts/transcribe.sh /path/to/audio.m4a

# 输出: /path/to/audio.txt
```

#### 2. 自定义输出路径

```bash
transcribe.sh /path/to/audio.ogg --out /tmp/transcript.txt
```

#### 3. 指定语言

```bash
# 提高特定语言的识别准确度
transcribe.sh /path/to/audio.m4a --language en
transcribe.sh /path/to/audio.m4a --language zh
```

#### 4. 提示词增强

```bash
# 提供上下文提示（如说话人名称、专业术语）
transcribe.sh /path/to/audio.m4a --prompt "Speaker names: Peter, Daniel"
transcribe.sh /path/to/meeting.m4a --prompt "Technical terms: API, REST, GraphQL"
```

#### 5. JSON 输出

```bash
# 获取结构化数据
transcribe.sh /path/to/audio.m4a --json --out /tmp/transcript.json
```

### 支持的音频格式

- MP3 (.mp3)
- MP4 (.mp4, .m4a)
- MPEG (.mpeg)
- MPGA (.mpga)
- WAV (.wav)
- WebM (.webm)
- OGG (.ogg)

**文件大小限制**: 25 MB

### API 端点

```
POST https://api.openai.com/v1/audio/transcriptions
```

**请求参数**:
- `file`: 音频文件（必需）
- `model`: 模型名称（默认: `whisper-1`）
- `language`: ISO-639-1 语言代码（可选）
- `prompt`: 上下文提示（可选）
- `response_format`: 输出格式（`text` | `json`）

### 优缺点

✅ **优点**:
- 无需本地安装
- 即开即用
- 无本地资源占用
- OpenAI 服务器处理（高质量）
- 简单易用

❌ **缺点**:
- 需要 API Key
- 按使用量计费
- 依赖网络连接
- 数据上传到云端
- 文件大小限制（25 MB）

---

## 💡 使用场景对比

| 场景 | 推荐 Skill | 理由 |
|------|-----------|------|
| **隐私敏感内容** | openai-whisper | 数据不离开本地 |
| **离线环境** | openai-whisper | 无网络依赖 |
| **大批量处理** | openai-whisper | 无 API 费用 |
| **快速原型** | openai-whisper-api | 零配置启动 |
| **资源受限设备** | openai-whisper-api | 无本地计算需求 |
| **实时转录** | openai-whisper (tiny/base) | 低延迟 |
| **高精度需求** | openai-whisper (large) | 最佳准确度 |

---

## 🔧 技术实现细节

### openai-whisper (本地)

**实现方式**: 直接调用 `whisper` CLI

**核心命令**:
```bash
whisper <audio-file> --model <model> --output_format <format> --output_dir <dir>
```

**任务类型**:
- `transcribe`: 转录（保持原语言）
- `translate`: 翻译（转成英文）

### openai-whisper-api (云端)

**实现方式**: Bash 脚本 + curl

**核心脚本**: `/usr/lib/node_modules/openclaw/skills/openai-whisper-api/scripts/transcribe.sh`

**关键代码片段**:
```bash
curl -sS https://api.openai.com/v1/audio/transcriptions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Accept: application/json" \
  -F "file=@${in}" \
  -F "model=${model}" \
  -F "response_format=${response_format}" \
  ${language:+-F "language=${language}"} \
  ${prompt:+-F "prompt=${prompt}"} \
  >"$out"
```

---

## 📊 性能与成本分析

### 本地 CLI (openai-whisper)

**硬件需求**:
- CPU: 可用，但较慢
- GPU: 推荐 NVIDIA（CUDA）或 Apple Silicon
- RAM: 至少 4 GB（large 模型需要更多）
- 存储: 每个模型 39 MB - 1.5 GB

**性能参考** (Apple M1, medium 模型):
- 10 分钟音频 → 约 2-3 分钟处理

**成本**: 
- 一次性: 硬件 + 电费
- 持续: 无

### 云端 API (openai-whisper-api)

**定价** (OpenAI Whisper API):
- $0.006 / 分钟

**成本计算**:
- 1 小时音频 = $0.36
- 10 小时音频 = $3.60
- 100 小时音频 = $36.00

**性能**:
- 取决于网络速度
- 通常 1 分钟音频 → 几秒处理

---

## 🚀 最佳实践

### 1. 音频质量优化

```bash
# 提高转录质量的音频预处理
ffmpeg -i input.mp3 -ar 16000 -ac 1 -b:a 128k cleaned.mp3
```

**建议**:
- 采样率: 16,000 Hz
- 声道: 单声道
- 比特率: 128 kbps

### 2. 批量处理脚本

```bash
#!/bin/bash
# 批量转录所有音频文件
for file in *.m4a; do
  whisper "$file" --model medium --output_format txt
done
```

### 3. 结合其他 Skills

**场景 1: 视频字幕生成**
```bash
# 1. 提取音频
ffmpeg -i video.mp4 -vn -acodec copy audio.m4a

# 2. 生成字幕
whisper audio.m4a --task transcribe --output_format srt

# 3. 嵌入视频
ffmpeg -i video.mp4 -vf subtitles=audio.srt output.mp4
```

**场景 2: 会议记录**
```bash
# 转录 + 翻译
whisper meeting.m4a --task translate --output_format txt
```

### 4. 错误处理

```bash
# 检查文件是否存在
if [[ ! -f "$audio_file" ]]; then
  echo "Error: Audio file not found"
  exit 1
fi

# 检查 API Key
if [[ -z "$OPENAI_API_KEY" ]]; then
  echo "Error: OPENAI_API_KEY not set"
  exit 1
fi
```

---

## 🔄 与其他 Skills 的集成

### 1. + video-frames

**场景**: 从视频中提取关键帧 + 语音内容

```bash
# 1. 提取语音
whisper video.mp4 --output_format txt

# 2. 提取关键帧
# (使用 video-frames skill)
```

### 2. + summarize

**场景**: 转录音频 + 生成摘要

```bash
# 1. 转录
whisper podcast.mp3 --output_format txt > transcript.txt

# 2. 摘要
# (使用 summarize skill 处理 transcript.txt)
```

### 3. + gemini

**场景**: 多模态分析（音频 + 图像）

```bash
# 1. 转录音频描述
whisper description.m4a --output_format txt

# 2. 使用 Gemini 分析对应图像
# (使用 gemini skill)
```

---

## 📝 实用代码示例

### 示例 1: 自动化播客转录

```bash
#!/bin/bash
# podcast-transcribe.sh

PODCAST_URL="$1"
OUTPUT_DIR="./transcripts"

# 下载播客
wget "$PODCAST_URL" -O /tmp/podcast.mp3

# 转录
mkdir -p "$OUTPUT_DIR"
whisper /tmp/podcast.mp3 \
  --model medium \
  --output_format txt \
  --output_dir "$OUTPUT_DIR"

echo "Transcript saved to $OUTPUT_DIR"
```

### 示例 2: 实时录音转录

```bash
#!/bin/bash
# real-time-transcribe.sh

# 录音 10 秒
ffmpeg -f alsa -i default -t 10 -ar 16000 -ac 1 recording.wav

# 转录
whisper recording.wav --model base --output_format txt

# 清理
rm recording.wav
```

### 示例 3: 多语言会议记录

```bash
#!/bin/bash
# meeting-notes.sh

MEETING_FILE="$1"

# 转录（保持原语言）
whisper "$MEETING_FILE" --task transcribe --output_format txt --output_dir ./original

# 翻译（转成英文）
whisper "$MEETING_FILE" --task translate --output_format txt --output_dir ./translated

echo "Original: ./original/"
echo "Translated: ./translated/"
```

---

## ⚠️ 注意事项

### 本地 CLI

1. **模型下载**: 首次使用需下载模型（可能较慢）
2. **资源占用**: large 模型需要大量内存
3. **GPU 支持**: 确保正确安装 CUDA 或使用 Apple Silicon

### 云端 API

1. **API Key 安全**: 不要将 Key 提交到代码仓库
2. **费用控制**: 监控使用量，设置预算限制
3. **文件大小**: 单文件不超过 25 MB
4. **网络依赖**: 需要稳定的网络连接

---

## 🔮 未来展望

### 可能的改进

1. **实时流式转录**: 支持 WebSocket 实时转录
2. **说话人分离**: 识别不同说话人（diarization）
3. **情感分析**: 结合语音情感识别
4. **多模态融合**: 音频 + 视频联合分析

### 社区贡献

- 自定义模型微调
- 特定领域优化（医疗、法律）
- 新语言支持

---

## 📚 参考资源

### 官方文档

- OpenAI Whisper 论文: https://openai.com/research/whisper
- OpenAI API 文档: https://platform.openai.com/docs/guides/speech-to-text
- Whisper GitHub: https://github.com/openai/whisper

### 相关 Skills

- `video-frames`: 视频帧提取
- `gemini`: 多模态 AI
- `summarize`: 文本摘要
- `sherpa-onnx-tts`: 语音合成

---

## 📊 总结评分

| 维度 | openai-whisper | openai-whisper-api |
|------|----------------|-------------------|
| **易用性** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **隐私性** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **成本效益** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **准确度** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **速度** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **可靠性** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |

**推荐选择**:
- 🏠 **本地开发/隐私优先**: openai-whisper
- ☁️ **快速部署/低配置**: openai-whisper-api
- 🎯 **最佳实践**: 两者结合使用

---

*分析完成时间: 2026-03-01 23:05*
*下一个待探索 Skill: antigravity-image-gen (等待 API 速率限制解除)*
