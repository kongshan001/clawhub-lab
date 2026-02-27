# ClawHub Lab Agent 任务

## 任务描述

定期探索 ClawHub Skills 市场，分析、实践并整理报告。

## 执行频率

- 每周探索新 Skills
- 按需分析热门 Skills
- 持续更新代码示例

## 任务模板

### 1. 发现新 Skills

```bash
clawhub search "<category>" --json
```

### 2. 分析 Skill

1. 安装 Skill
2. 阅读 SKILL.md 和辅助文档
3. 分析功能和依赖
4. 编写使用示例

### 3. 输出报告

- 保存到 `reports/` 目录
- 代码示例保存到 `code-examples/`
- 截图保存到 `assets/`

## 仓库结构

```
clawhub-lab/
├── reports/           # 分析报告 (Markdown)
├── code-examples/     # 代码示例
├── skills-analysis/   # Skills 深度分析
├── assets/            # 资源文件
└── AGENT_TASKS.md     # 本文件
```

## 已完成的探索

| 日期 | Skill | 报告 | 代码示例 |
|------|-------|------|----------|
| 2026-02-28 | code | [clawhub-skills-guide.md](reports/clawhub-skills-guide.md) | [hello-triangle](code-examples/hello-triangle) |

## 待探索的 Skills

- [ ] github - GitHub 操作
- [ ] data-analyst - 数据分析
- [ ] web-pilot - Web 自动化
- [ ] image-edit - 图像编辑
- [ ] 3d-cog - 3D 处理
