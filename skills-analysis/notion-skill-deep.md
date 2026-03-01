# Notion Skill 深度分析报告

**分析日期:** 2026-03-02  
**Skill 名称:** notion  
**类型:** OpenClaw 内置 Skill  
**评分:** N/A (内置)  
**Emoji:** 📝

---

## 1. 概述

Notion Skill 是 OpenClaw 内置的 Notion API 集成，用于创建和管理 Notion 页面、数据库和内容块。支持完整的 CRUD 操作，可与 Notion 工作空间进行双向同步。

### 核心能力

- 📄 **页面管理** - 创建、读取、更新页面
- 🗄️ **数据库操作** - 查询和创建数据源
- 🧱 **块级编辑** - 添加段落、列表等内容块
- 🔍 **搜索功能** - 全局搜索页面和数据源

---

## 2. 配置要求

### 环境变量

```bash
NOTION_API_KEY  # 必需：Notion 集成 API Key（以 ntn_ 或 secret_ 开头）
```

### 初始设置步骤

1. **创建集成**
   - 访问 https://notion.so/my-integrations
   - 创建新集成，获取 API Key

2. **存储凭证**
   ```bash
   mkdir -p ~/.config/notion
   echo "ntn_your_key_here" > ~/.config/notion/api_key
   ```

3. **授权页面**
   - 在 Notion 中打开目标页面/数据库
   - 点击 "..." → "Connect to" → 选择你的集成

### API 版本

- 使用 Notion API 版本: `2025-09-03`
- 必须在请求头中包含 `Notion-Version`

---

## 3. API 使用详解

### 基础请求结构

```bash
NOTION_KEY=$(cat ~/.config/notion/api_key)
curl -X METHOD "https://api.notion.com/v1/..." \
  -H "Authorization: Bearer $NOTION_KEY" \
  -H "Notion-Version: 2025-09-03" \
  -H "Content-Type: application/json"
```

### 核心操作

#### 3.1 搜索

```bash
curl -X POST "https://api.notion.com/v1/search" \
  -H "Authorization: Bearer $NOTION_KEY" \
  -H "Notion-Version: 2025-09-03" \
  -H "Content-Type: application/json" \
  -d '{"query": "页面标题"}'
```

#### 3.2 获取页面

```bash
curl "https://api.notion.com/v1/pages/{page_id}" \
  -H "Authorization: Bearer $NOTION_KEY" \
  -H "Notion-Version: 2025-09-03"
```

#### 3.3 获取页面内容（块）

```bash
curl "https://api.notion.com/v1/blocks/{page_id}/children" \
  -H "Authorization: Bearer $NOTION_KEY" \
  -H "Notion-Version: 2025-09-03"
```

#### 3.4 在数据库中创建页面

```bash
curl -X POST "https://api.notion.com/v1/pages" \
  -H "Authorization: Bearer $NOTION_KEY" \
  -H "Notion-Version: 2025-09-03" \
  -H "Content-Type: application/json" \
  -d '{
    "parent": {"database_id": "xxx"},
    "properties": {
      "Name": {"title": [{"text": {"content": "新项目"}}]},
      "Status": {"select": {"name": "Todo"}}
    }
  }'
```

#### 3.5 查询数据库（数据源）

```bash
curl -X POST "https://api.notion.com/v1/data_sources/{data_source_id}/query" \
  -H "Authorization: Bearer $NOTION_KEY" \
  -H "Notion-Version: 2025-09-03" \
  -H "Content-Type: application/json" \
  -d '{
    "filter": {"property": "Status", "select": {"equals": "Active"}},
    "sorts": [{"property": "Date", "direction": "descending"}]
  }'
```

#### 3.6 创建数据库（数据源）

```bash
curl -X POST "https://api.notion.com/v1/data_sources" \
  -H "Authorization: Bearer $NOTION_KEY" \
  -H "Notion-Version: 2025-09-03" \
  -H "Content-Type: application/json" \
  -d '{
    "parent": {"page_id": "xxx"},
    "title": [{"text": {"content": "我的数据库"}}],
    "properties": {
      "Name": {"title": {}},
      "Status": {"select": {"options": [{"name": "Todo"}, {"name": "Done"}]}},
      "Date": {"date": {}}
    }
  }'
```

#### 3.7 更新页面属性

