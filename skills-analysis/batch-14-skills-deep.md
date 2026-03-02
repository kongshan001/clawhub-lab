# 批量深度分析：14 个内置 Skills

> 分析日期：2026-03-02  
> 分析者：Glint 🔺

## 概览

本次批量分析涵盖 OpenClaw 内置 Skills 中剩余未分析的 **14 个 Skills**，涵盖笔记管理、智能家居、音视频处理、社交平台等类别。

---

## 1. bear-notes 🐻

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | bear-notes |
| 描述 | 通过 grizzly CLI 创建、搜索和管理 Bear 笔记 |
| 主页 | https://bear.app |
| 平台 | macOS |
| 依赖 | grizzly CLI |

### 核心功能
- **创建笔记**：`echo "content" | grizzly create --title "Title" --tag work`
- **读取笔记**：`grizzly open-note --id "NOTE_ID" --enable-callback --json`
- **追加内容**：`grizzly add-text --id "NOTE_ID" --mode append`
- **标签管理**：`grizzly tags --enable-callback --json`
- **搜索笔记**：`grizzly open-tag --name "work"`

### 认证配置
```bash
# 获取 Bear Token
# Bear → Help → API Token → Copy Token
echo "YOUR_TOKEN" > ~/.config/grizzly/token
```

### 配置文件
```toml
# ~/.config/grizzly/config.toml
token_file = "~/.config/grizzly/token"
callback_url = "http://127.0.0.1:42123/success"
timeout = "5s"
```

### 使用场景
- 自动创建会议笔记
- 批量整理笔记标签
- 与其他工具集成（如 OCR 结果存入 Bear）

### 限制
- 仅支持 macOS
- Bear 应用必须运行中
- 部分操作需要 API Token

---

## 2. blogwatcher 📰

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | blogwatcher |
| 描述 | 监控博客和 RSS/Atom 订阅更新 |
| 主页 | https://github.com/Hyaxia/blogwatcher |
| 依赖 | blogwatcher CLI (Go) |

### 核心功能
```bash
# 添加博客
blogwatcher add "My Blog" https://example.com

# 列出所有博客
blogwatcher blogs

# 扫描更新
blogwatcher scan

# 列出文章
blogwatcher articles

# 标记已读
blogwatcher read 1
blogwatcher read-all

# 移除博客
blogwatcher remove "My Blog"
```

### 使用场景
- 定时检查技术博客更新
- 追踪行业动态
- 构建个人阅读聚合器

### 输出示例
```
$ blogwatcher scan
Scanning 1 blog(s)...
  xkcd
    Source: RSS | Found: 4 | New: 4
Found 4 new article(s) total!
```

---

## 3. blucli 🫐

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | blucli |
| 描述 | Bluesound/NAD 音频播放器 CLI 控制 |
| 主页 | https://blucli.sh |
| 依赖 | blu CLI (Go) |

### 核心功能
```bash
# 发现设备
blu devices

# 查看状态
blu --device <id> status

# 播放控制
blu play|pause|stop

# 音量控制
blu volume set 15

# 分组
blu group status|add|remove

# TuneIn 广播
blu tunein search "query"
blu tunein play "query"
```

### 设备选择优先级
1. `--device <id|name|alias>`
2. `BLU_DEVICE` 环境变量
3. 配置文件默认值

### 使用场景
- 多房间音频控制
- 定时播放/暂停
- 与智能家居场景联动

---

## 4. camsnap 📸

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | camsnap |
| 描述 | 从 RTSP/ONVIF 摄像头捕获帧和视频 |
| 主页 | https://camsnap.ai |
| 依赖 | camsnap CLI, ffmpeg |

### 核心功能
```bash
# 添加摄像头
camsnap add --name kitchen --host 192.168.0.10 --user user --pass pass

# 发现设备
camsnap discover --info

# 快照
camsnap snap kitchen --out shot.jpg

# 录制视频
camsnap clip kitchen --dur 5s --out clip.mp4

# 动作检测
camsnap watch kitchen --threshold 0.2 --action '...'

# 诊断
camsnap doctor --probe
```

### 配置文件
- 位置：`~/.config/camsnap/config.yaml`

### 使用场景
- 家庭安防监控
- 自动截图记录
- 动作触发通知

### 限制
- 需要 ffmpeg
- 仅支持 RTSP/ONVIF 协议

---

## 5. eightctl 🎛️

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | eightctl |
| 描述 | Eight Sleep 智能床垫控制 |
| 主页 | https://eightctl.sh |
| 依赖 | eightctl CLI (Go) |

