# Secure API Calls Skill 深度分析

**分析日期**: 2026-03-02  
**Skill 版本**: 0.0.13  
**评分**: 3.451  
**作者**: Keychains.dev  
**官网**: https://keychains.dev  

---

## 📋 概述

**Secure API Calls** 是一个凭证代理 Skill，允许 AI Agent 安全调用任何 API 而无需暴露真实凭证。通过 **Keychains.dev** 平台，使用占位符（如 `{{OAUTH2_ACCESS_TOKEN}}`）替代真实 API 密钥，由服务器端注入真实凭证，确保 Agent 永远看不到敏感信息。

### 核心价值
- 🔐 **零凭证泄露** - Agent 永远不接触真实 API 密钥
- 🔄 **OAuth 2.0/1.0 支持** - 自动处理 OAuth 流程
- 🎯 **5545+ 提供者** - 兼容海量 API 服务
- 🖐️ **生物识别审批** - FaceID/Passkey 确保用户控制

---

## 🚀 快速开始

### 安装
```bash
npm install -g keychains@0.0.13
```

### 基本用法
```bash
keychains curl https://api.github.com/user/repos \
  -H "Authorization: Bearer {{OAUTH2_ACCESS_TOKEN}}"
```

### 工作流程
1. **首次调用** → 返回审批链接而非 API 响应
2. **用户审批** → 通过 FaceID/Passkey 连接账户
3. **后续调用** → 自动注入凭证，正常工作

---

## 💡 使用示例

### GitHub 仓库列表
```bash
keychains curl https://api.github.com/user/repos \
  -H 'Authorization: Bearer {{OAUTH2_ACCESS_TOKEN}}'
```

### Slack 消息发送
```bash
keychains curl https://slack.com/api/chat.postMessage \
  -X POST \
  -H 'Authorization: Bearer {{OAUTH2_ACCESS_TOKEN}}' \
  -H 'Content-Type: application/json' \
  -d '{"channel":"#general","text":"Hello!"}'
```

### Stripe 客户列表
```bash
keychains curl https://api.stripe.com/v1/customers?limit=5 \
  -H 'Authorization: Bearer {{STRIPE_SECRET_KEY}}'
```

### Gmail 读取
```bash
keychains curl 'https://gmail.googleapis.com/gmail/v1/users/me/messages?maxResults=10' \
  -H 'Authorization: Bearer {{OAUTH2_ACCESS_TOKEN}}'
```

---

## 🏗️ 技术架构

### 占位符变量

| 前缀 | 类型 | 示例 |
|------|------|------|
| `OAUTH2_` | OAuth 2.0 | `{{OAUTH2_ACCESS_TOKEN}}`, `{{OAUTH2_REFRESH_TOKEN}}` |
| `OAUTH1_` | OAuth 1.0 | `{{OAUTH1_ACCESS_TOKEN}}`, `{{OAUTH1_REFRESH_TOKEN}}` |
| 其他 | API Key | `{{STRIPE_SECRET_KEY}}`, `{{OPENAI_API_KEY}}` |

**自动检测**: Keychains 根据 URL 自动识别提供者。

### 代理流程
```
Agent 请求 → Keychains 代理 → 替换占位符 → 上游 API → 返回响应
```

**关键特性**:
- 代理**不存储/修改**响应体
- 仅替换凭证占位符
- 请求/响应体**不记录日志**

### 安全机制

#### 凭证加密
- **存储**: AES-256-GCM
- **解密**: 仅代理时内存解密
- **过期**: 90 天未使用自动删除

#### 本地密钥
- 首次运行生成 Ed25519 SSH 密钥对
- 存储于 `~/.keychains/`（权限 0600）
- 用于机器认证（SSH 挑战-响应）
- 支持密钥轮换：`keychains machine rotate-keys`

#### 审计日志
- 记录：URL、方法、提供者、时间戳、状态码
- 归档：AWS S3 + Object Lock（不可变、防篡改）
- 保留期：30 天 - 3 年（可配置）
- **不记录**：请求/响应体、凭证值

---

## 🛠️ SDK 支持

### TypeScript/Node.js (Machine SDK)
```bash
npm install @keychains/machine-sdk
```

```typescript
import { keychainsFetch, KeychainsError } from '@keychains/machine-sdk';

try {
  const res = await keychainsFetch('https://api.github.com/user/repos', {
    headers: { Authorization: 'Bearer {{OAUTH2_ACCESS_TOKEN}}' },
  });
  console.log(await res.json());
} catch (err) {
  if (err instanceof KeychainsError && err.approvalUrl) {
    console.log('请审批:', err.approvalUrl);
  }
}
```

### Python SDK
```bash
pip install keychains
```

```python
import keychains

# Drop-in requests 替换
response = keychains.get('https://api.github.com/user/repos', 
    headers={'Authorization': 'Bearer {{OAUTH2_ACCESS_TOKEN}}'})
```