```bash
curl -X PATCH "https://api.notion.com/v1/pages/{page_id}" \
  -H "Authorization: Bearer $NOTION_KEY" \
  -H "Notion-Version: 2025-09-03" \
  -H "Content-Type: application/json" \
  -d '{"properties": {"Status": {"select": {"name": "Done"}}}}'
```

#### 3.8 添加内容块

```bash
curl -X PATCH "https://api.notion.com/v1/blocks/{page_id}/children" \
  -H "Authorization: Bearer $NOTION_KEY" \
  -H "Notion-Version: 2025-09-03" \
  -H "Content-Type: application/json" \
  -d '{
    "children": [
      {"object": "block", "type": "paragraph", "paragraph": {"rich_text": [{"text": {"content": "Hello"}}]}}
    ]
  }'
```

---

## 4. 属性类型参考

| 类型 | 格式 |
|------|------|
| **标题** | `{"title": [{"text": {"content": "..."}}]}` |
| **富文本** | `{"rich_text": [{"text": {"content": "..."}}]}` |
| **单选** | `{"select": {"name": "选项名"}}` |
| **多选** | `{"multi_select": [{"name": "A"}, {"name": "B"}]}` |
| **日期** | `{"date": {"start": "2024-01-15", "end": "2024-01-16"}}` |
| **复选框** | `{"checkbox": true}` |
| **数字** | `{"number": 42}` |
| **URL** | `{"url": "https://..."}` |
| **邮箱** | `{"email": "a@b.com"}` |
| **关联** | `{"relation": [{"id": "page_id"}]}` |

---

## 5. 2025-09-03 API 版本关键变更

### 重要变化

1. **数据库 → 数据源**
   - 新端点使用 `/data_sources/` 路径
   - 查询数据库需使用数据源 ID

2. **双重 ID 系统**
   - `database_id`: 创建页面时使用（作为 parent）
   - `data_source_id`: 查询数据库时使用

3. **搜索结果格式**
   - 数据库返回为 `"object": "data_source"`
   - 包含 `data_source_id` 字段

### 迁移指南

```bash
# 旧版 API
POST /v1/databases/{database_id}/query

# 新版 API (2025-09-03)
POST /v1/data_sources/{data_source_id}/query
```

---

## 6. 使用场景

### 6.1 项目管理

- 自动创建任务页面
- 批量更新任务状态
- 生成项目进度报告

### 6.2 知识库管理

- 自动整理文档
- 创建知识条目
- 建立内容索引

### 6.3 自动化工作流

- 与其他 Skill 联动（如 GitHub Issues 同步）
- 定时生成报告
- 批量数据导入/导出

### 6.4 内容发布

- 自动创建博客草稿
- 管理发布日历
- 内容审核流程

---

## 7. 限制与注意事项

| 限制 | 说明 |
|------|------|
| **速率限制** | ~3 请求/秒（平均） |
| **ID 格式** | UUID，可带或不带连字符 |
| **视图过滤器** | API 无法设置，仅 UI 可用 |
| **权限** | 必须显式授权页面给集成 |

---

## 8. 最佳实践

### 8.1 凭证管理

```bash
# 使用环境变量而非硬编码
export NOTION_API_KEY=$(cat ~/.config/notion/api_key)
```

### 8.2 错误处理

```bash
response=$(curl -s -w "\n%{http_code}" ...)
status=$(echo "$response" | tail -n1)
body=$(echo "$response" | sed '$d')

if [ "$status" -ne 200 ]; then
  echo "Error: $body"
  exit 1
fi
```

### 8.3 批量操作

```bash
# 使用 sleep 控制速率
for item in "${items[@]}"; do
  create_page "$item"
  sleep 0.4  # ~2.5 请求/秒
done
```

---

## 9. 与其他 Skill 的集成

| Skill | 集成场景 |
|-------|----------|
| **github** | 同步 Issues 到 Notion 数据库 |
| **summarize** | 摘要内容存储到 Notion |
| **weather** | 记录天气数据到 Notion |
| **discord/slack** | 消息归档到 Notion |

---

## 10. 总结

### 优点 ✅

- 完整的 API 覆盖（页面、数据库、块）
- 支持最新 Notion API 版本
- 清晰的属性类型文档
- 适合自动化工作流

### 局限 ⚠️

- 需要手动授权每个页面
- 无法设置视图过滤器
- 速率限制较严格

### 推荐指数: ⭐⭐⭐⭐ (4/5)

适合需要将 AI 工作流与 Notion 知识库集成的用户。

---

*分析者: Glint 🔺*  
*ClawHub Lab*
