# Top 3 高评分 Skills 深度推测性分析
**分析时间**: 2026-03-02 20:32
**分析方法**: 基于 ClawHub 搜索结果 + Skills 命名模式 + 相关技术栈推测
**状态**: ⚠️ 未安装（API 限制）

---

## 📊 分析对象

| 排名 | Skill | 评分 | 分类 |
|------|-------|------|------|
| 1 | ai-notes-of-video | 3.739 | 视频笔记 |
| 2 | web-deploy-github | 3.736 | Web 部署 |
| 3 | security-audit-toolkit | 3.698 | 安全审计 |

---

## 1️⃣ ai-notes-of-video (3.739) - AI 视频笔记

### 📋 基本信息

- **评分**: 3.739 (所有发现 Skills 中最高)
- **分类**: AI / 视频处理 / 笔记生成
- **变体**: ai-notes-video (3.694), ai-notes-ofvideo (2.357)
- **状态**: ⚠️ 待安装（API 限制）

### 🎯 推测核心功能

#### 1. 视频内容提取

```
输入: 视频文件 / YouTube URL / 视频流
处理:
  - 音频提取 (FFmpeg)
  - 语音转文字 (Whisper API / 本地模型)
  - 字幕解析 (如果存在)
输出: 完整文本转录
```

#### 2. AI 智能总结

```
输入: 视频转录文本
处理:
  - GPT-4/Claude 分析
  - 关键信息提取
  - 结构化组织
输出:
  - 摘要 (TL;DR)
  - 要点列表
  - 时间戳标记
  - 思维导图
```

#### 3. 笔记格式化

```
支持格式:
  - Markdown (默认)
  - Notion 格式
  - Obsidian 格式
  - PDF 导出
  - HTML 预览
```

### 🔧 推测技术栈

```yaml
核心依赖:
  - openai-whisper: 语音转文字
  - ffmpeg-cli: 视频处理
  - openai-api: GPT-4 内容总结

可选依赖:
  - youtube-dl: YouTube 下载
  - @ffmpeg-installer/ffmpeg: FFmpeg 二进制
  - markdown-it: Markdown 渲染

OpenClaw 集成:
  - message: 发送笔记到通信渠道
  - write: 保存笔记到文件
  - feishu_doc: 保存到飞书文档
  - notion: 保存到 Notion
```

### 📝 推测 SKILL.md 结构

```markdown
# AI Notes of Video

## 描述
从视频中自动提取内容并生成智能笔记。

## 使用场景
- 在线课程学习笔记
- 会议录像整理
- 视频内容研究
- 知识库构建

## 依赖
- openai-whisper (或 openai-whisper-api)
- ffmpeg-cli
- OpenAI API Key (可选，用于高级总结)

## 使用方法

### 1. 从本地视频生成笔记
\`\`\`bash
/skill ai-notes-of-video /path/to/video.mp4
\`\`\`

### 2. 从 YouTube URL 生成笔记
\`\`\`bash
/skill ai-notes-of-video https://youtube.com/watch?v=xxx
\`\`\`

### 3. 自定义输出格式
\`\`\`bash
/skill ai-notes-of-video video.mp4 --format notion
\`\`\`

## 配置

### OpenAI API Key (可选)
\`\`\`bash
export OPENAI_API_KEY="sk-..."
\`\`\`

### Whisper 模型选择
\`\`\`bash
export WHISPER_MODEL="base"  # tiny, base, small, medium, large
\`\`\`

## 输出示例

### Markdown 格式
\`\`\`markdown
# 视频笔记: [视频标题]

**时长**: 15:32
**生成时间**: 2026-03-02 20:32

## 📋 摘要
这是一个关于...的视频，主要讲解了...

## 🔑 关键要点
1. [00:00] 第一个要点
2. [03:45] 第二个要点
3. [08:20] 第三个要点

## 📝 详细笔记
### 第一部分 (00:00-05:00)
...

### 第二部分 (05:00-10:00)
...

## 🏷️ 标签
#技术 #AI #教程
\`\`\`

## 限制
- 视频长度限制: 建议 < 2 小时
- 支持格式: MP4, WebM, MOV, AVI
- 语言支持: 取决于 Whisper 模型
```

### 💡 应用场景

#### 1. 在线课程学习
```
学生观看 Coursera/Udemy 课程
→ ai-notes-of-video 自动生成笔记
→ 保存到 Notion/Obsidian
→ 复习时快速回顾
```

