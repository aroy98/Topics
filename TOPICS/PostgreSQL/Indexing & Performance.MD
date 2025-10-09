# 🐘 PostgreSQL Mastery Series – Part V: Indexing & Performance

---

## 🎯 Objective

Design the **right indexes**, read **query plans** confidently, use **statistics** effectively, and apply **VACUUM/ANALYZE** & autovacuum tuning to keep your database fast and healthy.

---

## 🧩 Topics Covered

### 1) Index Fundamentals

* **B-Tree** (default): equality & range on sortable data.
* **Hash**: equality only (rarely needed; B-Tree often sufficient).
* **GiST**: extensible, geometric, ranges, full-text base.
* **GIN**: membership/containment for arrays, JSONB, tsvector.
* **BRIN**: huge, naturally ordered tables (append-only/time series).

> Rule of thumb: Start with **B-Tree**, consider **GIN** for JSONB/arrays/FTS, **BRIN** for very large append-only tables.

---

### 2) Creating the Right Index

#### Simple & Multi-Column

```sql
-- Single column
CREATE INDEX idx_users_email ON users(email);

-- Multi-column (leftmost prefix matters!)
CREATE INDEX idx_orders_customer_date ON orders(customer_id, order_date);
```

**Leftmost prefix**: queries can use `(customer_id)` or `(customer_id, order_date)` but **not** `(order_date)` alone.

#### Expression Index

```sql
-- Case-insensitive search
CREATE INDEX idx_ci_username ON accounts (lower(username));
-- Query must match expression
SELECT * FROM accounts WHERE lower(username) = 'akash';
```

#### Partial Index

```sql
-- Only index hot subset
CREATE INDEX idx_orders_open ON orders(status) WHERE status = 'OPEN';
```

#### Unique & Covering Index

```sql
-- Uniqueness guarantee
CREATE UNIQUE INDEX idx_users_email_unique ON users(email);

-- Covering (INCLUDE): helps avoid heap fetch
CREATE INDEX idx_orders_lookup ON orders(customer_id) INCLUDE (status, total);
```

#### Specialized

```sql
-- JSONB key lookup (GIN)
CREATE INDEX idx_products_specs_gin ON products USING GIN (specs jsonb_path_ops);

-- Array contains (GIN)
CREATE INDEX idx_posts_tags_gin ON posts USING GIN (tags);

-- Time-series BRIN
CREATE INDEX idx_metrics_ts_brin ON metrics USING BRIN (ts);
```

---

### 3) When NOT to Index

* Columns with **very low cardinality** (`status` with 2 values) unless used with partial index.
* Very small tables (planner may prefer seq scan).
* Highly volatile columns (updates cause bloat + maintenance).

---

### 4) Reading Query Plans (EXPLAIN / ANALYZE)

```sql
EXPLAIN (ANALYZE, BUFFERS, VERBOSE)
SELECT o.id, o.order_date, c.name
FROM orders o
JOIN customers c ON o.customer_id = c.id
WHERE o.order_date >= CURRENT_DATE - INTERVAL '30 days'
ORDER BY o.order_date DESC
LIMIT 50;
```

**Key nodes**

* **Seq Scan**: full table scan
* **Index Scan**: uses B-Tree on condition
* **Index Only Scan**: served from index + visibility map
* **Bitmap Index Scan** + **Bitmap Heap Scan**: good for many matches
* **Nested Loop**: small outer + indexed inner
* **Hash Join**: large joins, needs memory
* **Merge Join**: presorted inputs

**What to watch**

* `actual time` vs `rows`
* `rows` misestimates ⇒ create stats or analyze
* `Buffers: shared hit/read/dirtied` ⇒ I/O pressure
* `Planning time` vs `Execution time`

---

### 5) Statistics & Planner Guidance

#### ANALYZE & Extended Statistics

```sql
-- Refresh stats
ANALYZE;

-- Correlated columns (multi-column stats)
CREATE STATISTICS stat_orders_customer_date (dependencies)
  ON customer_id, order_date FROM orders;
ANALYZE orders;
```

