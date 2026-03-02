# Database Operations Skill - 深度分析报告

**分析日期**: 2026-03-02 20:04  
**Skill 版本**: 1.0.0  
**评分**: 3.508  
**来源**: ClawHub Marketplace  
**作者**: Dave Poon (adapted from buildwithclaude)  
**许可证**: MIT

---

## 📋 概述

**Database Operations** 是一个全面的数据库设计、迁移和优化专家 Skill，专注于 PostgreSQL、查询性能、模式设计和 EF Core 迁移。核心理念是"先测量，后优化，始终计划回滚"。

### 核心定位
- **角色**: 数据库优化专家
- **范围**: 实现层（implementation）
- **输出格式**: 代码（SQL、C#、TypeScript）
- **专业领域**: PostgreSQL, EF Core, Redis, 查询优化

---

## 🎯 触发条件

自动触发关键词：
- `database`, `schema`, `migration`, `SQL`
- `query optimization`, `index`, `PostgreSQL`, `Postgres`
- `N+1`, `slow query`, `EXPLAIN`
- `partitioning`, `caching`, `Redis`
- `connection pool`, `EF Core migration`, `database design`

---

## 🔧 核心功能

### 1. 模式设计模式（Schema Design Patterns）

#### 用户管理表
```sql
CREATE TYPE user_status AS ENUM ('active', 'inactive', 'suspended', 'pending');

CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  username VARCHAR(50) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  first_name VARCHAR(100) NOT NULL,
  last_name VARCHAR(100) NOT NULL,
  status user_status DEFAULT 'active',
  email_verified BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  deleted_at TIMESTAMPTZ,  -- 软删除
  
  CONSTRAINT users_email_format CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$'),
  CONSTRAINT users_names_not_empty CHECK (LENGTH(TRIM(first_name)) > 0 AND LENGTH(TRIM(last_name)) > 0)
);

-- 策略性索引
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_status ON users(status) WHERE status != 'active';
CREATE INDEX idx_users_created_at ON users(created_at);
CREATE INDEX idx_users_deleted_at ON users(deleted_at) WHERE deleted_at IS NULL;
```

**特点**：
- ✅ 使用 ENUM 类型约束状态
- ✅ 软删除模式（deleted_at）
- ✅ 邮箱格式验证约束
- ✅ 部分索引（WHERE 子句）
- ✅ 时区感知时间戳（TIMESTAMPTZ）

#### 审计日志表
```sql
CREATE TYPE audit_operation AS ENUM ('INSERT', 'UPDATE', 'DELETE');

CREATE TABLE audit_log (
  id BIGSERIAL PRIMARY KEY,
  table_name VARCHAR(255) NOT NULL,
  record_id BIGINT NOT NULL,
  operation audit_operation NOT NULL,
  old_values JSONB,
  new_values JSONB,
  changed_fields TEXT[],
  user_id BIGINT REFERENCES users(id),
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);

-- 自动审计触发器
CREATE OR REPLACE FUNCTION audit_trigger_function()
RETURNS TRIGGER AS $$
BEGIN
  IF TG_OP = 'DELETE' THEN
    INSERT INTO audit_log (table_name, record_id, operation, old_values)
    VALUES (TG_TABLE_NAME, OLD.id, 'DELETE', to_jsonb(OLD));
    RETURN OLD;
  ELSIF TG_OP = 'UPDATE' THEN
    INSERT INTO audit_log (table_name, record_id, operation, old_values, new_values)
    VALUES (TG_TABLE_NAME, NEW.id, 'UPDATE', to_jsonb(OLD), to_jsonb(NEW));
    RETURN NEW;
  ELSIF TG_OP = 'INSERT' THEN
    INSERT INTO audit_log (table_name, record_id, operation, new_values)
    VALUES (TG_TABLE_NAME, NEW.id, 'INSERT', to_jsonb(NEW));
    RETURN NEW;
  END IF;
END;
$$ LANGUAGE plpgsql;
```

**特点**：
- ✅ 使用 JSONB 存储变更前后值
- ✅ 通用触发器函数（适用于任何表）
- ✅ 记录变更字段列表

#### 软删除模式
```sql
-- 查询过滤视图
CREATE VIEW active_users AS SELECT * FROM users WHERE deleted_at IS NULL;

-- 软删除函数
CREATE OR REPLACE FUNCTION soft_delete(p_table TEXT, p_id BIGINT)
RETURNS VOID AS $$
BEGIN
  EXECUTE format('UPDATE %I SET deleted_at = CURRENT_TIMESTAMP WHERE id = $1 AND deleted_at IS NULL', p_table)
  USING p_id;
END;
$$ LANGUAGE plpgsql;
```

