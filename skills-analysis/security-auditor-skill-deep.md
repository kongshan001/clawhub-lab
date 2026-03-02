# Security Auditor Skill 深度分析报告

**分析日期**: 2026-03-02 15:02  
**Skill 版本**: 1.0.0  
**评分**: ⭐ 3.551  
**来源**: ClawHub Skills Market  
**适配来源**: buildwithclaude by Dave Poon (MIT License)

---

## 📋 概览

**Security Auditor** 是一个专业的应用安全审计和代码安全审查 Skill，专注于安全编码实践、漏洞检测和 OWASP 合规性。

### 核心定位
- **角色**: 高级应用安全工程师
- **专长**: 安全编码、漏洞检测、OWASP 合规
- **输出格式**: 结构化审计报告

---

## 🎯 适用场景

### ✅ 应该使用的场景
1. **代码安全审查** - 检查代码中的安全漏洞
2. **认证流程实现** - 设计和审查认证/授权机制
3. **OWASP 合规审计** - 确保 OWASP Top 10 合规
4. **安全头配置** - CORS/CSP/安全头设置
5. **密钥管理** - 处理 secrets 和敏感信息
6. **输入验证** - 防止注入攻击
7. **渗透测试准备** - 安全测试策略

### ⚠️ 不适用场景
- 功能性代码开发（专注于安全审查）
- 性能优化（虽然安全措施可能影响性能）
- UI/UX 设计

---

## 🔧 核心功能

### 1. OWASP Top 10 检查清单

#### A01:2021 - Broken Access Control（访问控制失效）

**检查要点**:
- ✅ 每个端点验证认证
- ✅ 每次数据访问验证授权
- ✅ CORS 配置（生产环境不使用 `*`）
- ✅ 禁用目录列表
- ✅ 敏感端点速率限制
- ✅ JWT 令牌验证

**示例代码**:
```typescript
// ✅ GOOD: 验证所有权
app.delete('/api/posts/:id', authenticate, async (req, res) => {
  const post = await db.post.findUnique({ where: { id: req.params.id } })
  if (!post) return res.status(404).json({ error: 'Not found' })
  if (post.authorId !== req.user.id && req.user.role !== 'admin') {
    return res.status(403).json({ error: 'Forbidden' })
  }
  await db.post.delete({ where: { id: req.params.id } })
  res.json({ success: true })
})
```

#### A02:2021 - Cryptographic Failures（加密失败）

**检查要点**:
- ✅ 密码使用 bcrypt（12+ rounds）或 argon2 哈希
- ✅ 敏感数据静态加密（AES-256）
- ✅ 强制 TLS/HTTPS
- ✅ 源代码和日志中无密钥
- ✅ API 密钥定期轮换
- ✅ API 响应排除敏感字段

**示例代码**:
```typescript
// ✅ GOOD: Bcrypt with sufficient rounds
import bcrypt from 'bcryptjs'
const hashedPassword = await bcrypt.hash(req.body.password, 12)
await db.user.create({ data: { password: hashedPassword } })
```

#### A03:2021 - Injection（注入）

**检查要点**:
- ✅ 所有数据库查询使用参数化语句或 ORM
- ✅ 查询中无字符串拼接
- ✅ OS 命令执行使用参数数组
- ✅ 防止 LDAP/XPath/NoSQL 注入
- ✅ 用户输入不用于 `eval()`/`Function()`

**示例代码**:
```typescript
// ✅ GOOD: 参数化查询
const user = await db.query('SELECT * FROM users WHERE email = $1', [email])

// ✅ GOOD: ORM 参数化输入
const user = await prisma.user.findUnique({ where: { email } })

// ✅ GOOD: 使用 execFile 而非 exec
import { execFile } from 'child_process'
execFile('ls', [sanitizedPath], callback)
```

#### A07:2021 - Cross-Site Scripting (XSS)

**检查要点**:
- ✅ 依赖 React 自动转义（避免 `dangerouslySetInnerHTML`）
- ✅ 必须渲染 HTML 时使用 DOMPurify 消毒
- ✅ 配置 CSP 头
- ✅ 会话令牌使用 HttpOnly cookies
- ✅ 渲染前验证 URL 参数

**示例代码**:
```typescript
// ✅ GOOD: HTML 消毒
import DOMPurify from 'isomorphic-dompurify'
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userComment) }} />

// ✅ BEST: 作为文本渲染（React 自动转义）
<div>{userComment}</div>
```

#### A05:2021 - Security Misconfiguration（安全配置错误）

**检查要点**:
- ✅ 修改默认凭据
- ✅ 生产环境错误消息不泄露堆栈跟踪
- ✅ 禁用不必要的 HTTP 方法
- ✅ 配置安全头
- ✅ 生产环境禁用调试模式
- ✅ 依赖项更新（`npm audit`）

---

### 2. 安全头配置

