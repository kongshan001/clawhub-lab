# Trello Skill 深度分析

**分析日期**: 2026-03-02
**Skill 类型**: OpenClaw 内置
**分类**: 任务管理 / 看板
**Emoji**: 📋

---

## 概述

Trello Skill 通过 Trello REST API 管理 Trello 看板、列表和卡片。支持完整的 CRUD 操作，适合团队协作和项目管理。

## 配置

### 1. 获取 API 凭证

1. 访问 https://trello.com/app-key 获取 API Key
2. 点击页面上的 "Token" 链接生成 Token

### 2. 设置环境变量

```bash
export TRELLO_API_KEY="your-api-key"
export TRELLO_TOKEN="your-token"
```

## 依赖

| 依赖 | 用途 |
|------|------|
| `jq` | JSON 解析 |
| `curl` | HTTP 请求 |
| `TRELLO_API_KEY` | API 认证 |
| `TRELLO_TOKEN` | 用户授权 |

## 核心操作

### 1. 列出看板

```bash
curl -s "https://api.trello.com/1/members/me/boards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq '.[] | {name, id}'
```

### 2. 列出看板中的列表

```bash
curl -s "https://api.trello.com/1/boards/{boardId}/lists?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq '.[] | {name, id}'
```

### 3. 列出列表中的卡片

```bash
curl -s "https://api.trello.com/1/lists/{listId}/cards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq '.[] | {name, id, desc}'
```

### 4. 创建卡片

```bash
curl -s -X POST "https://api.trello.com/1/cards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" \
  -d "idList={listId}" \
  -d "name=Card Title" \
  -d "desc=Card description"
```

### 5. 移动卡片

```bash
curl -s -X PUT "https://api.trello.com/1/cards/{cardId}?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" \
  -d "idList={newListId}"
```

### 6. 添加评论

```bash
curl -s -X POST "https://api.trello.com/1/cards/{cardId}/actions/comments?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" \
  -d "text=Your comment here"
```

### 7. 归档卡片

```bash
curl -s -X PUT "https://api.trello.com/1/cards/{cardId}?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" \
  -d "closed=true"
```

## 高级查询

### 搜索看板

```bash
# 按名称查找看板
curl -s "https://api.trello.com/1/members/me/boards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq '.[] | select(.name | contains("Work"))'
```

### 获取看板所有卡片

```bash
curl -s "https://api.trello.com/1/boards/{boardId}/cards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq '.[] | {name, list: .idList}'
```

### 仅获取看板名称和 ID

```bash
curl -s "https://api.trello.com/1/members/me/boards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN&fields=name,id" | jq
```

## 使用场景

### 1. 自动化任务创建

```yaml
# 场景：新 Issue 自动创建 Trello 卡片
- trigger: github_issue_opened
  actions:
    - exec: |
        curl -s -X POST "https://api.trello.com/1/cards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" \
          -d "idList=${INBOX_LIST_ID}" \
          -d "name=Fix: ${ISSUE_TITLE}" \
          -d "desc=${ISSUE_BODY}"
```

### 2. 看板状态同步

```bash
# 获取某看板的 WIP 卡片数
curl -s "https://api.trello.com/1/lists/${LIST_ID}/cards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq 'length'
```

### 3. 每日任务回顾

```bash
# 列出"今日"列表中的所有卡片
curl -s "https://api.trello.com/1/lists/${TODAY_LIST_ID}/cards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq '.[].name'
```

### 4. 批量归档

```bash
# 归档某列表中的所有卡片
LIST_ID="xxx"
CARDS=$(curl -s "https://api.trello.com/1/lists/$LIST_ID/cards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq -r '.[].id')

for card_id in $CARDS; do
  curl -s -X PUT "https://api.trello.com/1/cards/$card_id?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" -d "closed=true"
done
```

### 5. 团队通知

```yaml
# 场景：卡片移动到"完成"时通知
- trigger: card_moved_to_done
  actions:
    - slack.send: "✅ ${CARD_NAME} 已完成"
```

## API 限制

| 限制 | 值 |
|------|-----|
| **每 API Key** | 300 请求 / 10 秒 |
| **每 Token** | 100 请求 / 10 秒 |
| **/1/members** | 100 请求 / 900 秒 |

## 与其他 Skills 协同

| Skill | 协同场景 |
|-------|----------|
| **github** | Issues ↔ Trello 卡片同步 |
| **slack/discord** | 卡片变更通知 |
| **notion** | 双向任务同步 |
| **things-mac** | 个人任务与团队看板联动 |

## 安全考虑

1. **凭证保护**: API Key 和 Token 提供完全访问权限
2. **环境变量**: 不要硬编码在脚本中
3. **Token 过期**: 定期更新 Token
4. **权限最小化**: 只授予必要的看板权限

## 与 Things 3 对比

| 特性 | Trello | Things 3 |
|------|--------|----------|
| **协作** | ✅ 团队协作 | ❌ 个人 |
| **看板视图** | ✅ 原生看板 | ❌ 列表 |
| **平台** | 全平台 | macOS/iOS |
| **价格** | 免费基础版 | 付费 |
| **API** | REST API | URL Scheme |

## 常用 ID 获取

```bash
# 获取看板 ID
curl -s "https://api.trello.com/1/members/me/boards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq '.[] | {name, id}'

# 获取列表 ID
curl -s "https://api.trello.com/1/boards/{boardId}/lists?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq '.[] | {name, id}'

# 从 URL 获取 ID
# https://trello.com/b/BOARD_ID/board-name
# https://trello.com/c/CARD_ID/card-name
```

## 最佳实践

1. **使用变量**: 缓存 Board/List ID
2. **错误处理**: 检查 curl 返回值
3. **速率限制**: 添加请求间隔
4. **备份**: 定期导出看板数据

## 总结

Trello Skill 提供了完整的 Trello API 集成，适合：

- ✅ 团队项目管理
- ✅ 自动化工作流
- ✅ 跨平台任务协作
- ✅ 与其他工具集成

**推荐指数**: ⭐⭐⭐⭐ (4/5)

---

*分析者: Glint 🔺 | ClawHub Lab*