### 核心功能
```bash
# 状态查看
eightctl status

# 开关
eightctl on|off

# 温度设置
eightctl temp 20

# 闹钟
eightctl alarm list|create|dismiss

# 日程
eightctl schedule list|create|update

# 音频
eightctl audio state|play|pause

# 床基角度
eightctl base info|angle
```

### 认证
- 配置：`~/.config/eightctl/config.yaml`
- 环境变量：`EIGHTCTL_EMAIL`, `EIGHTCTL_PASSWORD`

### 使用场景
- 睡前自动调温
- 与智能闹钟联动
- 睡眠日程自动化

### 限制
- 使用非官方 API，有速率限制
- 避免频繁登录

---

## 6. gifgrep 🧲

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | gifgrep |
| 描述 | GIF 搜索、预览、下载和提取 |
| 主页 | https://gifgrep.com |
| 依赖 | gifgrep CLI |

### 核心功能
```bash
# 搜索 GIF
gifgrep cats --max 5

# URL 输出
gifgrep cats --format url | head -n 5

# JSON 输出
gifgrep search --json cats | jq '.[0].url'

# TUI 交互
gifgrep tui "office handshake"

# 下载
gifgrep cats --download --max 1 --format url

# 提取静态帧
gifgrep still ./clip.gif --at 1.5s -o still.png

# 生成帧表格
gifgrep sheet ./clip.gif --frames 9 --cols 3 -o sheet.png
```

### GIF 提供商
- `--source auto|tenor|giphy`
- Giphy 需要 `GIPHY_API_KEY`
- Tenor 可选 `TENOR_API_KEY`

### 输出格式
- `--json`：JSON 数组
- `--format url`：纯 URL 列表

### 使用场景
- 快速获取表情包
- 文档插图
- PR/Issue 动图展示

---

## 7. nano-pdf 📄

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | nano-pdf |
| 描述 | 自然语言编辑 PDF |
| 主页 | https://pypi.org/project/nano-pdf/ |
| 依赖 | nano-pdf CLI (Python/uv) |

### 核心功能
```bash
# 自然语言编辑
nano-pdf edit deck.pdf 1 "Change the title to 'Q3 Results' and fix the typo"
```

### 特点
- **自然语言指令**：无需手动编辑
- **智能识别**：自动定位文本位置
- **页码灵活**：支持 0/1 基索引

### 使用场景
- 快速修复 PDF 错误
- 批量更新模板
- 自动化文档处理

### 注意事项
- 页码可能因版本而异
- 编辑后建议检查输出

---

## 8. openhue 💡

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | openhue |
| 描述 | Philips Hue 灯光控制 CLI |
| 主页 | https://www.openhue.io/cli |
| 依赖 | openhue CLI |

### 核心功能
```bash
# 列出资源
openhue get light
openhue get room
openhue get scene

# 开关控制
openhue set light "Bedroom Lamp" --on
openhue set light "Bedroom Lamp" --off

# 亮度 (0-100)
openhue set light "Bedroom Lamp" --on --brightness 50

# 色温 (153-500 mirek)
openhue set light "Bedroom Lamp" --on --temperature 300

# 颜色
openhue set light "Bedroom Lamp" --on --color red
openhue set light "Bedroom Lamp" --on --rgb "#FF5500"

# 房间控制
openhue set room "Bedroom" --off
openhue set room "Bedroom" --on --brightness 30

# 场景
openhue set scene "Relax" --room "Bedroom"
```

### 快捷预设
```bash
# 睡前模式
openhue set room "Bedroom" --on --brightness 20 --temperature 450

# 工作模式
openhue set room "Office" --on --brightness 100 --temperature 250

# 电影模式
openhue set room "Living Room" --on --brightness 10
```

### 使用场景
- 智能家居场景联动
- 定时灯光控制
- 日出/日落模拟

### 限制
- 仅支持 Philips Hue
- 需要局域网内 Hue Bridge

---

## 9. ordercli 🛵

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | ordercli |
| 描述 | Foodora 订单查询和管理 CLI |
| 主页 | https://ordercli.sh |
| 依赖 | ordercli CLI |

### 核心功能
```bash
# 国家设置
ordercli foodora countries
ordercli foodora config set --country AT

# 登录
ordercli foodora login --email you@example.com --password-stdin

# 活跃订单
ordercli foodora orders
ordercli foodora orders --watch

# 历史订单
ordercli foodora history --limit 20
ordercli foodora history show <orderCode>

# 重购
ordercli foodora reorder <orderCode>
ordercli foodora reorder <orderCode> --confirm
```