**完整的 Next.js 安全头配置**:
```typescript
// next.config.js
const securityHeaders = [
  { key: 'X-DNS-Prefetch-Control', value: 'on' },
  { key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains; preload' },
  { key: 'X-Frame-Options', value: 'SAMEORIGIN' },
  { key: 'X-Content-Type-Options', value: 'nosniff' },
  { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
  { key: 'Permissions-Policy', value: 'camera=(), microphone=(), geolocation=()' },
  {
    key: 'Content-Security-Policy',
    value: [
      "default-src 'self'",
      "script-src 'self' 'unsafe-eval' 'unsafe-inline'",
      "style-src 'self' 'unsafe-inline'",
      "img-src 'self' data: https:",
      "font-src 'self'",
      "connect-src 'self' https://api.example.com",
      "frame-ancestors 'none'",
      "base-uri 'self'",
      "form-action 'self'",
    ].join('; '),
  },
]

module.exports = {
  async headers() {
    return [{ source: '/(.*)', headers: securityHeaders }]
  },
}
```

**安全头说明**:
- **HSTS**: 强制 HTTPS，有效期 2 年
- **X-Frame-Options**: 防止点击劫持
- **X-Content-Type-Options**: 防止 MIME 类型嗅探
- **CSP**: 内容安全策略，防止 XSS 和数据注入
- **Permissions-Policy**: 限制浏览器功能访问

---

### 3. 输入验证模式

#### Zod Schema 验证

```typescript
import { z } from 'zod'

const userSchema = z.object({
  email: z.string().email().max(255),
  password: z.string().min(8).max(128),
  name: z.string().min(1).max(100).regex(/^[a-zA-Z\s'-]+$/),
  age: z.number().int().min(13).max(150).optional(),
})

// Server Action
export async function createUser(formData: FormData) {
  'use server'
  const parsed = userSchema.safeParse({
    email: formData.get('email'),
    password: formData.get('password'),
    name: formData.get('name'),
  })

  if (!parsed.success) {
    return { error: parsed.error.flatten() }
  }

  // Safe to use parsed.data
}
```

#### 文件上传验证

```typescript
const ALLOWED_TYPES = ['image/jpeg', 'image/png', 'image/webp']
const MAX_SIZE = 5 * 1024 * 1024 // 5MB

export async function uploadFile(formData: FormData) {
  'use server'
  const file = formData.get('file') as File

  if (!file || file.size === 0) return { error: 'No file' }
  if (!ALLOWED_TYPES.includes(file.type)) return { error: 'Invalid file type' }
  if (file.size > MAX_SIZE) return { error: 'File too large' }

  // Read and validate magic bytes, not just extension
  const bytes = new Uint8Array(await file.arrayBuffer())
  if (!validateMagicBytes(bytes, file.type)) return { error: 'File content mismatch' }
}
```

---

### 4. 认证安全

#### JWT 最佳实践

```typescript
import { SignJWT, jwtVerify } from 'jose'

const secret = new TextEncoder().encode(process.env.JWT_SECRET) // min 256-bit

export async function createToken(payload: { userId: string; role: string }) {
  return new SignJWT(payload)
    .setProtectedHeader({ alg: 'HS256' })
    .setIssuedAt()
    .setExpirationTime('15m')  // Short-lived access tokens
    .setAudience('your-app')
    .setIssuer('your-app')
    .sign(secret)
}

export async function verifyToken(token: string) {
  try {
    const { payload } = await jwtVerify(token, secret, {
      algorithms: ['HS256'],
      audience: 'your-app',
      issuer: 'your-app',
    })
    return payload
  } catch {
    return null
  }
}
```

**JWT 安全要点**:
- ✅ 使用 256-bit+ 密钥
- ✅ 短期访问令牌（15分钟）
- ✅ 设置 audience 和 issuer
- ✅ 验证时检查算法

#### Cookie 安全

```typescript
cookies().set('session', token, {
  httpOnly: true,     // 禁止 JavaScript 访问
  secure: true,       // 仅 HTTPS
  sameSite: 'lax',    // CSRF 保护
  maxAge: 60 * 60 * 24 * 7,
  path: '/',
})
```

#### 速率限制

```typescript
import { Ratelimit } from '@upstash/ratelimit'
import { Redis } from '@upstash/redis'

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, '10 s'),
})

// In middleware or route handler
const ip = request.headers.get('x-forwarded-for') ?? '127.0.0.1'
const { success, remaining } = await ratelimit.limit(ip)
if (!success) {
  return NextResponse.json({ error: 'Too many requests' }, { status: 429 })
}
```

---

### 5. 环境变量和密钥管理

**规则**:
- ❌ 永远不要提交 `.env` 文件
- ✅ 只提交 `.env.example` 和占位符
- ✅ 每个环境使用不同的密钥
- ✅ 定期轮换密钥
- ✅ 生产环境使用密钥管理器（Vault/AWS SSM/Doppler）
- ❌ 永远不要记录密钥或包含在错误响应中

**示例**:
```typescript
// ❌ BAD
const API_KEY = 'sk-1234567890abcdef'

// ✅ GOOD
const API_KEY = process.env.API_KEY
if (!API_KEY) throw new Error('API_KEY not configured')
```

---

### 6. 依赖安全

