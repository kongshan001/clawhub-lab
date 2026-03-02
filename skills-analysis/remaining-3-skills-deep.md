# 剩余 3 个内置 Skills 深度分析

> 分析日期: 2026-03-02
> 分析者: Glint 🔺

---

## 1. clawhub - ClawHub Skills 包管理器

### 基本信息

| 属性 | 值 |
|------|-----|
| 名称 | clawhub |
| 用途 | ClawHub Skills 市场的搜索、安装、更新、发布 |
| 安装方式 | `npm i -g clawhub` |
| 依赖 | Node.js |

### 核心功能

#### 1. 搜索 Skills
```bash
clawhub search "postgres backups"
clawhub search "ai image"
```

#### 2. 安装 Skills
```bash
# 安装最新版本
clawhub install my-skill

# 安装指定版本
clawhub install my-skill --version 1.2.3
```

#### 3. 更新 Skills
```bash
# 更新单个 Skill
clawhub update my-skill
clawhub update my-skill --version 1.2.3

# 更新所有
clawhub update --all

# 强制更新
clawhub update my-skill --force
clawhub update --all --no-input --force
```

#### 4. 列出已安装
```bash
clawhub list
```

#### 5. 发布 Skills
```bash
clawhub publish ./my-skill \
  --slug my-skill \
  --name "My Skill" \
  --version 1.2.0 \
  --changelog "Fixes + docs"
```

### 认证

```bash
# 登录（发布需要）
clawhub login

# 查看当前用户
clawhub whoami
```

### 配置选项

| 环境变量 | 说明 | 默认值 |
|----------|------|--------|
| `CLAWHUB_REGISTRY` | 注册表 URL | https://clawhub.com |
| `CLAWHUB_WORKDIR` | 工作目录 | cwd 或 OpenClaw workspace |

### 架构特点

1. **哈希匹配更新**: `update` 命令通过文件哈希识别本地版本
2. **灵活的目录结构**: 支持 `--workdir` 和 `--dir` 参数
3. **版本控制**: 支持语义化版本安装和更新

### 使用场景

| 场景 | 命令示例 |
|------|----------|
| 发现新 Skills | `clawhub search "weather"` |
| 安装生产 Skills | `clawhub install github` |
| 批量更新 | `clawhub update --all --no-input` |
| 发布自定义 Skill | `clawhub publish ./skill --slug xxx` |

### 评分: ⭐⭐⭐⭐⭐ (5/5)

**理由**: ClawHub 是 OpenClaw 生态的核心组件，提供了完整的 Skills 生命周期管理。

---

## 2. goplaces - Google Places API CLI

### 基本信息

| 属性 | 值 |
|------|-----|
| 名称 | goplaces |
| 用途 | Google Places API (New) 查询 |
| 安装方式 | `brew install steipete/tap/goplaces` |
| 依赖 | `GOOGLE_PLACES_API_KEY` |
| 主页 | https://github.com/steipete/goplaces |
| Emoji | 📍 |

### 核心功能

#### 1. 文本搜索
```bash
# 基础搜索
goplaces search "coffee"

# 带过滤条件
goplaces search "coffee" --open-now --min-rating 4 --limit 5

# 地理位置偏置
goplaces search "pizza" --lat 40.8 --lng -73.9 --radius-m 3000

# 分页
goplaces search "pizza" --page-token "NEXT_PAGE_TOKEN"
```

#### 2. 地址解析
```bash
goplaces resolve "Soho, London" --limit 5
```

#### 3. 地点详情
```bash
goplaces details <place_id> --reviews
```

#### 4. JSON 输出（脚本友好）
```bash
goplaces search "sushi" --json
```

### 环境配置

| 环境变量 | 必需 | 说明 |
|----------|------|------|
| `GOOGLE_PLACES_API_KEY` | ✅ | Google Places API 密钥 |
| `GOOGLE_PLACES_BASE_URL` | ❌ | 自定义基础 URL（测试用） |

### 输出特性

- **人类可读**: 默认输出格式化的文本
- **JSON 模式**: `--json` 用于脚本集成
- **颜色控制**: `--no-color` 或 `NO_COLOR` 禁用 ANSI

### 价格等级说明

| 等级 | 含义 |
|------|------|
| 0 | 免费 |
| 1 | 便宜 |
| 2 | 中等 |
| 3 | 昂贵 |
| 4 | 非常昂贵 |

