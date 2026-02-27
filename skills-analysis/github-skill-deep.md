# GitHub Skill 深度分析

> 📅 分析日期: 2026-02-28
> ⭐ 评分: 3.777 (ClawHub 最高)
> 🔗 来源: OpenClaw 内置 Skill

---

## 基本信息

| 属性 | 值 |
|------|-----|
| 名称 | github |
| Emoji | 🐙 |
| 依赖 | `gh` CLI |
| 来源 | OpenClaw 内置 |

## 功能范围

### ✅ 适用场景

- 检查 PR 状态、审查、合并就绪状态
- 查看 CI/Workflow 运行状态和日志
- 创建、关闭或评论 Issues
- 创建或合并 Pull Requests
- 查询 GitHub API 获取仓库数据
- 列出仓库、发布版本、协作者

### ❌ 不适用场景

- 本地 git 操作 → 使用 `git` 直接
- 非 GitHub 仓库 → 使用其他 CLI
- 克隆仓库 → 使用 `git clone`
- 审查代码变更 → 使用 `coding-agent`

## 安装配置

```bash
# 认证（一次性）
gh auth login

# 验证
gh auth status
```

## 常用命令

### Pull Requests

```bash
# 列出 PR
gh pr list --repo owner/repo

# 检查 CI 状态
gh pr checks 55 --repo owner/repo

# 查看 PR 详情
gh pr view 55 --repo owner/repo

# 创建 PR
gh pr create --title "feat: add feature" --body "Description"

# 合并 PR
gh pr merge 55 --squash --repo owner/repo
```

### Issues

```bash
# 列出 Issues
gh issue list --repo owner/repo --state open

# 创建 Issue
gh issue create --title "Bug: something broken" --body "Details..."

# 关闭 Issue
gh issue close 42 --repo owner/repo
```

### CI/Workflow

```bash
# 列出最近运行
gh run list --repo owner/repo --limit 10

# 查看特定运行
gh run view <run-id> --repo owner/repo

# 查看失败步骤日志
gh run view <run-id> --repo owner/repo --log-failed

# 重新运行失败任务
gh run rerun <run-id> --failed --repo owner/repo
```

### API 查询

```bash
# 获取 PR 特定字段
gh api repos/owner/repo/pulls/55 --jq '.title, .state, .user.login'

# 列出所有标签
gh api repos/owner/repo/labels --jq '.[].name'

# 获取仓库统计
gh api repos/owner/repo --jq '{stars: .stargazers_count, forks: .forks_count}'
```

## 实用模板

### PR 审查摘要

```bash
PR=55 REPO=owner/repo
echo "## PR #$PR Summary"
gh pr view $PR --repo $REPO --json title,body,author,additions,deletions,changedFiles \
  --jq '"**\(.title)** by @\(.author.login)\n\n\(.body)\n\n📊 +\(.additions) -\(.deletions) across \(.changedFiles) files"'
gh pr checks $PR --repo $REPO
```

### Issue 分类视图

```bash
gh issue list --repo owner/repo --state open --json number,title,labels,createdAt \
  --jq '.[] | "[\(.number)] \(.title) - \([.labels[].name] | join(", ")) (\(.createdAt[:10]))"'
```

## 注意事项

- 不在 git 目录时需要指定 `--repo owner/repo`
- 可以直接使用 URL: `gh pr view https://github.com/owner/repo/pull/55`
- 有速率限制；重复查询使用 `gh api --cache 1h`

## 与其他 Skills 配合

| Skill | 配合场景 |
|-------|----------|
| `coding-agent` | 代码审查、自动修复 PR |
| `gh-issues` | 批量处理 GitHub Issues |
| `code` | 编码工作流集成 |

---

*由 ClawHub Lab Agent 分析*