**定期审计命令**:
```bash
# 常规审计
npm audit
npm audit fix

# 检查已知漏洞
npx better-npm-audit audit

# 保持依赖更新
npx npm-check-updates -u
```

---

## 📊 安全审计报告格式

当进行审查时，输出格式如下：

```
## Security Audit Report

### Critical (Must Fix)
1. **[A03:Injection]** SQL injection in `/api/search` — user input concatenated into query
   - File: `app/api/search/route.ts:15`
   - Fix: Use parameterized query
   - Risk: Full database compromise

### High (Should Fix)
1. **[A01:Access Control]** Missing auth check on DELETE endpoint
   - File: `app/api/posts/[id]/route.ts:42`
   - Fix: Add authentication middleware and ownership check

### Medium (Recommended)
1. **[A05:Misconfiguration]** Missing security headers
   - Fix: Add CSP, HSTS, X-Frame-Options headers

### Low (Consider)
1. **[A06:Vulnerable Components]** 3 packages with known vulnerabilities
   - Run: `npm audit fix`
```

---

## 🛡️ 受保护的文件模式

这些文件在修改前应仔细审查：
- `.env*` — 环境密钥
- `auth.ts` / `auth.config.ts` — 认证配置
- `middleware.ts` — 路由保护逻辑
- `**/api/auth/**` — 认证端点
- `prisma/schema.prisma` — 数据库 schema（权限、RLS）
- `next.config.*` — 安全头、重定向
- `package.json` / `package-lock.json` — 依赖变更

---

## 💡 实践建议

### 1. 防御深度原则
- 多层安全防护
- 不依赖单一安全措施
- 每一层都应能独立防御

### 2. 最小权限原则
- 所有访问控制遵循最小权限
- 默认拒绝，显式允许
- 定期审查权限

### 3. 永不信任用户输入
- 严格验证所有输入
- 使用类型安全的验证库（Zod）
- 消毒后再使用

### 4. 安全失败设计
- 系统失败时不泄露信息
- 错误消息通用化
- 记录详细错误到日志，不返回给用户

### 5. 定期依赖扫描
- 使用 `npm audit` 定期检查
- 及时更新有漏洞的依赖
- 使用 Dependabot 自动化

---

## 🎓 学习路径

### 初级
1. 理解 OWASP Top 10
2. 学习基本的安全头配置
3. 掌握输入验证
4. 了解 XSS 和 SQL 注入

### 中级
1. 实现认证/授权流程
2. 配置 CSP 策略
3. 使用速率限制
4. 密钥管理最佳实践

### 高级
1. 渗透测试准备
2. 安全架构设计
3. 依赖安全审计
4. 安全监控和告警

---

## 🔗 相关 Skills

- **healthcheck** - 主机安全加固和风险容忍配置
- **secure-api-calls** - Keychains 凭证代理
- **github** - GitHub 操作（CI/CD 安全）
- **coding-agent** - 代码生成（结合安全审查）

---

## 📈 评分分析

**总评分**: ⭐ 3.551

**评分维度推测**:
- **实用性**: ⭐⭐⭐⭐⭐ (5/5) - OWASP Top 10 覆盖全面
- **代码质量**: ⭐⭐⭐⭐⭐ (5/5) - 示例代码清晰、现代
- **文档完整**: ⭐⭐⭐⭐⭐ (5/5) - 详细的安全检查清单
- **易用性**: ⭐⭐⭐⭐ (4/5) - 需要安全背景知识
- **创新性**: ⭐⭐⭐ (3/5) - 基于成熟的 OWASP 标准

---

## ⚠️ 注意事项

1. **需要安全背景** - 使用此 Skill 需要一定的安全知识
2. **持续更新** - OWASP Top 10 会定期更新，需保持同步
3. **上下文相关** - 某些安全措施可能影响性能，需权衡
4. **不是银弹** - 安全审计不能替代渗透测试

---

## 🚀 最佳实践

### 1. 集成到开发流程
```bash
# pre-commit hook
npm audit
npx tsc --noEmit

# CI/CD pipeline
- Security audit
- Dependency check
- SAST (Static Application Security Testing)
```

### 2. 定期审查
- 每月进行安全审计
- 季度依赖更新
- 年度渗透测试

### 3. 团队培训
- OWASP Top 10 培训
- 安全编码工作坊
- 安全事件复盘

---

## 📝 总结

**Security Auditor** 是一个专业、全面的安全审计 Skill，适合：

✅ **推荐使用**:
- Web 应用安全审查
- API 安全审计
- 认证/授权流程设计
- 安全合规检查
- 渗透测试准备

⚠️ **需要注意**:
- 需要一定的安全背景知识
- 不是自动化工具，需要人工判断
- 应结合其他安全工具使用

---

**分析完成时间**: 2026-03-02 15:02  
**分析者**: Glint 🔺  
**安装状态**: ✅ 已成功安装  
**文档质量**: ⭐⭐⭐⭐⭐  
**推荐指数**: ⭐⭐⭐⭐⭐

---

*愿你的代码安全，愿你的系统坚固。* 🔒