### 使用场景

| 场景 | 命令示例 |
|------|----------|
| 找附近的咖啡馆 | `goplaces search "coffee" --lat 40.7 --lng -74` |
| 查找高评分餐厅 | `goplaces search "restaurant" --min-rating 4.5` |
| 获取地点评论 | `goplaces details ChIJ... --reviews` |
| 脚本集成 | `goplaces search "hotel" --json \| jq .` |

### 评分: ⭐⭐⭐⭐ (4/5)

**理由**: 优秀的地点查询 CLI，但需要 Google API Key。

**扣分项**: 需要 Google Cloud 账户和 API Key 配置。

---

## 3. sonoscli - Sonos 音响控制

### 基本信息

| 属性 | 值 |
|------|-----|
| 名称 | sonoscli |
| 用途 | Sonos 音响控制（发现/播放/音量/分组） |
| 安装方式 | `go install github.com/steipete/sonoscli/cmd/sonos@latest` |
| 依赖 | Go, 本地网络访问 |
| 主页 | https://sonoscli.sh |
| Emoji | 🔊 |

### 核心功能

#### 1. 发现设备
```bash
sonos discover
```

#### 2. 状态查询
```bash
sonos status --name "Kitchen"
```

#### 3. 播放控制
```bash
sonos play --name "Kitchen"
sonos pause --name "Kitchen"
sonos stop --name "Kitchen"
```

#### 4. 音量控制
```bash
sonos volume set 15 --name "Kitchen"
```

#### 5. 分组管理
```bash
# 查看分组状态
sonos group status

# 加入分组
sonos group join

# 退出分组
sonos group unjoin

# 派对模式（所有设备同步）
sonos group party

# 单独模式
sonos group solo
```

#### 6. 收藏夹
```bash
sonos favorites list
sonos favorites open "My Playlist"
```

#### 7. 播放队列
```bash
sonos queue list
sonos queue play 1
sonos queue clear
```

#### 8. Spotify 集成
```bash
# 通过 SMAPI 搜索
sonos smapi search --service "Spotify" --category tracks "query"
```

### 环境变量

| 变量 | 说明 |
|------|------|
| `SPOTIFY_CLIENT_ID` | Spotify Web API 客户端 ID（可选） |
| `SPOTIFY_CLIENT_SECRET` | Spotify Web API 密钥（可选） |

### 故障排除

#### 问题 1: `no route to host`

**原因**: SSDP 组播被阻止

**解决方案**:
- macOS: 系统设置 → 隐私与安全 → 本地网络 → 启用相关应用
  - `node` (launchd 运行)
  - `Terminal` (终端运行)
  - `Visual Studio Code` (VS Code 终端)
- 或使用 Docker sandbox 并允许网络访问

#### 问题 2: `bind: operation not permitted`

**原因**: 沙箱环境不允许网络绑定

**解决方案**:
- 确保运行环境有网络权限
- Codex 等沙箱需要批准网络权限请求

### 备用连接方式

```bash
# 如果 SSDP 发现失败，直接指定 IP
sonos status --ip 192.168.1.100
```

### 使用场景

| 场景 | 命令示例 |
|------|----------|
| 早晨闹钟 | `sonos play --name "Bedroom"` |
| 全屋派对 | `sonos group party` |
| 音量调节 | `sonos volume set 20 --name "Living Room"` |
| 查看状态 | `sonos status --name "Kitchen"` |

### 评分: ⭐⭐⭐⭐ (4/5)

**理由**: 优秀的 Sonos 控制工具，功能全面。

**扣分项**: 
- 仅限 Sonos 设备
- 需要 SSDP/本地网络访问
- macOS 需要额外权限配置

---

## 总结

| Skill | 功能 | API Key | 推荐度 |
|-------|------|---------|--------|
| clawhub | Skills 包管理 | 登录即可 | ⭐⭐⭐⭐⭐ |
| goplaces | 地点查询 | Google API Key | ⭐⭐⭐⭐ |
| sonoscli | Sonos 控制 | 无（本地网络） | ⭐⭐⭐⭐ |

### 内置 Skills 分析完成度

至此，**所有 54 个 OpenClaw 内置 Skills 均已完成深度分析**！ 🎉

---

*分析完成: 2026-03-02 10:55 by Glint 🔺*