**特点**：
- ✅ 视图简化查询
- ✅ 动态 SQL 函数（适用于任何表）
- ✅ 防止重复删除

#### 全文搜索
```sql
ALTER TABLE products ADD COLUMN search_vector tsvector
  GENERATED ALWAYS AS (
    to_tsvector('english', COALESCE(name, '') || ' ' || COALESCE(description, '') || ' ' || COALESCE(sku, ''))
  ) STORED;

CREATE INDEX idx_products_search ON products USING gin(search_vector);

-- 查询
SELECT * FROM products
WHERE search_vector @@ to_tsquery('english', 'laptop & gaming');
```

**特点**：
- ✅ 生成列（自动维护）
- ✅ GIN 索引加速
- ✅ 多字段合并搜索

---

### 2. 查询优化（Query Optimization）

#### 分析工具
```sql
-- 永远从这里开始
EXPLAIN (ANALYZE, BUFFERS, FORMAT TEXT)
SELECT u.id, u.name, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.created_at > '2024-01-01'
GROUP BY u.id, u.name
ORDER BY order_count DESC;
```

#### 索引策略

| 索引类型 | 用途 | 示例 |
|---------|------|------|
| 单列索引 | 精确查找 | `CREATE INDEX idx_users_email ON users(email)` |
| 复合索引 | 多列查询（顺序重要！）| `CREATE INDEX idx_orders_user_status ON orders(user_id, status, created_at)` |
| 部分索引 | 过滤查询 | `CREATE INDEX idx_products_low_stock ON products(inventory_quantity) WHERE inventory_tracking = true AND inventory_quantity <= 5` |
| 覆盖索引 | 避免表查找 | `CREATE INDEX idx_orders_covering ON orders(user_id, status) INCLUDE (total, created_at)` |
| GIN 索引 | JSONB 查询 | `CREATE INDEX idx_products_attrs ON products USING gin(attributes)` |
| 表达式索引 | 函数查询 | `CREATE INDEX idx_users_email_lower ON users(lower(email))` |

#### 查找未使用的索引
```sql
SELECT
  schemaname, tablename, indexname,
  idx_scan as scans,
  pg_size_pretty(pg_relation_size(indexrelid)) as size
FROM pg_stat_user_indexes
WHERE idx_scan = 0
ORDER BY pg_relation_size(indexrelid) DESC;
```

#### 查找缺失索引（慢查询）
```sql
-- 首先启用 pg_stat_statements
SELECT query, calls, total_exec_time, mean_exec_time, rows
FROM pg_stat_statements
WHERE mean_exec_time > 100  -- ms
ORDER BY total_exec_time DESC
LIMIT 20;
```

#### N+1 查询检测
```sql
-- 在 pg_stat_statements 中查找重复的相似查询
SELECT query, calls, mean_exec_time
FROM pg_stat_statements
WHERE calls > 100 AND query LIKE '%WHERE%id = $1%'
ORDER BY calls DESC;
```

---

### 3. 迁移模式（Migration Patterns）

#### 安全添加列
```sql
-- +migrate Up
-- 生产环境总是使用 CONCURRENTLY 创建索引
ALTER TABLE users ADD COLUMN phone VARCHAR(20);
CREATE INDEX CONCURRENTLY idx_users_phone ON users(phone) WHERE phone IS NOT NULL;

-- +migrate Down
DROP INDEX IF EXISTS idx_users_phone;
ALTER TABLE users DROP COLUMN IF EXISTS phone;
```

#### 零停机重命名列
```sql
-- 步骤 1: 添加新列
ALTER TABLE users ADD COLUMN display_name VARCHAR(100);
UPDATE users SET display_name = name;
ALTER TABLE users ALTER COLUMN display_name SET NOT NULL;

-- 步骤 2: 部署同时写入两列的代码
-- 步骤 3: 部署从新列读取的代码
-- 步骤 4: 删除旧列
ALTER TABLE users DROP COLUMN name;
```

#### 表分区
```sql
-- 创建分区表
CREATE TABLE orders (
  id BIGSERIAL,
  user_id BIGINT NOT NULL,
  total DECIMAL(10,2),
  created_at TIMESTAMPTZ NOT NULL,
  PRIMARY KEY (id, created_at)
) PARTITION BY RANGE (created_at);

-- 月度分区
CREATE TABLE orders_2024_01 PARTITION OF orders
  FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
CREATE TABLE orders_2024_02 PARTITION OF orders
  FOR VALUES FROM ('2024-02-01') TO ('2024-03-01');

-- 自动创建分区
CREATE OR REPLACE FUNCTION create_monthly_partition(p_table TEXT, p_date DATE)
RETURNS VOID AS $$
DECLARE
  partition_name TEXT := p_table || '_' || to_char(p_date, 'YYYY_MM');
  next_date DATE := p_date + INTERVAL '1 month';
BEGIN
  EXECUTE format(
    'CREATE TABLE IF NOT EXISTS %I PARTITION OF %I FOR VALUES FROM (%L) TO (%L)',
    partition_name, p_table, p_date, next_date
  );
END;
$$ LANGUAGE plpgsql;
```