### Cloudflare 绕过
```bash
# 浏览器登录
ordercli foodora login --email you@example.com --password-stdin --browser

# 导入 Chrome cookies
ordercli foodora cookies chrome --profile "Default"

# Session 导入
ordercli foodora session chrome --url https://www.foodora.at/
```

### 使用场景
- 追踪外卖订单状态
- 快速重购常点餐品
- 分析消费习惯

### 限制
- 仅支持 Foodora（Deliveroo WIP）
- 可能触发 Cloudflare 保护

---

## 10. peekaboo 👀

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | peekaboo |
| 描述 | macOS 全功能 UI 自动化 CLI |
| 主页 | https://peekaboo.boo |
| 平台 | macOS |
| 依赖 | Screen Recording + Accessibility 权限 |

### 功能分类

#### 核心功能
- `bridge` - 检查 Peekaboo Bridge 连接
- `capture` - 实时捕获/视频帧提取
- `image` - 截图
- `list` - 列出应用/窗口/屏幕/菜单
- `permissions` - 检查权限状态

#### 交互功能
- `click` - 点击
- `drag` - 拖拽
- `hotkey` - 快捷键
- `move` - 移动光标
- `paste` - 粘贴
- `press` - 按键
- `scroll` - 滚动
- `swipe` - 滑动手势
- `type` - 输入文本

#### 系统功能
- `app` - 应用管理
- `clipboard` - 剪贴板
- `dialog` - 系统对话框
- `dock` - Dock 管理
- `menu` - 菜单操作
- `menubar` - 状态栏
- `space` - Spaces 管理
- `window` - 窗口管理

#### 视觉功能
- `see` - UI 映射和分析

### 典型工作流
```bash
# 1. 检查权限
peekaboo permissions

# 2. 查看目标
peekaboo see --app Safari --annotate --path /tmp/see.png

# 3. 点击元素
peekaboo click --on B3 --app Safari

# 4. 输入文本
peekaboo type "user@example.com" --app Safari
```

### 使用场景
- UI 自动化测试
- 重复任务自动化
- 无障碍辅助

---

## 11. sag 🗣️

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | sag |
| 描述 | ElevenLabs 文字转语音（macOS say 风格） |
| 主页 | https://sag.sh |
| 依赖 | sag CLI, ELEVENLABS_API_KEY |

### 核心功能
```bash
# 基础使用
sag "Hello there"

# 指定声音
sag speak -v "Roger" "Hello"

# 列出声音
sag voices

# 提示技巧
sag prompting

# 生成文件
sag -v Clawd -o /tmp/voice-reply.mp3 "Your message"
```

### 模型选择
| 模型 | 特点 |
|------|------|
| eleven_v3 | 默认，表现力强 |
| eleven_multilingual_v2 | 稳定，多语言 |
| eleven_flash_v2_5 | 快速响应 |

### 语音标签（v3）
- `[whispers]`, `[shouts]`, `[sings]`
- `[laughs]`, `[sighs]`, `[exhales]`
- `[sarcastic]`, `[curious]`, `[excited]`
- `[pause]`, `[short pause]`, `[long pause]`

### 使用场景
- 语音回复消息
- 播客旁白生成
- 语音通知

---

## 12. songsee 🌊

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | songsee |
| 描述 | 音频频谱和特征可视化 |
| 主页 | https://github.com/steipete/songsee |
| 依赖 | songsee CLI |

### 核心功能
```bash
# 基础频谱
songsee track.mp3

# 多面板
songsee track.mp3 --viz spectrogram,mel,chroma,hpss,selfsim,loudness,tempogram,mfcc,flux

# 时间切片
songsee track.mp3 --start 12.5 --duration 8 -o slice.jpg

# 标准输入
cat track.mp3 | songsee - --format png -o out.png
```

### 可视化类型
- `spectrogram` - 频谱图
- `mel` - Mel 频谱
- `chroma` - 色度特征
- `hpss` - 谐波/打击分离
- `selfsim` - 自相似矩阵
- `loudness` - 响度曲线
- `tempogram` - 节奏图
- `mfcc` - MFCC 特征
- `flux` - 频谱通量

### 输出控制
- `--style`：调色板（classic, magma, inferno, viridis, gray）
- `--width` / `--height`：输出尺寸
- `--min-freq` / `--max-freq`：频率范围

### 使用场景
- 音乐分析
- 音频质量检查
- 教学演示

---

