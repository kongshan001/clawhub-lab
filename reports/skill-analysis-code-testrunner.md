# ClawHub Skills 深度分析报告

> 📅 分析日期: 2026-02-28  
> 🎯 分析目标: `code` (v1.0.4) 和 `test-runner` (v1.0.0)  
> 🔍 分析方法: `clawhub inspect` 获取完整文件内容

---

## 一、分析背景

由于 ClawHub API 速率限制，无法直接安装新技能。但通过 `clawhub inspect` 命令成功获取了已安装技能的完整文件结构。

---

## 二、Code Skill 深度分析

### 2.1 基本信息

| 属性 | 值 |
|------|-----|
| 名称 | Code |
| Slug | code |
| 版本 | 1.0.4 |
| 作者 | ivangdavila |
| 创建时间 | 2026-02-11 |
| 最后更新 | 2026-02-26 |
| 标签 | emoji: 💻, 支持: linux/darwin/win32 |

### 2.2 文件结构

```
code/
├── SKILL.md           # 主文档 (3.1KB) - 核心定义和工作流
├── criteria.md        # 偏好存储标准 (1.1KB)
├── execution.md       # 执行指导 (857B)
├── memory-template.md # 记忆模板 (877B)
├── planning.md        # 规划参考 (633B)
├── state.md           # 状态跟踪 (1.3KB)
└── verification.md    # 验证参考 (874B)
```

### 2.3 核心架构

#### 工作流程
```
Request → Plan → Execute → Verify → Deliver
```

#### 目录结构
```
~/code/
  - memory.md    # 仅存储用户明确提供的偏好
```

### 2.4 设计理念

**安全边界（重要）**：
- ✅ 提供编码工作流指导
- ✅ 存储用户明确要求的偏好到 `~/code/`
- ✅ 读取包含的参考文件
- ❌ 不自动执行代码
- ❌ 不发起网络请求
- ❌ 不访问 `~/code/` 和用户项目之外的文件
- ❌ 不修改自身的 SKILL.md 或辅助文件
- ❌ 不在用户不知情的情况下采取自主行动

### 2.5 关键功能模块

#### 1. 规划模块 (planning.md)
```markdown
## 何时规划
- 多文件或多组件
- 部件之间有依赖
- UI 需要视觉验证
- 用户说 "build", "create", "implement"

## 步骤格式
Step N: [What]
- Output: [完成后存在什么]
- Test: [如何验证]
```

#### 2. 执行模块 (execution.md)
```markdown
## 推荐流程
1. 执行该步骤
2. 验证其工作
3. 向用户报告完成
4. 等待用户批准下一步

## 进度跟踪
- [DONE] Step 1 (completed)
- [WIP] Step 2 <- awaiting user approval
- [ ] Step 3
```

#### 3. 验证模块 (verification.md)
```markdown
## 截图
- 等待页面完全加载（无加载动画）
- 发送前自行审查
- 长页面分成 3-5 个部分（每个约 800px）

## 修复优先
如果截图显示问题：
1. 修复代码
2. 重新部署
3. 新截图
4. 仍然有问题？回到 1
5. 修复了？现在发送
```

#### 4. 状态跟踪模块 (state.md)
```markdown
## 请求跟踪
[R1] Build login page
[R2] Add dark mode
[R3] Fix header alignment

## 状态显示
[R1] [DONE] Done
[R2] [WIP] In progress
[R3] [Q] Queued
```

#### 5. 偏好存储模块 (criteria.md)
```markdown
## 何时保存（用户必须请求）
- "记住我更喜欢 X"
- "从现在开始总是做 Y"
- "保存这个偏好"

## 不保存的情况
- 用户没有明确要求保存
- 项目特定要求
- 一次性请求
```

### 2.6 设计亮点

1. **用户控制优先**: 所有操作需要用户明确批准
2. **最小权限原则**: 不访问外部网络，不修改自身文件
3. **透明度**: 用户始终知道正在发生什么
4. **模块化**: 每个功能有独立的参考文件
5. **安全性**: 明确的安全边界声明

---

## 三、Test-Runner Skill 深度分析

### 3.1 基本信息

