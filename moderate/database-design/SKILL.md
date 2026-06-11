---
name: database-design
version: 5.1.1-moderate
description: "Database design: schema patterns, migrations, NoSQL, query optimization, scaling, microservices data patterns."
author: Dinoudon
tags: [database, schema, migrations, nosql, postgresql, mysql, redis, mongodb]
related_skills: [architecture-design, sdlc-deployment]
---

# Database Design

Balanced guide to database design, migrations, optimization, and scaling.

## Steps

### 1. Normalization

| Form | Rule |
|------|------|
| 1NF | Atomic values, no repeating groups |
| 2NF | Non-key depends on entire PK |
| 3NF | No transitive dependencies |
| BCNF | Every determinant is a candidate key |

```sql
-- Violates 1NF
CREATE TABLE orders (id INT, products TEXT);  -- "laptop,mouse"

-- Fixed (1NF)
CREATE TABLE order_items (
  order_id INT, product_id INT, quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

Denormalize for: analytics (star schema), caching, reporting.

### 2. Migration Tools

| Tool | Language | Auto-gen | Rollback |
|------|----------|----------|----------|
| Flyway | Java/SQL | No | Undo scripts |
| Liquibase | Java/XML | No | Built-in |
| Alembic | Python | Yes | Manual |
| Atlas | Go | Yes | Built-in |
| Prisma | TS | Yes | Manual |

### 3. Indexing

```sql
-- Composite (leftmost prefix)
CREATE INDEX idx ON users(email, name);  -- covers (email), (email,name)

-- Partial
CREATE INDEX idx ON users(email) WHERE active = true;

-- Covering (INCLUDE)
CREATE INDEX idx ON users(email) INCLUDE (name);
```

### 4. Query Optimization

```sql
-- BAD: function on column
SELECT * FROM users WHERE LOWER(email) = 'alice@ex.com';
-- GOOD: functional index
CREATE INDEX idx ON users(LOWER(email));

-- BAD: OFFSET
SELECT * FROM users ORDER BY id LIMIT 20 OFFSET 10000;
-- GOOD: keyset
SELECT * FROM users WHERE id > 10000 ORDER BY id LIMIT 20;
```

### 5. Scaling

| Strategy | Use Case | Tool |
|----------|----------|------|
| Read replicas | Read-heavy | PostgreSQL streaming |
| Sharding | Write-heavy | Vitess, Citus |
| Partitioning | Time-series | PostgreSQL declarative |
| Connection pooling | High concurrency | PgBouncer, HikariCP |

### 6. Microservices Data

- Database-per-service (no shared DB)
- CDC with Debezium for cross-service sync
- Outbox pattern for reliable events

### 7. PostgreSQL

JSONB queries, window functions, CTEs, pg_stat_statements.

### 8. Redis

Cache-aside, rate limiting, pub/sub patterns.

---

## Checklist

- [ ] Normalization appropriate for use case
- [ ] FKs indexed, composite indexes for common queries
- [ ] Migrations version-controlled with rollback scripts
- [ ] EXPLAIN ANALYZE on slow queries
- [ ] Connection pooling configured
- [ ] No SELECT * in production

## Pitfalls

| Pitfall | Fix |
|---------|-----|
| No FK indexes | Always index FKs |
| OFFSET pagination | Use keyset |
| Shared DB in microservices | Database-per-service |
| No migration rollback | Write undo scripts |