---

### 4. EF Core 迁移（.NET）

#### 创建和应用
```bash
# 添加迁移
dotnet ef migrations add AddPhoneToUsers -p src/Infrastructure -s src/Api

# 应用
dotnet ef database update -p src/Infrastructure -s src/Api

# 生成幂等 SQL 脚本（生产环境）
dotnet ef migrations script -p src/Infrastructure -s src/Api -o migration.sql --idempotent

# 回滚
dotnet ef database update PreviousMigrationName -p src/Infrastructure -s src/Api
```

#### EF Core 最佳实践
```csharp
// 只读查询使用 AsNoTracking
var users = await _db.Users
    .AsNoTracking()
    .Where(u => u.Status == UserStatus.Active)
    .Select(u => new UserDto { Id = u.Id, Name = u.Name })
    .ToListAsync(ct);

// 避免 N+1：使用 Include
var orders = await _db.Orders
    .Include(o => o.Items)
    .ThenInclude(i => i.Product)
    .Where(o => o.UserId == userId)
    .ToListAsync(ct);

// 更好：投影（Projection）
var orders = await _db.Orders
    .Where(o => o.UserId == userId)
    .Select(o => new OrderDto
    {
        Id = o.Id,
        Total = o.Total,
        Items = o.Items.Select(i => new OrderItemDto
        {
            ProductName = i.Product.Name,
            Quantity = i.Quantity,
        }).ToList(),
    })
    .ToListAsync(ct);
```

---

### 5. 缓存策略（Caching Strategy）

#### Redis 查询缓存
```typescript
import Redis from 'ioredis'

const redis = new Redis(process.env.REDIS_URL)

async function cachedQuery<T>(
  key: string,
  queryFn: () => Promise<T>,
  ttlSeconds: number = 300
): Promise<T> {
  const cached = await redis.get(key)
  if (cached) return JSON.parse(cached)

  const result = await queryFn()
  await redis.setex(key, ttlSeconds, JSON.stringify(result))
  return result
}

// 使用
const products = await cachedQuery(
  `products:category:${categoryId}:page:${page}`,
  () => db.product.findMany({ where: { categoryId }, skip, take }),
  300 // 5 分钟
)

// 失效
async function invalidateProductCache(categoryId: string) {
  const keys = await redis.keys(`products:category:${categoryId}:*`)
  if (keys.length) await redis.del(...keys)
}
```

#### 物化视图
```sql
CREATE MATERIALIZED VIEW monthly_sales AS
SELECT
  DATE_TRUNC('month', created_at) as month,
  category_id,
  COUNT(*) as order_count,
  SUM(total) as revenue,
  AVG(total) as avg_order_value
FROM orders
WHERE created_at >= DATE_TRUNC('year', CURRENT_DATE)
GROUP BY 1, 2;

CREATE UNIQUE INDEX idx_monthly_sales ON monthly_sales(month, category_id);

-- 刷新（可通过 pg_cron 调度）
REFRESH MATERIALIZED VIEW CONCURRENTLY monthly_sales;
```

---

### 6. 连接池配置

#### Node.js (pg)
```typescript
import { Pool } from 'pg'

const pool = new Pool({
  max: 20,                      // 最大连接数
  idleTimeoutMillis: 30000,     // 30s 后关闭空闲连接
  connectionTimeoutMillis: 2000, // 2s 内无法连接则快速失败
  maxUses: 7500,                // N 次使用后刷新连接
})

// 监控池健康
setInterval(() => {
  console.log({
    total: pool.totalCount,
    idle: pool.idleCount,
    waiting: pool.waitingCount,
  })
}, 60000)
```

---

### 7. 监控查询

#### 活跃连接
```sql
SELECT count(*), state
FROM pg_stat_activity
WHERE datname = current_database()
GROUP BY state;
```

#### 长时间运行查询
```sql
SELECT pid, now() - query_start AS duration, query, state
FROM pg_stat_activity
WHERE (now() - query_start) > interval '5 minutes'
AND state = 'active';
```