| 属性 | 值 |
|------|-----|
| 名称 | Test Runner |
| Slug | test-runner |
| 版本 | 1.0.0 |
| 作者 | cmanfre7 |
| 创建时间 | 2026-01-28 |
| 文件大小 | 5KB |

### 3.2 支持的测试框架

| 语言 | 单元测试 | 集成测试 | E2E |
|------|----------|----------|-----|
| TypeScript/JS | Vitest (推荐), Jest | Supertest | Playwright |
| Python | pytest | pytest + httpx | Playwright |
| Swift | XCTest | XCTest | XCUITest |

### 3.3 TDD 工作流

```
┌─────────┐     ┌─────────┐     ┌──────────┐
│  Write   │────▶│  Write  │────▶│ Refactor │──┐
│  Test    │     │  Code   │     │  Code    │  │
│  (Red)   │     │ (Green) │     │          │  │
└─────────┘     └─────────┘     └──────────┘  │
     ▲                                          │
     └──────────────────────────────────────────┘
```

### 3.4 关键代码示例

#### Vitest 配置
```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
export default defineConfig({
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './tests/setup.ts',
  },
})
```

#### 测试模式 (AAA)
```typescript
test('calculates total with tax', () => {
  // Arrange
  const cart = new Cart([{ price: 100, qty: 2 }]);

  // Act
  const total = cart.totalWithTax(0.08);

  // Assert
  expect(total).toBe(216);
});
```

#### API 测试 (Python)
```python
@pytest.mark.asyncio
async def test_get_users():
    async with AsyncClient(app=app, base_url="http://test") as client:
        response = await client.get("/users")
    assert response.status_code == 200
```

### 3.5 覆盖率命令

```bash
# JavaScript/TypeScript
npx vitest --coverage
npx jest --coverage

# Python
pytest --cov=app --cov-report=html
pytest --cov=app --cov-fail-under=80  # 低于 80% 失败
```

---

## 四、Skills 结构对比分析

| 特性 | code | test-runner |
|------|------|-------------|
| 文件数量 | 7 | 1 |
| 主文档大小 | 3.1KB | 5KB |
| 模块化程度 | 高（6个辅助文件） | 低（单文件） |
| 代码示例 | 较少 | 丰富 |
| 工作流指导 | 详细 | 简洁 |
| 安全边界声明 | 详细 | 无 |

### 设计模式差异

**Code Skill**: 
- 采用"参考文档"模式
- 主文档定义核心规则
- 辅助文件提供详细指导
- 适合复杂工作流

**Test-Runner Skill**:
- 采用"速查表"模式
- 单文件包含所有信息
- 丰富的代码示例
- 适合快速参考

---

## 五、Skill 开发最佳实践

基于分析，总结 Skill 开发建议：

### 5.1 文档结构

```
my-skill/
├── SKILL.md           # 必须：主文档
├── quickstart.md      # 快速开始
├── reference.md       # 详细参考
├── examples.md        # 示例集合
└── security.md        # 安全声明
```

### 5.2 必须包含的内容

1. **何时使用**: 明确使用场景
2. **架构说明**: 目录结构和工作流
3. **安全边界**: 明确能做什么、不能做什么
4. **核心规则**: 简洁的行为准则
5. **常见陷阱**: 帮助用户避免错误

### 5.3 代码示例原则

- 可直接复制运行
- 包含必要的环境设置
- 覆盖常见用例
- 标注语言和框架版本

---

## 六、结论

### Code Skill 评价
- ⭐⭐⭐⭐⭐ 设计理念：用户控制、安全边界
- ⭐⭐⭐⭐☆ 模块化：清晰的文件分离
- ⭐⭐⭐☆☆ 代码示例：相对较少

### Test-Runner Skill 评价
- ⭐⭐⭐⭐⭐ 代码示例：丰富且实用
- ⭐⭐⭐⭐☆ 框架覆盖：主流语言全覆盖
- ⭐⭐⭐☆☆ 模块化：单文件结构

### 建议
1. Code Skill 可以增加更多代码示例
2. Test-Runner Skill 可以添加安全边界声明
3. 两种设计模式各有优势，可根据 Skill 复杂度选择

---

*报告由 ClawHub Explorer Agent 生成*
