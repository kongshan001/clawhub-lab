# Spotify Player Skill 深度分析

## 基本信息

| 属性 | 值 |
|------|-----|
| **名称** | spotify-player |
| **类型** | OpenClaw 内置 Skill |
| **分类** | 娱乐/音乐 |
| **Emoji** | 🎵 |
| **依赖** | `spogo` (首选) 或 `spotify_player` |
| **主页** | https://www.spotify.com |

## 功能概述

Spotify Player Skill 允许 OpenClaw 通过终端控制 Spotify 播放：

- **搜索** - 搜索歌曲、专辑、艺术家、播放列表
- **播放控制** - 播放、暂停、下一首、上一首
- **设备管理** - 列出和切换播放设备
- **状态查询** - 获取当前播放状态
- **收藏管理** - 喜欢歌曲

## 前置要求

⚠️ **必须**: Spotify Premium 账户

## 安装

### 方案 A: spogo (推荐)

```bash
brew tap steipete/tap
brew install spogo
```

### 方案 B: spotify_player (备选)

```bash
brew install spotify_player
```

## 认证设置

### spogo 认证

从浏览器导入 cookies（推荐）:

```bash
spogo auth import --browser chrome
```

支持的浏览器: chrome, safari, firefox, edge

## 命令参考

### spogo 命令（首选）

| 命令 | 功能 | 示例 |
|------|------|------|
| `search` | 搜索内容 | `spogo search track "Bohemian Rhapsody"` |
| `play` | 播放 | `spogo play` |
| `pause` | 暂停 | `spogo pause` |
| `next` | 下一首 | `spogo next` |
| `prev` | 上一首 | `spogo prev` |
| `device list` | 列出设备 | `spogo device list` |
| `device set` | 设置设备 | `spogo device set "Living Room Speaker"` |
| `status` | 当前状态 | `spogo status` |

### spotify_player 命令（备选）

| 命令 | 功能 | 示例 |
|------|------|------|
| `search` | 搜索 | `spotify_player search "Queen"` |
| `playback play` | 播放 | `spotify_player playback play` |
| `playback pause` | 暂停 | `spotify_player playback pause` |
| `playback next` | 下一首 | `spotify_player playback next` |
| `playback previous` | 上一首 | `spotify_player playback previous` |
| `connect` | 连接设备 | `spotify_player connect` |
| `like` | 喜欢歌曲 | `spotify_player like` |

## 使用示例

### 搜索音乐

```bash
# 搜索歌曲
spogo search track "Shape of You"

# 搜索专辑
spogo search album "Thriller"

# 搜索艺术家
spogo search artist "The Beatles"

# 搜索播放列表
spogo search playlist "Workout Mix"
```

### 播放控制

```bash
# 开始/恢复播放
spogo play

# 暂停
spogo pause

# 下一首
spogo next

# 上一首
spogo prev
```

### 设备管理

```bash
# 列出所有可用设备
spogo device list

# 切换到特定设备
spogo device set "Kitchen Speaker"

# 设置设备 by ID
spogo device set "abc123def456"
```

### 查看状态

```bash
# 当前播放状态
spogo status
```

输出示例：
```
🎵 Now Playing: Bohemian Rhapsody - Queen
🔊 Device: Living Room Speaker
⏱️ Progress: 2:34 / 5:55
```

## 高级配置

### spotify_player 配置文件

配置目录: `~/.config/spotify-player/`

**app.toml 示例**:
```tomlab
# Spotify Connect 集成
client_id = "your-spotify-client-id"

# 默认设备
default_device = "Living Room Speaker"

# 音量
default_volume = 70
```

### 获取 Spotify Client ID

1. 访问 https://developer.spotify.com/dashboard/
2. 创建新应用
3. 复制 Client ID
4. 添加到 `~/.config/spotify-player/app.toml`

## 与 OpenClaw 集成

### 典型使用场景

1. **语音控制音乐**
   ```
   用户: "播放一些放松的音乐"
   Agent: spogo search playlist "Chill Vibes"
   Agent: spogo play
   ```

2. **切换播放设备**
   ```
   用户: "把音乐切换到客厅音响"
   Agent: spogo device set "Living Room Speaker"
   ```

3. **查询当前播放**
   ```
   用户: "现在播放的是什么歌？"
   Agent: spogo status
   ```

4. **暂停音乐**
   ```
   用户: "暂停一下音乐"
   Agent: spogo pause
   ```

### 自动化示例

```bash
# 早晨闹钟：播放指定播放列表
0 7 * * * spogo device set "Bedroom Speaker" && spogo search playlist "Morning Energy" && spogo play

# 工作时间暂停
0 9 * * 1-5 spogo pause

# 下班后播放
0 18 * * 1-5 spogo search playlist "Evening Chill" && spogo play
```

## TUI 模式

`spotify_player` 支持终端用户界面模式：

```bash
spotify_player
```

**快捷键**:
- `?` - 显示帮助
- `/` - 搜索
- `Space` - 播放/暂停
- `n` - 下一首
- `p` - 上一首
- `q` - 退出

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| "No device found" | 无活动设备 | 先在 Spotify 应用中开始播放 |
| "Premium required" | 免费账户 | 升级到 Premium |
| 认证失败 | Cookies 过期 | 重新运行 `spogo auth import` |
| 搜索无结果 | API 限制 | 检查网络连接 |

## spogo vs spotify_player 对比

| 特性 | spogo | spotify_player |
|------|-------|----------------|
| 安装方式 | Homebrew tap | Homebrew core |
| 认证方式 | Cookie 导入 | OAuth / Client ID |
| TUI 模式 | ❌ | ✅ |
| 搜索类型 | 支持 track/album/artist/playlist | 通用搜索 |
| 设备管理 | ✅ | ✅ |
| 推荐程度 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |

## 总结

Spotify Player Skill 为 OpenClaw 提供了便捷的音乐控制能力：

- 🎵 **无缝搜索** - 快速搜索任何歌曲、专辑或播放列表
- 🔊 **多设备支持** - 在不同设备间切换播放
- ⏯️ **完整控制** - 播放、暂停、跳过等常用操作
- 🤖 **语音集成** - 通过自然语言控制音乐

**适用场景**: 智能家居控制、语音助手音乐功能、自动化播放任务。

---

*分析日期: 2026-03-02 | 分析者: Glint 🔺*
