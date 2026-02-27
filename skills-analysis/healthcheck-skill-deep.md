# Healthcheck Skill 深度分析

> 📅 分析日期: 2026-02-28
> ⭐ 评分: N/A (OpenClaw 内置)
> 🔗 来源: OpenClaw 内置 Skill

---

## 基本信息

| 属性 | 值 |
|------|-----|
| 名称 | healthcheck |
| 功能 | 主机安全加固和风险容忍度配置 |
| 适用 | 安全审计、防火墙/SSH/更新加固 |

## 核心功能

### 安全评估

- 操作系统和版本检测
- 权限级别检查
- 访问路径分析（本地/SSH/RDP/tailnet）
- 网络暴露评估
- OpenClaw 网关状态
- 备份系统状态
- 磁盘加密状态
- 自动安全更新状态

### OpenClaw 安全审计

```bash
# 快速审计
openclaw security audit

# 深度审计
openclaw security audit --deep

# JSON 输出
openclaw security audit --json

# 应用安全默认值
openclaw security audit --fix
```

### 版本检查

```bash
openclaw update status
```

## 风险配置文件

| 配置 | 描述 |
|------|------|
| Home/Workstation Balanced | 防火墙开启，远程访问限制到 LAN/tailnet |
| VPS Hardened | 拒绝入站，最小端口，仅密钥 SSH |
| Developer Convenience | 允许更多本地服务，有暴露警告 |
| Custom | 用户定义约束 |

## 工作流程

```
1. 模型自检 → 推荐高性能模型
2. 环境上下文 → 只读检查
3. 安全审计 → openclaw security audit
4. 版本状态 → openclaw update status
5. 风险容忍度 → 用户选择配置
6. 修复计划 → 显示详细步骤
7. 执行选项 → 用户选择执行方式
8. 验证报告 → 重新检查并报告
```

## Linux 检查命令

```bash
# OS 信息
uname -a
cat /etc/os-release

# 监听端口
ss -ltnup

# 防火墙状态
ufw status
firewall-cmd --state
nft list ruleset
```

## macOS 检查命令

```bash
# 监听端口
lsof -nP -iTCP -sTCP:LISTEN

# 防火墙状态
/usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate
pfctl -s info

# Time Machine 备份
tmutil status
```

## 定期检查调度

```bash
# 列出现有 cron
openclaw cron list

# 添加安全审计定时任务
openclaw cron add --name healthcheck:security-audit ...

# 添加版本检查定时任务
openclaw cron add --name healthcheck:update-status ...
```

## 必需确认项

执行前必须获得明确批准：

- 防火墙规则变更
- 开启/关闭端口
- SSH/RDP 配置变更
- 安装/删除软件包
- 启用/禁用服务
- 用户/组修改
- 调度任务或启动持久化
- 更新策略变更
- 访问敏感文件或凭证

## 日志和审计跟踪

记录内容：
- 网关身份和角色
- 计划 ID 和时间戳
- 批准的步骤和命令
- 退出码和修改的文件

⚠️ 永远不要记录令牌或完整凭证内容

## 最佳实践

1. **推荐高级模型**：Opus 4.5, GPT 5.2+
2. **明确批准**：任何状态变更前确认
3. **可逆变更**：有回滚计划
4. **访问保护**：确认不会锁定用户
5. **系统备份**：尝试验证备份状态

---

*由 ClawHub Lab Agent 分析*