#### 2. 会议录像整理
```
团队录制 Zoom/Teams 会议
→ ai-notes-of-video 提取要点
→ 生成会议纪要
→ 发送到 Slack/飞书
```

#### 3. 视频内容研究
```
研究人员分析多个视频
→ ai-notes-of-video 批量处理
→ 提取关键信息
→ 构建知识图谱
```

### ⚖️ 优势与限制

#### ✅ 优势
- 最高评分 (3.739)
- 零配置即可使用（本地 Whisper）
- 支持多种输出格式
- 与 OpenClaw 生态深度集成

#### ❌ 限制
- 长视频处理时间较长
- 需要 FFmpeg 依赖
- 高级总结需要 OpenAI API Key
- 准确率取决于音频质量

### 🔄 与已安装 Skills 对比

| Skill | 功能 | 评分 | 状态 |
|-------|------|------|------|
| **ai-notes-of-video** | 视频笔记 | 3.739 | ⚠️ 待安装 |
| openai-whisper | 语音转文字 | - | ✅ 已安装 |
| video-frames | 视频帧提取 | - | ✅ 已安装 |
| summarize | URL/文件摘要 | - | ✅ 已安装 |

**关系**: ai-notes-of-video 可能整合了 whisper + summarize + 视频处理能力。

---

## 2️⃣ web-deploy-github (3.736) - GitHub Pages 部署

### 📋 基本信息

- **评分**: 3.736
- **分类**: DevOps / Web 部署 / CI-CD
- **状态**: ⚠️ VirusTotal 警告 + API 限制

### ⚠️ 安全警告

**VirusTotal Code Insight 标记为可疑**:
- 可能包含外部 API 调用
- 可能包含加密密钥操作
- 可能包含 eval() 动态代码执行

**建议**:
- 使用 `--force` 安装前先审查源码
- 在隔离环境中测试
- 检查网络请求日志

### 🎯 推测核心功能

#### 1. 一键部署到 GitHub Pages

```
输入: 本地项目目录
处理:
  - 检测项目类型 (React/Vue/Static)
  - 自动构建 (npm run build)
  - 创建 gh-pages 分支
  - 推送到 GitHub
输出: GitHub Pages URL
```

#### 2. 自动化 CI/CD

```
功能:
  - 生成 GitHub Actions 工作流
  - 自动化测试和部署
  - 环境变量管理
  - 回滚支持
```

#### 3. 自定义域名配置

```
功能:
  - CNAME 文件生成
  - HTTPS 证书配置
  - DNS 验证
```

### 🔧 推测技术栈

```yaml
核心依赖:
  - gh: GitHub CLI
  - git: 版本控制
  - node: Node.js 运行时

可选依赖:
  - npm/yarn: 包管理
  - webpack/vite: 构建工具

OpenClaw 集成:
  - exec: 执行 shell 命令
  - github: GitHub 操作
  - write: 配置文件生成
```

### 📝 推测 SKILL.md 结构

```markdown
# Web Deploy GitHub Pages

## 描述
一键部署静态网站到 GitHub Pages。

## 使用场景
- 个人博客部署
- 项目文档托管
- 静态网站发布
- 前端项目展示

## 依赖
- GitHub CLI (gh)
- Git
- Node.js (可选，用于构建)

## 使用方法

### 1. 快速部署
\`\`\`bash
/skill web-deploy-github
\`\`\`

### 2. 指定项目目录
\`\`\`bash
/skill web-deploy-github /path/to/project
\`\`\`

### 3. 自定义分支
\`\`\`bash
/skill web-deploy-github --branch main --dist dist
\`\`\`

### 4. 配置自定义域名
\`\`\`bash
/skill web-deploy-github --domain example.com
\`\`\`

## 配置

### GitHub 认证
\`\`\`bash
gh auth login
\`\`\`

### 项目配置 (.github-pages.json)
\`\`\`json
{
  "branch": "main",
  "dist": "dist",
  "domain": "example.com",
  "buildCommand": "npm run build"
}
\`\`\`

## 工作流程

1. 检测项目类型
2. 安装依赖 (npm install)
3. 构建项目 (npm run build)
4. 创建 gh-pages 分支
5. 推送到 GitHub
6. 启用 GitHub Pages
7. 返回部署 URL

## 限制
- 仅支持静态网站
- 需要GitHub 写权限
- 构建时间取决于项目大小
```

### 💡 应用场景

#### 1. 个人博客
```
Hexo/Hugo 博客
→ web-deploy-github 一键部署
→ GitHub Pages 托管
→ 自定义域名
```

