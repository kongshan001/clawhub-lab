# sherpa-onnx-tts Skill 深度分析

**分析日期**: 2026-03-02
**Skill 类型**: OpenClaw 内置 Skill
**功能定位**: 本地离线文本转语音 (TTS)
**Emoji**: 🗣️

---

## 概述

`sherpa-onnx-tts` 是一个完全离线运行的文本转语音解决方案，基于 [sherpa-onnx](https://github.com/k2-fsa/sherpa-onnx) 项目。它使用 ONNX Runtime 进行推理，无需云服务或网络连接，支持多种平台和语音模型。

### 核心特点

| 特性 | 描述 |
|------|------|
| **完全离线** | 无需 API Key，无需网络，隐私安全 |
| **跨平台** | 支持 macOS, Linux, Windows |
| **多语言** | 支持英语、中文等多种语音模型 |
| **高性能** | 基于 ONNX Runtime，支持 NPU 加速 |
| **轻量级** | 模型可按需下载，无云端依赖 |

---

## 安装配置

### 1. 下载运行时

根据操作系统下载对应的 sherpa-onnx 运行时：

| 平台 | 下载链接 |
|------|----------|
| macOS | `sherpa-onnx-v1.12.23-osx-universal2-shared.tar.bz2` |
| Linux x64 | `sherpa-onnx-v1.12.23-linux-x64-shared.tar.bz2` |
| Windows x64 | `sherpa-onnx-v1.12.23-win-x64-shared.tar.bz2` |

解压到 `~/.openclaw/tools/sherpa-onnx-tts/runtime`

### 2. 下载语音模型

默认推荐 Piper 英语模型：
```
vits-piper-en_US-lessac-high.tar.bz2
```

解压到 `~/.openclaw/tools/sherpa-onnx-tts/models`

### 3. 配置环境变量

在 `~/.openclaw/openclaw.json` 中添加：

```json5
{
  skills: {
    entries: {
      "sherpa-onnx-tts": {
        env: {
          SHERPA_ONNX_RUNTIME_DIR: "~/.openclaw/tools/sherpa-onnx-tts/runtime",
          SHERPA_ONNX_MODEL_DIR: "~/.openclaw/tools/sherpa-onnx-tts/models/vits-piper-en_US-lessac-high",
        },
      },
    },
  },
}
```

---

## 使用方法

### 基本用法

```bash
# 直接运行
/usr/lib/node_modules/openclaw/skills/sherpa-onnx-tts/bin/sherpa-onnx-tts \
  -o ./output.wav "Hello from local TTS."

# 或添加到 PATH
export PATH="/usr/lib/node_modules/openclaw/skills/sherpa-onnx-tts/bin:$PATH"
sherpa-onnx-tts -o ./tts.wav "This is a test."
```

### 命令行参数

| 参数 | 说明 |
|------|------|
| `-o, --output <file>` | 输出 WAV 文件路径 (默认: tts.wav) |
| `--runtime-dir <dir>` | 覆盖运行时目录 |
| `--model-dir <dir>` | 覆盖模型目录 |
| `--model-file <file>` | 指定 ONNX 模型文件 |
| `--tokens-file <file>` | 指定 tokens 文件 |
| `--data-dir <dir>` | 指定 espeak-ng-data 目录 |
| `--text <text>` | 要转换的文本 |
| `<text>` | 位置参数形式的文本 |

### 高级用法

```bash
# 使用不同的模型
sherpa-onnx-tts \
  --model-dir /path/to/chinese-model \
  -o chinese.wav "你好世界"

# 覆盖特定文件
sherpa-onnx-tts \
  --model-file /custom/model.onnx \
  --tokens-file /custom/tokens.txt \
  -o custom.wav "Custom model test"
```

---

## 可用语音模型

### 英语模型

| 模型 | 描述 |
|------|------|
| `vits-piper-en_US-lessac-high` | 美式英语，高质量 (默认) |
| `vits-piper-en_US-amy-low` | 美式英语，低资源 |
| `vits-piper-en_GB-alba-medium` | 英式英语 |

### 中文模型

| 模型 | 描述 |
|------|------|
| `vits-icefall-zh-aishell3` | 中文标准语音 |
| `vits-piper-zh_CN-huayan-medium` | 中文普通话 |

### 多语言模型

sherpa-onnx 支持超过 40 种语言的 Piper 模型，包括：
- 欧洲语言：德语、法语、西班牙语、意大利语
- 亚洲语言：中文、日语、韩语、印地语
- 其他：阿拉伯语、俄语、葡萄牙语

完整列表：https://github.com/k2-fsa/sherpa-onnx/releases/tag/tts-models

---

## 技术架构

### Wrapper 脚本分析

```javascript
// 核心流程
1. 解析命令行参数
2. 解析运行时目录 (SHERPA_ONNX_RUNTIME_DIR)
3. 解析模型目录 (SHERPA_ONNX_MODEL_DIR)
4. 自动检测模型文件 (.onnx)
5. 自动检测 tokens 文件 (tokens.txt)
6. 自动检测 data 目录 (espeak-ng-data)
7. 设置库路径 (LD_LIBRARY_PATH / DYLD_LIBRARY_PATH)
8. 调用 sherpa-onnx-offline-tts 二进制
```

### 依赖结构

```
sherpa-onnx-tts/
├── bin/
│   └── sherpa-onnx-tts       # Node.js wrapper 脚本
└── SKILL.md                  # 文档

~/.openclaw/tools/sherpa-onnx-tts/
├── runtime/                  # sherpa-onnx 运行时
│   ├── bin/
│   │   └── sherpa-onnx-offline-tts
│   └── lib/
│       └── libonnxruntime.*
└── models/
    └── vits-piper-en_US-lessac-high/
        ├── *.onnx            # 模型文件
        ├── tokens.txt        # 词汇表
        └── espeak-ng-data/   # 发音数据
```

---

## 与 Whisper Skill 对比

| 特性 | sherpa-onnx-tts | openai-whisper |
|------|-----------------|----------------|
| **功能** | 文本转语音 (TTS) | 语音转文本 (ASR) |
| **运行方式** | 完全离线 | 本地 GPU/CPU |
| **API Key** | 不需要 | 不需要 |
| **模型来源** | sherpa-onnx | OpenAI Whisper |
| **语言支持** | 40+ 语言 | 99+ 语言 |
| **实时性** | 实时生成 | 实时转写 |

两者组合可构建完整的离线语音交互系统。

---

## 应用场景

### 1. 离线语音助手

```bash
# 生成语音回复
sherpa-onnx-tts -o response.wav "I understand your request."
# 播放
aplay response.wav  # Linux
afplay response.wav # macOS
```

### 2. 无障碍辅助

```bash
# 阅读文本文件
cat article.txt | sherpa-onnx-tts -o article.wav
```

### 3. 自动化通知

```bash
# 在脚本中生成提醒语音
sherpa-onnx-tts -o alert.wav "Build completed successfully."
```

### 4. 批量音频生成

```bash
# 为多个文本生成音频
for text in "Hello" "Goodbye" "Welcome"; do
  sherpa-onnx-tts -o "${text,,}.wav" "$text"
done
```

---

## 性能特点

### 资源需求

| 指标 | 值 |
|------|-----|
| **内存** | ~100-300MB (取决于模型) |
| **CPU** | 实时或更快 |
| **磁盘** | 50-200MB 每个模型 |
| **GPU** | 可选，支持 NPU 加速 |

### 支持的硬件加速

- **NVIDIA Jetson**: CPU + GPU
- **Rockchip NPU (RKNN)**: 专用 NPU
- **Qualcomm NPU (QNN)**: 移动端 NPU
- **Ascend NPU**: 华为昇腾

---

## 与云端 TTS 对比

| 特性 | sherpa-onnx-tts | 云端 TTS |
|------|-----------------|----------|
| **网络依赖** | 无 | 需要 |
| **延迟** | 低（本地） | 高（网络） |
| **成本** | 免费 | 按量计费 |
| **隐私** | 完全本地 | 数据上传 |
| **质量** | 良好 | 优秀 |
| **声音数量** | 40+ | 数百 |
| **自定义** | 支持训练 | 部分支持 |

---

## 常见问题

### Q: 如何更换语音？

下载不同的模型并更新 `SHERPA_ONNX_MODEL_DIR`：

```bash
# 下载中文模型
wget https://github.com/k2-fsa/sherpa-onnx/releases/download/tts-models/vits-icefall-zh-aishell3.tar.bz2
tar -xf vits-icefall-zh-aishell3.tar.bz2 -C ~/.openclaw/tools/sherpa-onnx-tts/models/
```

### Q: 为什么找不到二进制文件？

确保已下载运行时并正确配置 `SHERPA_ONNX_RUNTIME_DIR`。

### Q: 如何在代码中调用？

```javascript
const { execSync } = require('child_process');
const path = require('path');

const ttsPath = '/usr/lib/node_modules/openclaw/skills/sherpa-onnx-tts/bin/sherpa-onnx-tts';
execSync(`${ttsPath} -o output.wav "Hello from code."`);
```

---

## 最佳实践

1. **模型选择**: 根据语言和质量需求选择合适的模型
2. **资源管理**: 不需要的模型可以删除以节省空间
3. **批量处理**: 对于大量文本，考虑分批生成
4. **格式转换**: 输出为 WAV，可用 ffmpeg 转换为其他格式

---

## 相关资源

- **sherpa-onnx GitHub**: https://github.com/k2-fsa/sherpa-onnx
- **TTS 模型列表**: https://github.com/k2-fsa/sherpa-onnx/releases/tag/tts-models
- **在线演示**: https://huggingface.co/spaces/k2-fsa/text-to-speech
- **文档**: https://k2-fsa.github.io/sherpa/onnx/tts/all-in-one.html

---

## 总结

| 维度 | 评分 | 说明 |
|------|------|------|
| **易用性** | ⭐⭐⭐⭐ | 配置简单，命令行友好 |
| **功能性** | ⭐⭐⭐⭐ | 支持多语言，质量良好 |
| **隐私性** | ⭐⭐⭐⭐⭐ | 完全离线，无数据泄露风险 |
| **性能** | ⭐⭐⭐⭐ | 本地推理，延迟低 |
| **可扩展** | ⭐⭐⭐⭐ | 支持自定义模型 |

**推荐场景**:
- 隐私敏感的语音应用
- 离线环境下的 TTS 需求
- 与 Whisper 组合构建语音交互系统
- 嵌入式设备和边缘计算

---

*分析者: Glint 🔺 | ClawHub Lab*
