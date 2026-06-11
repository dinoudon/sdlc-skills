---
name: database-design
version: 5.1.1
description: "Database design: schema patterns, migrations, NoSQL, query optimization, scaling, microservices data patterns."
author: Dinoudon
tags: [database, schema, migrations, nosql, postgresql, mysql, redis, mongodb, query-optimization]
related_skills: [architecture-design, sdlc-deployment, sdlc-observability]
---

# Database Design

Comprehensive guide to database schema design, migration tools, query optimization, and scaling patterns.

## When to Use

- Designing database schemas for new applications
- Choosing between SQL and NoSQL databases
- Optimizing slow queries
- Scaling database infrastructure
- Implementing database migrations in CI/CD
- Modeling data for microservices

## Steps

### 1. Normalization (1NF → BCNF)

| Normal Form | Rule | Example Violation |
|-------------|------|-------------------|
| 1NF | Atomic values, no repeating groups | `tags: "python,javascript"` → separate table |
| 2NF | 1NF + non-key depends on entire PK | Partial dependency in composite PK |
| 3NF | 2NF + no transitive dependencies | `user_id → city → zip` → split tables |
| BCNF | Every determinant is a candidate key | Overlapping candidate keys |

```sql
-- Before (violates 1NF)
CREATE TABLE orders (
  id INT PRIMARY KEY,
  products TEXT  -- "laptop,mouse,keyboard"
);

-- After (1NF)
CREATE TABLE orders (id INT PRIMARY KEY);
CREATE TABLE order_items (
  order_id INT REFERENCES orders(id),
  product_id INT REFERENCES products(id),
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

When to denormalize:
- Read-heavy analytics (star schema)
- Caching layers (Redis, materialized views)
- Reporting tables (pre-aggregated data)

---

### 2. Schema Design Patterns

**Star Schema (OLAP):**
```sql
-- Fact table
CREATE TABLE sales (
  sale_id BIGINT PRIMARY KEY,
  date_id INT REFERENCES dim_date(date_id),
  product_id INT REFERENCES dim_product(product_id),
  store_id INT REFERENCES dim_store(store_id),
  amount DECIMAL(10,2),
  quantity INT
);

-- Dimension tables
CREATE TABLE dim_date (
  date_id INT PRIMARY KEY,
  date DATE,
  year INT,
  quarter INT,
  month INT,
  day_of_week VARCHAR(10)
);
```

**Snowflake Schema:** Normalize dimensions into sub-dimensions.
- Pros: less storage, clearer hierarchies
- Cons: more joins, slower queries

---

### 3. Database Migration Tools

| Tool | Language | Format | Auto-generate | Rollback |
|------|----------|--------|---------------|----------|
| Flyway | Java | SQL/Java | No | Undo scripts |
| Liquibase | Java | XML/YAML/JSON/SQL | No | Built-in |
| Alembic | Python | Python | Yes (SQLAlchemy) | Manual |
| Atlas | Go | HCL/SQL | Yes (Ent/GORM) | Built-in |
| Prisma Migrate | TypeScript | Prisma Schema | Yes | Manual |

**Flyway:**
```sql
-- V1__create_users.sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- U1__undo_create_users.sql
DROP TABLE users;
```

**Alembic:**
```python
# migration.py
def upgrade():
    op.create_table('users',
        sa.Column('id', sa.UUID, primary_key=True),
        sa.Column('email', sa.String(255), unique=True),
        sa.Column('created_at', sa.DateTime),
    )

def downgrade():
    op.drop_table('users')
```

---

### 4. Indexing Strategies

| Index Type | Use Case | PostgreSQL | MySQL |
|------------|----------|------------|-------|
| B-tree | Equality + range | Default | Default |
| Hash | Equality only | `CREATE INDEX ... USING hash` | `USING hash` |
| GIN | Arrays, JSONB, full-text | `CREATE INDEX ... USING gin` | N/A |
| GiST | Geometric, range types | `CREATE INDEX ... USING gist` | N/A |
| BRIN | Large sorted tables | `CREATE INDEX ... USING brin` | N/A |

**Composite index (leftmost prefix rule):**
```sql
-- Covers: (a), (a,b), (a,b,c) queries
CREATE INDEX idx_users_email_name ON users(email, name);

