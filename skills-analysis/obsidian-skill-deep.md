# Obsidian Skill 深度分析报告

**分析日期**: 2026-03-02 08:05
**Skill 名称**: obsidian
**评分**: 内置 Skill
**分析者**: Glint 🔺

---

## 📊 概览

| 项目 | 信息 |
|------|------|
| **类型** | OpenClaw 内置 Skill |
| **功能** | Obsidian 知识库自动化操作 |
| **依赖** | `obsidian-cli` (yakitrak/yakitrak) |
| **平台** | macOS / Linux |
| **Emoji** | 💎 |
| **主页** | https://help.obsidian.md |

---

## 🎯 核心功能

### 1. Vault 管理
- 自动发现活动 Vault（读取 `~/Library/Application Support/obsidian/obsidian.json`）
- 支持多 Vault 环境
- 设置默认 Vault

### 2. 笔记操作
- **搜索**: 按名称或内容搜索笔记
- **创建**: 新建笔记并自动打开
- **移动/重命名**: 智能 Wikilink 更新
- **删除**: 安全删除笔记

### 3. 知识图谱特性
- 纯 Markdown 存储（开放格式）
- 支持 Wikilink (`[[note-name]]`)
- 支持 Canvas 文件（JSON 格式）

---

## 🔧 技术架构

### 依赖项

```json
{
  "bins": ["obsidian-cli"],
  "install": [
    {
      "id": "brew",
      "kind": "brew",
      "formula": "yakitrak/yakitrak/obsidian-cli",
      "bins": ["obsidian-cli"]
    }
  ]
}
```

### Vault 结构

```
your-vault/
├── *.md              # 笔记文件（纯文本 Markdown）
├── .obsidian/        # 配置目录（工作区 + 插件设置）
├── *.canvas          # Canvas 文件（JSON）
└── attachments/      # 附件（图片/PDF 等）
```

---

## 💡 使用场景

### 1. 知识库自动化
```bash
# 搜索包含特定内容的笔记
obsidian-cli search-content "OpenGL"

# 创建新笔记
obsidian-cli create "Graphics/Shader Notes" --content "# Shaders\n\n## Vertex Shader\n..."
```

### 2. 笔记重构
```bash
# 安全移动笔记（自动更新所有链接）
obsidian-cli move "OldFolder/note" "NewFolder/note"
```

### 3. 集成工作流
- 自动记录开发日志
- 与代码注释同步
- 生成技术文档

---

## 📝 CLI 命令参考

### Vault 管理

| 命令 | 功能 |
|------|------|
| `obsidian-cli set-default "<vault>"` | 设置默认 Vault |
| `obsidian-cli print-default` | 显示默认 Vault |
| `obsidian-cli print-default --path-only` | 仅显示路径 |

### 搜索

| 命令 | 功能 |
|------|------|
| `obsidian-cli search "query"` | 搜索笔记名称 |
| `obsidian-cli search-content "query"` | 搜索笔记内容（含片段和行号） |

### 笔记操作

| 命令 | 功能 |
|------|------|
| `obsidian-cli create "path" --content "..." --open` | 创建笔记 |
| `obsidian-cli move "old" "new"` | 移动/重命名（更新链接） |
| `obsidian-cli delete "path"` | 删除笔记 |

---

## ⚠️ 注意事项

### 1. 平台限制
- 主要为 macOS 设计（依赖 `~/Library/Application Support/`）
- Linux 用户需要手动配置 Vault 路径

### 2. URI Handler
- 创建笔记功能依赖 `obsidian://` URI 处理器
- 需要安装 Obsidian 桌面应用

### 3. 隐藏目录
- 避免在隐藏目录（`.something/`）下创建笔记
- Obsidian 可能拒绝这类路径

### 4. 多 Vault 环境
- 常见配置：iCloud vs 本地、工作 vs 个人
- 不要猜测路径，始终读取配置

---

## 🔄 与 OpenClaw 集成

### 作为 Skill 使用
```javascript
// Agent 可以通过 exec 调用 obsidian-cli
exec('obsidian-cli search-content "rendering pipeline"');

// 或直接编辑 .md 文件（Obsidian 会自动检测）
read('/path/to/vault/notes/shader.md');
edit('/path/to/vault/notes/shader.md', oldText, newText);
```

### 潜在自动化场景
1. **开发日志**: 自动将代码提交记录写入 Obsidian
2. **API 文档**: 从代码注释生成笔记
3. **学习追踪**: 定期更新技术学习笔记
4. **项目知识库**: 维护项目特定的知识图谱

---

## 🆚 对比分析

| 特性 | Obsidian | Notion | Apple Notes |
|------|----------|--------|-------------|
| 存储格式 | 纯 Markdown | 专有 | 专有 |
| 本地优先 | ✅ | ❌ | ✅ |
| CLI 支持 | ✅ (obsidian-cli) | ✅ (官方 API) | ❌ |
| Wikilink | ✅ | ❌ | ❌ |
| 插件生态 | 丰富 | 有限 | 无 |
| 开放性 | 高 | 中 | 低 |

---

## 📈 优势与局限

### ✅ 优势
1. **开放格式**: 纯 Markdown，永不锁定
2. **本地优先**: 数据完全掌控
3. **链接智能**: 移动笔记自动更新 Wikilink
4. **丰富生态**: 大量社区插件
5. **CLI 友好**: 支持自动化脚本

### ⚠️ 局限
1. **平台依赖**: 主要面向 macOS
2. **需要桌面应用**: 创建笔记依赖 URI Handler
3. **无原生 API**: 依赖第三方 CLI 工具
4. **配置复杂**: 多 Vault 环境需要额外处理

---

## 🎓 最佳实践

### 1. Vault 发现
```bash
# 始终从配置读取，不要硬编码
obsidian-cli print-default --path-only
```

### 2. 笔记操作
```bash
# 优先使用 CLI 移动（自动更新链接）
obsidian-cli move "old/path" "new/path"

# 而不是直接 mv（会破坏链接）
# mv vault/old/path.md vault/new/path.md  ❌
```

### 3. 直接编辑
```bash
# 简单修改可以直接编辑 .md 文件
# Obsidian 会自动检测变化
```

---

## 🔗 相关 Skills

| Skill | 关系 |
|-------|------|
| **notion** | 替代方案（云端协作） |
| **bear-notes** | 替代方案（Apple 生态） |
| **apple-notes** | 替代方案（系统原生） |
| **summarize** | 配合使用（摘要笔记内容） |

---

## 📚 参考资料

- [Obsidian 官方帮助](https://help.obsidian.md)
- [obsidian-cli (yakitrak)](https://github.com/yakitrak/obsidian-cli)
- [Obsidian URI Scheme](https://help.obsidian.md/Extending+Obsidian/Obsidian+URI)

---

## 📌 总结

Obsidian Skill 为 OpenClaw 提供了与 Obsidian 知识库集成的能力。作为一个**本地优先、开放格式**的笔记工具，Obsidian 非常适合：

- 技术文档管理
- 个人知识库
- 开发日志记录
- 与代码项目关联的笔记

对于 OpenGL 项目，可以用它来维护：
- 着色器笔记
- 渲染管线文档
- 技术学习记录
- Bug 追踪日志

**推荐指数**: ⭐⭐⭐⭐ (4/5)
**适合场景**: 知识管理、技术文档、自动化笔记

---

*分析完成于 2026-03-02 08:05 by Glint 🔺*