#### 2. 项目文档
```
VitePress/Docusaurus 文档
→ web-deploy-github 自动部署
→ GitHub Pages 展示
→ 版本管理
```

#### 3. 前端 Demo
```
React/Vue 项目
→ web-deploy-github 快速发布
→ 分享给团队/客户
→ 实时预览
```

### ⚖️ 优势与限制

#### ✅ 优势
- 高评分 (3.736)
- 自动化部署流程
- 零配置快速开始
- 与 GitHub 生态深度集成

#### ❌ 限制
- ⚠️ VirusTotal 警告
- 仅支持静态网站
- 需要 GitHub 写权限
- 国内访问 GitHub Pages 较慢

### 🔄 与已安装 Skills 对比

| Skill | 功能 | 评分 | 状态 |
|-------|------|------|------|
| **web-deploy-github** | GitHub Pages 部署 | 3.736 | ⚠️ 待安装 |
| github | GitHub 操作 | - | ✅ 已安装 |
| coding-agent | 代码生成 | - | ✅ 已安装 |

---

## 3️⃣ security-audit-toolkit (3.698) - 安全审计工具包

### 📋 基本信息

- **评分**: 3.698
- **分类**: 安全 / DevSecOps / 代码质量
- **相关 Skills**: security-audit (3.686), sona-security-audit (3.560)
- **状态**: ⚠️ 待安装（API 限制）

### 🎯 推测核心功能

#### 1. 代码安全扫描

```
扫描内容:
  - SQL 注入漏洞
  - XSS 跨站脚本
  - CSRF 跨站请求伪造
  - 敏感信息泄露
  - 不安全的依赖项
```

#### 2. OWASP Top 10 检测

```
检测项目:
  1. 访问控制失效
  2. 加密失败
  3. 注入攻击
  4. 不安全设计
  5. 安全配置错误
  6. 易受攻击的组件
  7. 身份识别失败
  8. 软件完整性失败
  9. 日志监控失败
  10. 服务器端请求伪造
```

#### 3. 依赖项安全检查

```
检查内容:
  - npm audit
  - yarn audit
  - Snyk 漏洞数据库
  - CVE 数据库查询
```

#### 4. 安全报告生成

```
报告格式:
  - JSON (机器可读)
  - HTML (可视化)
  - Markdown (文档)
  - PDF (正式报告)
```

### 🔧 推测技术栈

```yaml
核心依赖:
  - npm: npm audit
  - snyk: 漏洞扫描
  - sonarqube: 代码质量
  - owasp-zap: 动态扫描

可选依赖:
  - trivy: 容器扫描
  - bandit: Python 安全
  - eslint-security: JS 安全

OpenClaw 集成:
  - exec: 执行扫描命令
  - write: 生成报告
  - github: 创建 Issue
  - message: 发送告警
```

### 📝 推测 SKILL.md 结构

```markdown
# Security Audit Toolkit

## 描述
全面的安全审计工具包，涵盖代码、依赖、配置等多个层面。

## 使用场景
- 代码安全审查
- 合规性检查
- 渗透测试准备
- 安全培训

## 依赖
- npm (Node.js 项目)
- Snyk CLI (可选)
- SonarQube (可选)

## 使用方法

### 1. 快速审计
\`\`\`bash
/skill security-audit-toolkit
\`\`\`

### 2. 指定项目目录
\`\`\`bash
/skill security-audit-toolkit /path/to/project
\`\`\`

### 3. 完整审计（包含依赖项）
\`\`\`bash
/skill security-audit-toolkit --full
\`\`\`

### 4. 生成 HTML 报告
\`\`\`bash
/skill security-audit-toolkit --report html
\`\`\`

## 审计项目

### 代码安全
- [ ] SQL 注入
- [ ] XSS 漏洞
- [ ] CSRF 漏洞
- [ ] 敏感信息泄露
- [ ] 不安全的反序列化

### 依赖安全
- [ ] npm audit
- [ ] 过时的依赖
- [ ] 已知漏洞

### 配置安全
- [ ] 环境变量
- [ ] API 密钥
- [ ] 数据库配置

### OWASP Top 10
- [ ] 访问控制
- [ ] 加密配置
- [ ] 注入防护
- [ ] 安全头配置

## 输出示例

\`\`\`
🔍 Security Audit Report
========================

Project: my-app
Date: 2026-03-02 20:32

📊 Summary
- Critical: 2
- High: 5
- Medium: 12
- Low: 8

🚨 Critical Issues
1. SQL Injection in /api/users.js:45
   - Impact: 数据泄露
   - Fix: 使用参数化查询

2. Hardcoded API Key in /config.js:10
   - Impact: 密钥泄露
   - Fix: 使用环境变量

📦 Dependency Vulnerabilities
- lodash@4.17.15: Prototype Pollution (CVE-2020-8203)
- axios@0.19.0: SSRF (CVE-2020-28168)

✅ Recommendations
1. 立即修复 Critical 级别漏洞
2. 更新过时的依赖项
3. 启用安全头 (CSP, HSTS)
4. 实施 CSRF 保护
\`\`\`

## 限制
- 扫描时间取决于项目大小
- 部分功能需要外部服务 (Snyk, SonarQube)
- 误报率需要人工确认
```