-- Does NOT cover: (name) or (name,email) queries
```

**Partial index:**
```sql
-- Only index active users (smaller, faster)
CREATE INDEX idx_active_users ON users(email) WHERE active = true;
```

**Covering index (INCLUDE):**
```sql
-- Satisfy query from index alone (index-only scan)
CREATE INDEX idx_users_email ON users(email) INCLUDE (name, created_at);
```

---

### 5. EXPLAIN Plans

```sql
EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)
SELECT u.name, COUNT(o.id)
FROM users u
JOIN orders o ON o.user_id = u.id
WHERE u.created_at > '2024-01-01'
GROUP BY u.name;
```

Key metrics to watch:
| Metric | Good | Bad |
|--------|------|-----|
| Seq Scan | Small tables | Large tables → add index |
| Index Scan | Point lookups | High cost → check selectivity |
| Index Only Scan | Best case | Requires covering index |
| Nested Loop | Small inner set | Large inner set → use hash join |
| Hash Join | Large sets | Missing stats → ANALYZE |

---

### 6. Query Optimization

```sql
-- BAD: function on indexed column (kills index)
SELECT * FROM users WHERE LOWER(email) = 'alice@example.com';

-- GOOD: functional index
CREATE INDEX idx_users_email_lower ON users(LOWER(email));

-- BAD: SELECT * (fetches unnecessary columns)
SELECT * FROM users WHERE active = true;

-- GOOD: select only needed columns
SELECT id, name, email FROM users WHERE active = true;

-- BAD: OFFSET pagination (scans all prior rows)
SELECT * FROM users ORDER BY id LIMIT 20 OFFSET 10000;

-- GOOD: keyset pagination
SELECT * FROM users WHERE id > 10000 ORDER BY id LIMIT 20;
```

**pg_stat_statements:**
```sql
SELECT query, calls, mean_exec_time, total_exec_time
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 10;
```

---

### 7. Database Scaling

**Read Replicas:**
```yaml
# PostgreSQL streaming replication
primary:
  wal_level: replica
  max_wal_senders: 3
  synchronous_commit: on

replica:
  primary_conninfo: 'host=primary port=5432'
  hot_standby: on
```

**Sharding (Vitess for MySQL):**
```yaml
# vschema.json
{
  "sharded": true,
  "vindexes": {
    "hash": { "type": "hash" }
  },
  "tables": {
    "users": {
      "column_vindexes": [{ "column": "id", "name": "hash" }]
    }
  }
}
```

**Partitioning (PostgreSQL):**
```sql
CREATE TABLE orders (
  id BIGINT,
  created_at TIMESTAMPTZ,
  amount DECIMAL(10,2)
) PARTITION BY RANGE (created_at);

CREATE TABLE orders_2024_q1 PARTITION OF orders
  FOR VALUES FROM ('2024-01-01') TO ('2024-04-01');

CREATE TABLE orders_2024_q2 PARTITION OF orders
  FOR VALUES FROM ('2024-04-01') TO ('2024-07-01');
```

**Connection Pooling (PgBouncer):**
```ini
[databases]
mydb = host=127.0.0.1 port=5432 dbname=mydb

[pgbouncer]
listen_port = 6432
pool_mode = transaction
max_client_conn = 1000
default_pool_size = 20
```

---

### 8. Microservices Data Patterns

**Database-per-Service:**
```
┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│ User Service│  │ Order Svc   │  │ Product Svc │
│     ↓       │  │     ↓       │  │     ↓       │
│  users_db   │  │  orders_db  │  │ products_db │
└─────────────┘  └─────────────┘  └─────────────┘
```

**Change Data Capture (Debezium):**
```json
{
  "name": "users-connector",
  "config": {
    "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
    "database.hostname": "postgres",
    "database.dbname": "users_db",
    "table.include.list": "public.users",
    "topic.prefix": "users"
  }
}
```

**Outbox Pattern:**
```sql
-- Write to outbox in same transaction
BEGIN;
  INSERT INTO orders (id, user_id, amount) VALUES (...);
  INSERT INTO outbox (aggregate_type, aggregate_id, event_type, payload)
    VALUES ('order', 'ord_123', 'OrderCreated', '{"amount": 99.99}');
COMMIT;

-- CDC streams outbox table to Kafka
```

---

### 9. PostgreSQL Deep Dive

**JSONB:**
```sql
-- Store flexible attributes
CREATE TABLE products (
  id UUID PRIMARY KEY,
  name TEXT,
  attributes JSONB
);