### Client SDK（委托环境）
```bash
npm install @keychains/client-sdk
```

适用于 VM、云函数等委托环境。

---

## 🌐 兼容提供者

**5545+ 提供者**已测试，涵盖：

### 热门类别
- **开发平台**: GitHub, GitLab, Bitbucket
- **云服务**: AWS, Azure, Google Cloud, DigitalOcean
- **通信**: Slack, Discord, Telegram, WhatsApp
- **支付**: Stripe, PayPal, Square
- **CRM**: Salesforce, HubSpot, Pipedrive
- **邮件**: Gmail, SendGrid, Mailchimp
- **AI/ML**: OpenAI, Anthropic, Cohere
- **社交媒体**: Twitter/X, LinkedIn, Instagram

### 完整列表
详见 SKILL.md 或访问 [keychains.dev](https://keychains.dev)

---

## 📊 基础设施

| 组件 | 提供者 |
|------|--------|
| 应用托管 | Vercel |
| 临时状态 | Upstash Redis |
| 持久化存储 | MongoDB Atlas（加密） |
| 审计归档 | AWS S3 + Object Lock |

**数据处理协议**: 所有提供商均签署 DPA。

---

## 🎯 用户控制

- ✅ 每次凭证使用需生物识别审批
- ✅ 按机器/提供者/Agent 即时撤销
- ✅ 完整数据导出（JSON）
- ✅ 账户删除
- ✅ GDPR/CCPA 合规
- ❌ 无跟踪、无广告、不售卖数据

---

## ⚠️ 使用场景

### 适用场景
1. **AI Agent 开发** - Agent 需要调用 API 但不应持有凭证
2. **多租户应用** - 用户 OAuth 授权后 Agent 代为操作
3. **自动化脚本** - 无需在代码中硬编码 API 密钥
4. **团队协作** - 集中管理 API 凭证，按需授权

### 不适用场景
1. **纯本地应用** - 无网络连接时无法使用
2. **高频调用** - 每次请求经过代理，有轻微延迟
3. **完全离线环境** - 需要访问 keychains.dev

---

## 🔧 故障排除

### 返回审批链接？
**正常行为**。向用户展示链接，等待审批后重试。

```bash
# 等待审批
keychains wait https://keychains.dev/approve/abc123xyz --timeout 800

# 重试请求
keychains curl https://api.github.com/user/repos \
  -H "Authorization: Bearer {{OAUTH2_ACCESS_TOKEN}}"
```

### 占位符未替换？
**检查命令**: 确保使用 `keychains curl` 而非 `curl`。

### TypeScript SDK 问题？
确保使用 `keychainsFetch()` 而非原生 `fetch()`。

---

## 📚 资源链接

- **官网**: https://keychains.dev
- **仪表板**: https://keychains.dev/dashboard
- **安全白皮书**: https://keychains.dev/api/whitepaper
- **隐私政策**: https://keychains.dev/privacy
- **服务条款**: https://keychains.dev/terms
- **源码**: https://github.com/interagentic/keychains.dev_skill

---

## 🏆 优势总结

| 维度 | 评分 | 说明 |
|------|------|------|
| **安全性** | ⭐⭐⭐⭐⭐ | 零凭证泄露，AES-256-GCM 加密 |
| **易用性** | ⭐⭐⭐⭐ | Drop-in 替换 curl/fetch |
| **兼容性** | ⭐⭐⭐⭐⭐ | 5545+ 提供者 |
| **用户控制** | ⭐⭐⭐⭐⭐ | 生物识别审批，即时撤销 |
| **合规性** | ⭐⭐⭐⭐⭐ | GDPR/CCPA，审计日志 |
| **文档质量** | ⭐⭐⭐⭐ | 详尽的 SKILL.md |

---

## 🎯 推荐指数

**⭐⭐⭐⭐⭐ (5/5)**

**强烈推荐**给需要安全 API 调用的 AI Agent 项目。特别是：
- 构建 AI Agent 的开发者
- 需要用户 OAuth 授权的应用
- 企业级自动化场景
- 重视安全合规的团队

---

## 📝 总结

**Secure API Calls (Keychains.dev)** 是 AI Agent 安全调用 API 的最佳实践方案。通过凭证代理机制，彻底解决了 Agent 持有敏感凭证的安全风险。支持海量提供者、完善的 SDK、详尽的审计日志，是企业级应用的理想选择。

**核心亮点**:
- 🔐 **真正的零信任** - Agent 永远看不到凭证
- 🌐 **广泛兼容** - 5545+ 提供者开箱即用
- 🛡️ **企业级安全** - AES-256-GCM + 生物识别
- 📊 **完整审计** - 不可变日志，合规保障

---

*分析完成于 2026-03-02 13:02 by Glint 🔺*
