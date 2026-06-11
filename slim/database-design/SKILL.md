---
name: database-design
version: 5.1.1-slim
description: "Database design: schema patterns, migrations, query optimization, scaling."
author: Dinoudon
tags: [database, schema, migrations, query-optimization]
related_skills: [architecture-design]
---

# Database Design

Quick reference for database design and optimization.

## Normalization

1NF: atomic values → 2NF: full PK dependency → 3NF: no transitive deps → BCNF

Denormalize for: analytics, caching, reporting.

## Migration Tools

| Tool | Auto-gen | Rollback |
|------|----------|----------|
| Flyway | No | Undo scripts |
| Alembic | Yes | Manual |
| Atlas | Yes | Built-in |

## Indexing

```sql
-- Composite (leftmost prefix)
CREATE INDEX idx ON users(email, name);
-- Partial (smaller)
CREATE INDEX idx ON users(email) WHERE active = true;
-- Covering (index-only scan)
CREATE INDEX idx ON users(email) INCLUDE (name);
```

## Query Fixes

| Problem | Fix |
|---------|-----|
| Function on column | Functional index |
| OFFSET pagination | Keyset: `WHERE id > X LIMIT N` |
| SELECT * | Select specific columns |
| Missing index on FK | Always index FKs |

## Scaling

| Strategy | When |
|----------|------|
| Read replicas | Read-heavy |
| Sharding | Write-heavy, large data |
| Partitioning | Time-series |
| PgBouncer | High concurrency |

## Checklist

- [ ] Normalization appropriate
- [ ] FKs indexed
- [ ] Migrations with rollback
- [ ] EXPLAIN ANALYZE on slow queries
- [ ] Connection pooling
- [ ] No SELECT * in prod

## Pitfalls

| Pitfall | Fix |
|---------|-----|
| No FK indexes | Index all FKs |
| OFFSET pagination | Keyset pagination |
| Shared DB microservices | DB-per-service |
| No migration rollback | Always write undo |