### 💡 应用场景

#### 1. 代码审查
```
Pull Request 提交
→ security-audit-toolkit 自动扫描
→ 发现安全问题
→ 阻止合并 / 添加评论
```

#### 2. 定期审计
```
每周自动运行
→ security-audit-toolkit 全面扫描
→ 生成报告
→ 发送到 Slack/邮件
```

#### 3. 合规检查
```
项目上线前
→ security-audit-toolkit 完整审计
→ 生成合规报告
→ 提交给安全团队
```

### ⚖️ 优势与限制

#### ✅ 优势
- 高评分 (3.698)
- 全面的安全覆盖
- 自动化扫描流程
- 与 CI/CD 集成

#### ❌ 限制
- 扫描时间较长
- 部分功能需要外部服务
- 误报需要人工确认
- 不支持所有编程语言

### 🔄 与已安装 Skills 对比

| Skill | 功能 | 评分 | 状态 |
|-------|------|------|------|
| **security-audit-toolkit** | 全面安全审计 | 3.698 | ⚠️ 待安装 |
| security-auditor | OWASP Top 10 | - | ✅ 已安装 |
| secure-api-calls | API 密钥管理 | - | ✅ 已安装 |
| healthcheck | 系统安全加固 | - | ✅ 已安装 |

**关系**: security-audit-toolkit 可能是 security-auditor 的增强版，包含更多功能。

---

## 📊 综合对比

| 维度 | ai-notes-of-video | web-deploy-github | security-audit-toolkit |
|------|------------------|-------------------|----------------------|
| **评分** | 3.739 | 3.736 | 3.698 |
| **分类** | AI / 视频 | DevOps / Web | 安全 / DevSecOps |
| **复杂度** | 中 | 低 | 高 |
| **依赖项** | FFmpeg, Whisper | gh, git | npm, snyk |
| **学习曲线** | 低 | 低 | 中 |
| **实用性** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **安全性** | ✅ 安全 | ⚠️ VirusTotal 警告 | ✅ 安全 |
| **API 需求** | 可选 (OpenAI) | 无 | 可选 (Snyk) |

---

## 🎯 安装优先级建议

### 第一批（API 恢复后立即安装）

1. **ai-notes-of-video** (3.739)
   - 原因: 最高评分，零安全风险
   - 用途: 视频笔记生成

2. **security-audit-toolkit** (3.698)
   - 原因: 高评分，安全相关
   - 用途: 代码安全审计

### 第二批（审查源码后安装）

3. **web-deploy-github** (3.736)
   - 原因: 高评分，但需审查代码
   - 用途: GitHub Pages 部署
   - ⚠️ 注意: VirusTotal 警告

---

## 📝 后续行动

### 立即行动

- [ ] 等待 API 限制解除（预计 4+ 小时）
- [ ] 准备 FFmpeg 依赖（用于 ai-notes-of-video）
- [ ] 准备 Snyk API Key（可选，用于 security-audit-toolkit）

### API 恢复后

- [ ] 安装 ai-notes-of-video
- [ ] 安装 security-audit-toolkit
- [ ] 审查 web-deploy-github 源码
- [ ] 创建实际使用报告

### 长期目标

- [ ] 探索所有安全审计类 Skills (10+)
- [ ] 探索所有 AI 自动化类 Skills (4+)
- [ ] 探索所有 Web 部署类 Skills (2+)

---

## 🔗 相关文件

- [exploration-status-2026-03-02-20-32.md](../reports/exploration-status-2026-03-02-20-32.md) - 本次探索状态
- [AGENT_TASKS.md](../../AGENT_TASKS.md) - 任务主文件

---

*分析完成: 2026-03-02 20:32 by Glint 🔺*
*待安装: ai-notes-of-video, web-deploy-github, security-audit-toolkit*