#### 表大小
```sql
SELECT
  relname AS table,
  pg_size_pretty(pg_total_relation_size(relid)) AS total_size,
  pg_size_pretty(pg_relation_size(relid)) AS data_size,
  pg_size_pretty(pg_total_relation_size(relid) - pg_relation_size(relid)) AS index_size
FROM pg_catalog.pg_statio_user_tables
ORDER BY pg_total_relation_size(relid) DESC
LIMIT 20;
```

#### 表膨胀
```sql
SELECT
  tablename,
  pg_size_pretty(pg_total_relation_size(tablename::regclass)) as size,
  n_dead_tup,
  n_live_tup,
  CASE WHEN n_live_tup > 0
    THEN round(n_dead_tup::numeric / n_live_tup, 2)
    ELSE 0
  END as dead_ratio
FROM pg_stat_user_tables
WHERE n_dead_tup > 1000
ORDER BY dead_ratio DESC;
```

---

## ⚠️ 反模式（Anti-Patterns）

1. ❌ `SELECT *` — 总是指定需要的列
2. ❌ 外键缺少索引 — 总是索引 FK 列
3. ❌ `LIKE '%search%'` — 使用全文搜索或 trigram 索引
4. ❌ 大型 `IN` 子句 — 使用 `ANY(ARRAY[...])` 或 JOIN 值列表
5. ❌ 无限制查询没有 `LIMIT` — 总是分页
6. ❌ 生产环境不使用 `CONCURRENTLY` 创建索引
7. ❌ 运行迁移没有测试回滚
8. ❌ 忽略 `EXPLAIN ANALYZE` 输出 — 总是验证执行计划
9. ❌ 使用 `FLOAT` 存储金额 — 使用 `DECIMAL(10,2)` 或整数分
10. ❌ 缺少 `NOT NULL` 约束 — 明确可空性

---

## 🎯 适用场景

### 最佳场景
1. ✅ **PostgreSQL 数据库设计** — 提供完整的模式模板
2. ✅ **查询性能优化** — 系统化的优化流程
3. ✅ **EF Core 迁移** — .NET 技术栈集成
4. ✅ **零停机迁移** — 大规模生产环境
5. ✅ **数据库监控** — 性能指标追踪

### 不适用场景
1. ❌ **NoSQL 数据库** — 仅支持关系型数据库
2. ❌ **MySQL 优化** — 部分 PostgreSQL 特性不适用
3. ❌ **图形数据库** — 不涉及 Neo4j 等

---

## 📊 技术栈

| 技术 | 支持程度 | 用途 |
|------|---------|------|
| PostgreSQL | ⭐⭐⭐⭐⭐ | 主要数据库 |
| EF Core | ⭐⭐⭐⭐⭐ | .NET ORM |
| Redis | ⭐⭐⭐⭐ | 缓存层 |
| Node.js pg | ⭐⭐⭐⭐ | JavaScript 客户端 |
| TypeScript | ⭐⭐⭐⭐ | 类型安全 |

---

## 🏆 优势

1. **全面性** — 涵盖从设计到监控的完整生命周期
2. **最佳实践** — 所有代码都遵循生产级标准
3. **多语言支持** — SQL、C#、TypeScript 示例
4. **可操作性强** — 提供可直接使用的代码模板
5. **性能导向** — 强调测量和验证

---

## ⚡ 快速开始

### 典型使用流程

1. **设计模式** → 选择合适的表结构（users、audit_log）
2. **查询优化** → 使用 `EXPLAIN ANALYZE` 分析慢查询
3. **创建索引** → 根据查询模式创建复合/部分/覆盖索引
4. **迁移部署** → 使用零停机模式逐步发布
5. **监控告警** → 设置长查询、表膨胀等监控
6. **缓存加速** → Redis 缓存热点查询

---

## 🔗 相关 Skills

- **code** — 代码工作流集成
- **github** — 迁移脚本版本控制
- **security-auditor** — 数据库安全审计
- **monitoring** — 系统监控集成
- **redis** — 缓存层操作

---

## 📝 总结

**Database Operations** 是一个**生产级、全面、实用**的数据库 Skill，特别适合：

- PostgreSQL 开发者
- .NET/EF Core 技术栈团队
- 需要零停机迁移的大规模应用
- 关注性能优化的工程师

**核心价值**：
- 🎯 **节省时间** — 提供现成的模式模板
- 🛡️ **降低风险** — 强调回滚和零停机
- 📈 **提升性能** — 系统化的优化方法论
- 🔍 **可观测性** — 全面的监控查询

**推荐指数**: ⭐⭐⭐⭐⭐ (5/5)

---

*分析完成时间: 2026-03-02 20:04*  
*下次探索目标: productivity (3.559) 或 monitoring (3.537)*
