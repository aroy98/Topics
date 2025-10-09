# 🐘 PostgreSQL Mastery Series – Part X: Production Ops & Observability

---

## 🎯 Objective

Operate PostgreSQL in production with confidence — mastering **monitoring**, **logging**, **capacity planning**, **upgrades**, and **cloud optimization** for performance, reliability, and cost-efficiency.

---

## 🧩 Topics Covered

### 1) Key Monitoring Metrics

PostgreSQL exposes a rich set of runtime views:

| Category    | View                    | Monitors                         |
| ----------- | ----------------------- | -------------------------------- |
| Activity    | `pg_stat_activity`      | Active sessions, running queries |
| Locks       | `pg_locks`              | Waits & blocking locks           |
| Replication | `pg_stat_replication`   | Replica sync/lag                 |
| Query stats | `pg_stat_statements`    | Query time, I/O, calls           |
| Storage     | `pg_statio_user_tables` | Reads, cache hits                |
| WAL         | `pg_stat_wal`           | Write-ahead log metrics          |
| Background  | `pg_stat_bgwriter`      | Checkpoints, buffers             |

#### Example:

```sql
SELECT pid, usename, datname, state, wait_event_type, wait_event, query
FROM pg_stat_activity
WHERE state != 'idle';

SELECT * FROM pg_locks WHERE granted = false;
```

> 💡 Combine these metrics in dashboards (Grafana, Prometheus) for live observability.

---

### 2) `pg_stat_statements` – Query Analytics

```sql
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;

SELECT queryid, calls, total_exec_time, mean_exec_time, rows
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;
```

| Metric                             | Meaning               |
| ---------------------------------- | --------------------- |
| `calls`                            | # of executions       |
| `mean_exec_time`                   | Average time per call |
| `rows`                             | Avg. result size      |
| `blk_read_time` / `blk_write_time` | I/O latency           |

> 🔍 Identify slow or frequent queries to optimize indexes or caching.

---

### 3) Logging Configuration

Enable performance & error logging in `postgresql.conf`:

```conf
log_statement = 'ddl'
log_min_duration_statement = 500ms
log_checkpoints = on
log_autovacuum_min_duration = 0
log_lock_waits = on
log_timezone = 'UTC'
```

#### Example log entry

```
2025-10-09 12:30:45 UTC LOG: duration: 501.234 ms statement: SELECT * FROM orders WHERE id=10;
```

> 📂 Logs stored in `$PGDATA/log/` or configured `log_directory`.

---

### 4) Metrics & Dashboards with Prometheus + Grafana

Install **postgres_exporter** to expose metrics:

```bash
docker run -d \
  -p 9187:9187 \
  -e DATA_SOURCE_NAME="postgresql://postgres:password@host:5432/postgres?sslmode=disable" \
  quay.io/prometheuscommunity/postgres-exporter
```

Visualize via **Grafana Dashboards**:

* TPS (transactions per second)
* Cache hit ratio (`1 - blks_read / (blks_read + blks_hit)`)
* Autovacuum stats
* Long-running queries
* Replication lag

> 🎯 Aim for **>99% cache hit ratio**, minimal lock waits, and balanced I/O.

---

### 5) Capacity Planning

#### Key Metrics

* CPU utilization vs query load
* Memory: `shared_buffers`, `work_mem`, `maintenance_work_mem`
* Storage growth rate (per table/index)
* WAL volume & checkpoint frequency

```sql
SELECT schemaname, relname AS table, pg_size_pretty(pg_total_relation_size(relid)) AS total_size
FROM pg_catalog.pg_statio_user_tables ORDER BY pg_total_relation_size(relid) DESC LIMIT 10;
```

> 💡 Use `pgstattuple` to estimate bloat and plan VACUUM frequency.

---

### 6) Major & Minor Upgrades

#### Minor

Safe, in-place updates (bug fixes, no feature changes):

```bash
sudo apt-get update && sudo apt-get install postgresql-16
systemctl restart postgresql
```

#### Major

Requires dump/restore or logical replication.

```bash
pg_dumpall > backup.sql
pg_upgrade -b /usr/lib/postgresql/15/bin -B /usr/lib/postgresql/16/bin -d /var/lib/postgresql/15/main -D /var/lib/postgresql/16/main
```

> 🧠 Always test upgrades in staging. For zero-downtime, use **logical replication**.

---

### 7) Migration Strategies

* Use `pg_dump` / `pg_restore` for schema + data moves.
* For live data: **logical replication** or **pglogical**.
* Use tools like **pgloader** for ETL or cross-DB migrations.

```bash
pgloader mysql://user:pass@localhost/db postgresql://user:pass@localhost/pgdb
```

> 🧩 Automate migrations in CI/CD (e.g., Flyway, Liquibase, Sqitch).

---

### 8) Cloud Deployment & Scaling

| Provider | Managed Service         | Highlights                        |
| -------- | ----------------------- | --------------------------------- |
| AWS      | RDS / Aurora            | Auto backup, failover, encryption |
| GCP      | Cloud SQL               | HA, IAM auth, insights            |
| Azure    | Azure DB for PostgreSQL | VNet isolation, autoscaling       |

#### Scaling Approaches

* **Vertical**: increase CPU/RAM.
* **Horizontal**: read replicas (logical or streaming).
* **Connection pooling**: PgBouncer / pgbadger.
* **Storage scaling**: partitioning, tablespaces.

> 💡 Use **connection pooling** and **read replicas** before vertical scaling.

---

### 9) Cost Optimization

* Use **BRIN indexes** for time-series.
* Periodically **archive cold data**.
* Use **pg_partman** for automated partitioning.
* Monitor **replication lag** to balance read loads.
* Enable **autovacuum tuning** to minimize I/O spikes.

> 💰 Proper index + retention policy tuning can save 40–60% on cloud costs.

---

### 10) Mini Project – Production-Ready Stack

Deploy PostgreSQL with the following setup:

1. **Primary + Read Replica** (streaming replication)
2. **PgBouncer** for pooling
3. **Prometheus + Grafana** for monitoring
4. **pg_cron** for maintenance jobs
5. **pg_audit** + log rotation for compliance

```bash
-- Example maintenance job
docker exec -it postgres psql -c "SELECT cron.schedule('nightly_vacuum', '0 2 * * *', $$VACUUM (ANALYZE);$$);"
```

> 🎯 Outcome: A secure, observable, and automated PostgreSQL stack for enterprise workloads.

---

### 11) Cheatsheet Summary

| Task            | Tool/Command                           |
| --------------- | -------------------------------------- |
| Active sessions | `pg_stat_activity`                     |
| Slow queries    | `pg_stat_statements`                   |
| Storage usage   | `pg_total_relation_size()`             |
| Logs            | `log_min_duration_statement`, `pg_log` |
| Dashboards      | Prometheus + Grafana                   |
| Major upgrade   | `pg_upgrade`                           |
| Backups         | `pg_dump`, `pg_basebackup`             |
| Pooling         | PgBouncer                              |
| Maintenance     | `pg_cron`                              |
| Replication     | Streaming / Logical                    |

---

## 🏁 PostgreSQL Mastery Complete

🎉 You’ve completed the **PostgreSQL Mastery Series (Parts I–X)** — from fundamentals to production-grade scaling. You now have the expertise to design, secure, optimize, and operate PostgreSQL in any environment — from local dev to high-availability cloud clusters.

> 🚀 Next challenge: Build a **real-world data platform** integrating **PostgreSQL + LLMs + APIs**, or contribute to Postgres open source extensions.