#### Configuration Tips

* `default_statistics_target` ↑ for tricky distributions (e.g., 200–500 on problem tables).
* `work_mem` affects sort/hash nodes; set via `SET work_mem = '64MB';` per session for heavy queries.

---

### 6) VACUUM / AUTOVACUUM / Bloat

* **MVCC** leaves dead tuples; VACUUM cleans them.
* **Autovacuum** runs automatically; tune for write-heavy tables.

```sql
-- Manual maintenance
VACUUM (VERBOSE);
VACUUM (ANALYZE) orders;

-- Per-table autovacuum thresholds
ALTER TABLE orders SET (
  autovacuum_vacuum_scale_factor = 0.05,
  autovacuum_analyze_scale_factor = 0.02
);

-- Reclaim index bloat
REINDEX INDEX idx_orders_customer_date;
```

**Signs of bloat**: growing table/index size without proportional row count; slow lookups; high heap fetches.

---

### 7) Common Performance Anti-Patterns

* Leading wildcard searches on B-Tree (`LIKE '%foo'`) → use trigram/GIN.
* Functions on the column without matching **expression index**.
* Over-indexing (write slowdown, bigger storage, slower VACUUM).
* Not using **LIMIT** in APIs when appropriate.
* Chatty apps: too many small round-trips → batch/CTE.
* Missing JOIN predicates → accidental cross joins.

---

### 8) Full-Text Search (FTS) Crash Course

```sql
-- Setup
CREATE EXTENSION IF NOT EXISTS pg_trgm; -- for similarity searches
CREATE INDEX idx_docs_fts ON documents USING GIN (to_tsvector('english', content));

-- Query
SELECT id, ts_rank_cd(to_tsvector('english', content), plainto_tsquery('english', 'postgres performance')) AS rank
FROM documents
WHERE to_tsvector('english', content) @@ plainto_tsquery('english', 'postgres performance')
ORDER BY rank DESC
LIMIT 20;
```

> For substring/fuzzy matches, use `pg_trgm` and `%%` (similarity) or `LIKE` with trigram index.

---

### 9) Mini Project – Tune an Analytics Workload

1. **Workload**: dashboard filtering last 90 days by `customer_id`, aggregating totals.

```sql
-- Helpful indexes
CREATE INDEX idx_orders_cust_date ON orders(customer_id, order_date DESC) INCLUDE (total);

-- Query
EXPLAIN (ANALYZE, BUFFERS)
SELECT customer_id,
       COUNT(*) AS orders,
       SUM(total) AS revenue,
       MIN(order_date) AS first_order,
       MAX(order_date) AS last_order
FROM orders
WHERE order_date >= CURRENT_DATE - INTERVAL '90 days'
GROUP BY customer_id
ORDER BY revenue DESC
LIMIT 50;
```

2. **Tuning loop**: check misestimates → `ANALYZE`; high I/O → consider covering/BRIN; many matches → bitmap scans; slow sorts → raise `work_mem`.

---

### 10) Cheatsheet

| Need                    | Tool/Command                           |
| ----------------------- | -------------------------------------- |
| See plan                | `EXPLAIN (ANALYZE, BUFFERS)`           |
| IO vs CPU               | Check `Buffers` + node timings         |
| Refresh stats           | `ANALYZE` / autovacuum                 |
| Extended stats          | `CREATE STATISTICS ... (dependencies)` |
| Clean dead tuples       | `VACUUM` / autovacuum tuning           |
| Reclaim index bloat     | `REINDEX`                              |
| Choose index            | B-Tree/GIN/GiST/BRIN per workload      |
| Avoid function mismatch | Use **expression index**               |

---

## 🧠 Next Step → Part VI: Concurrency & Transactions

Learn **MVCC**, isolation levels, locks/deadlocks, hot updates, advisory locks, and connection pooling patterns.