## 13. wacli 📱

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | wacli |
| 描述 | WhatsApp 消息发送和历史同步 |
| 主页 | https://wacli.sh |
| 依赖 | wacli CLI |

### 核心功能
```bash
# 认证
wacli auth

# 同步
wacli sync --follow

# 查找聊天
wacli chats list --limit 20 --query "name"

# 搜索消息
wacli messages search "query" --chat <jid>
wacli messages search "invoice" --after 2025-01-01

# 历史回填
wacli history backfill --chat <jid> --requests 2

# 发送消息
wacli send text --to "+14155551212" --message "Hello!"
wacli send file --to "+14155551212" --file /path/agenda.pdf --caption "Agenda"
```

### JID 格式
- 私聊：`<number>@s.whatsapp.net`
- 群组：`<id>@g.us`

### 安全规则
- 必须明确指定收件人
- 发送前确认
- 模糊情况需询问

### 使用场景
- 自动发送提醒
- 批量消息通知
- 历史消息搜索

### 注意
- **不用于普通用户聊天**：OpenClaw 自动路由 WhatsApp 对话
- 仅用于向第三方发送消息

---

## 14. xurl 𝕏

### 基本信息
| 属性 | 值 |
|------|-----|
| 名称 | xurl |
| 描述 | X (Twitter) API 完整 CLI |
| 主页 | https://github.com/xdevplatform/xurl |
| 依赖 | xurl CLI |

### 核心功能

#### 发布
```bash
xurl post "Hello world!"
xurl reply POST_ID "Nice post!"
xurl quote POST_ID "My take"
xurl delete POST_ID
```

#### 阅读
```bash
xurl read POST_ID
xurl search "golang" -n 10
xurl whoami
xurl user @handle
xurl timeline -n 20
xurl mentions -n 10
```

#### 互动
```bash
xurl like POST_ID
xurl repost POST_ID
xurl bookmark POST_ID
xurl follow @handle
xurl block @handle
xurl mute @handle
```

#### 私信
```bash
xurl dm @handle "message"
xurl dms -n 10
```

#### 媒体
```bash
xurl media upload photo.jpg
xurl media status MEDIA_ID
xurl post "Check this" --media-id MEDIA_ID
```

### 认证
```bash
# 检查状态
xurl auth status

# OAuth2 登录
xurl auth oauth2

# 多应用切换
xurl auth default prod-app
xurl --app dev-app /2/users/me
```

### 安全规则
- **禁止**在 Agent 会话中使用 `--verbose`（可能泄露 Token）
- **禁止**在命令中直接传递 secrets
- **禁止**读取 `~/.xurl` 文件

### 原始 API 访问
```bash
# 任意 v2 端点
xurl /2/users/me
xurl -X POST /2/tweets -d '{"text":"Hello"}'
xurl -X DELETE /2/tweets/1234567890
```

---

## 分类总结

| 分类 | Skills |
|------|--------|
| 笔记管理 | bear-notes |
| 内容订阅 | blogwatcher |
| 音频控制 | blucli, sag, songsee |
| 智能家居 | camsnap, eightctl, openhue |
| 图像/GIF | gifgrep |
| 文档处理 | nano-pdf |
| 生活服务 | ordercli |
| UI 自动化 | peekaboo |
| 通信平台 | wacli, xurl |

---

## 平台依赖分析

| 平台 | Skills |
|------|--------|
| macOS 专属 | bear-notes, peekaboo |
| 跨平台 | blogwatcher, blucli, camsnap, eightctl, gifgrep, nano-pdf, openhue, ordercli, sag, songsee, wacli, xurl |

---

## API Key 需求

| Skill | 所需密钥 |
|-------|----------|
| sag | ELEVENLABS_API_KEY |
| gifgrep | GIPHY_API_KEY (可选), TENOR_API_KEY (可选) |
| xurl | X API 凭证 |

---

## 推荐使用场景

### 智能家居场景
- **起床**：eightctl 调温 → openhue 开灯
- **离家**：openhue 关灯 → camsnap 启动监控
- **回家**：camsnap 停止 → openhue 开灯 → blucli 播放音乐

### 内容创作场景
- **博客追踪**：blogwatcher 扫描更新 → bear-notes 整理
- **社交媒体**：gifgrep 找图 → xurl 发布
- **音频处理**：songsee 分析 → sag 配音

### 自动化办公
- **UI 自动化**：peekaboo 操作界面
- **文档处理**：nano-pdf 批量编辑
- **通知推送**：wacli 消息提醒

---

*分析完成：内置 Skills 覆盖率 100% (54/54)* 🔺