-- Query JSONB
SELECT * FROM products WHERE attributes->>'color' = 'red';
SELECT * FROM products WHERE attributes @> '{"size": "large"}';

-- Index JSONB
CREATE INDEX idx_products_attrs ON products USING gin(attributes);
```

**Window Functions:**
```sql
SELECT
  user_id,
  amount,
  SUM(amount) OVER (PARTITION BY user_id ORDER BY created_at) as running_total,
  ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY amount DESC) as rank
FROM orders;
```

**CTEs (WITH):**
```sql
WITH active_users AS (
  SELECT id, name FROM users WHERE active = true
),
recent_orders AS (
  SELECT user_id, SUM(amount) as total
  FROM orders WHERE created_at > NOW() - INTERVAL '30 days'
  GROUP BY user_id
)
SELECT au.name, COALESCE(ro.total, 0) as spend_30d
FROM active_users au
LEFT JOIN recent_orders ro ON ro.user_id = au.id;
```

---

### 10. Redis Patterns

**Caching (Cache-Aside):**
```python
import redis

r = redis.Redis()

def get_user(user_id):
    # Check cache first
    cached = r.get(f"user:{user_id}")
    if cached:
        return json.loads(cached)
    
    # Cache miss: query DB
    user = db.query("SELECT * FROM users WHERE id = %s", user_id)
    
    # Store in cache with TTL
    r.setex(f"user:{user_id}", 3600, json.dumps(user))
    return user
```

**Rate Limiting:**
```python
def is_rate_limited(user_id, limit=100, window=60):
    key = f"ratelimit:{user_id}"
    current = r.incr(key)
    if current == 1:
        r.expire(key, window)
    return current > limit
```

**Pub/Sub:**
```python
# Publisher
r.publish('notifications', json.dumps({'user': 'u1', 'msg': 'Hello'}))

# Subscriber
pubsub = r.pubsub()
pubsub.subscribe('notifications')
for message in pubsub.listen():
    if message['type'] == 'message':
        process(message['data'])
```

---

## Checklist

### Schema Design
- [ ] Appropriate normalization level (3NF for OLTP, denormalized for OLAP)
- [ ] Primary keys defined (UUID or auto-increment)
- [ ] Foreign keys with proper ON DELETE/UPDATE actions
- [ ] CHECK constraints for data validation
- [ ] Default values for common fields (created_at, updated_at)
- [ ] Audit columns (created_by, updated_by, deleted_at for soft delete)

### Indexing
- [ ] Indexes on foreign keys
- [ ] Composite indexes for common query patterns
- [ ] Partial indexes for filtered queries
- [ ] No duplicate or redundant indexes
- [ ] Index bloat monitored

### Migrations
- [ ] Migrations version-controlled in Git
- [ ] Migrations tested in CI before merge
- [ ] Rollback scripts provided
- [ ] Zero-downtime migration strategy (expand-contract)
- [ ] Large table migrations use batched approach

### Performance
- [ ] EXPLAIN ANALYZE run on slow queries
- [ ] pg_stat_statements enabled and monitored
- [ ] Connection pooling configured
- [ ] Query result caching strategy defined
- [ ] N+1 queries eliminated

---

## Pitfalls

| Pitfall | Impact | Fix |
|---------|--------|-----|
| No indexing on foreign keys | Slow joins | Always index FKs |
| SELECT * in production | Wasted I/O, memory | Select specific columns |
| OFFSET pagination | Slow at scale | Use keyset pagination |
| Functions on indexed columns | Index not used | Use functional indexes |
| No connection pooling | Connection exhaustion | Use PgBouncer/HikariCP |
| Shared database in microservices | Tight coupling | Database-per-service |
| No migration rollback plan | Can't revert failures | Always write undo scripts |
| Ignoring EXPLAIN plans | Undetected slow queries | Profile before deploying |

---

## Quick Reference

**Key URLs:**
- PostgreSQL docs: https://www.postgresql.org/docs/
- Use The Index Luke: https://use-the-index-luke.com/
- Flyway: https://docs.flyway.io/
- Alembic: https://alembic.sqlalchemy.org/
- Atlas: https://atlasgo.io/
- Vitess: https://vitess.io/docs/
- PgBouncer: https://www.pgbouncer.org/config.html
- Debezium: https://debezium.io/documentation/
