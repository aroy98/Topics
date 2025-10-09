# 🐘 PostgreSQL Mastery Series – Part IV: Administration & Configuration

---

## 🎯 Objective

Operate PostgreSQL like a pro: understand **server architecture**, manage **roles & privileges**, secure access with **pg_hba.conf**, tune **core parameters**, and handle **environment & lifecycle** tasks.

---

## 🧩 Topics Covered

### 1) Architecture Overview

* **postmaster (postgres)** → cluster manager (one per data directory)
* **Backends** → one process per client connection
* **Background workers** → autovacuum, checkpointer, walwriter, logical replication launcher
* **WAL (Write-Ahead Log)** → durability & crash recovery
* **Data directory** (a.k.a. *PGDATA*): `base/`, `global/`, `pg_wal/`, `pg_stat/`, `pg_tblspc/`, `pg_logical/`

> 💡 `SHOW data_directory;` to locate PGDATA.

---

### 2) Cluster & Service Management

```bash
# Initialize (if needed)
initdb -D /var/lib/postgresql/data

# Start/stop/restart
pg_ctl -D /var/lib/postgresql/data start
pg_ctl -D /var/lib/postgresql/data stop -m fast
pg_ctl -D /var/lib/postgresql/data restart

# Check server version & settings
psql -c "SELECT version();"
psql -c "SHOW all;"
```

* **System services** (Linux): `systemctl start postgresql` (distro-specific)
* **Docker**:

```bash
docker run --name pg -e POSTGRES_PASSWORD=postgres -p 5432:5432 -v pgdata:/var/lib/postgresql/data -d postgres:16
```

---

### 3) Roles, Privileges & Security Model

* **Roles** unify users & groups.
* Permissions granted on: **database, schema, table, sequence, function**.

```sql
-- Create login role
CREATE ROLE app_user LOGIN PASSWORD 'S3cure!'
  NOSUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT;

-- Create group role and grant
CREATE ROLE app_readwrite;
GRANT app_readwrite TO app_user;

-- Object privileges
GRANT USAGE ON SCHEMA public TO app_readwrite;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_readwrite;
ALTER DEFAULT PRIVILEGES IN SCHEMA public
  GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO app_readwrite;
```

> 🛡️ **Security tips:** Avoid `SUPERUSER`; use least privilege; rotate passwords; prefer SCRAM auth.

---

### 4) Authentication: `pg_hba.conf`

Controls *who* can connect, *from where*, and *how*.

```conf
# TYPE  DATABASE  USER        ADDRESS          METHOD
host    all       all         127.0.0.1/32     scram-sha-256
host    app_db    app_user    10.0.0.0/24      scram-sha-256
hostssl all       all         0.0.0.0/0        scram-sha-256
```

* Reload after changes: `SELECT pg_reload_conf();`
* Methods: `scram-sha-256`, `md5` (legacy), `peer`, `trust` (dev only)

---

### 5) Core Configuration (`postgresql.conf`)

Key knobs for most workloads:

| Parameter              | What it does                   | Quick guidance                                      |
| ---------------------- | ------------------------------ | --------------------------------------------------- |
| `shared_buffers`       | Buffer cache                   | ~25% of RAM (server-only)                           |
| `work_mem`             | Per-sort/hash memory           | Start 4–64MB; beware per-node/per-session           |
| `maintenance_work_mem` | VACUUM/CREATE INDEX            | 256MB–2GB depending on RAM                          |
| `effective_cache_size` | Planner hint for OS cache      | ~50–75% of RAM                                      |
| `max_connections`      | Concurrent backends            | Keep modest; use pooling                            |
| `wal_level`            | WAL detail                     | `replica` for streaming, `logical` for logical repl |
| `checkpoint_timeout`   | Checkpoint frequency           | 5–15 min typical                                    |
| `max_wal_size`         | WAL budget between checkpoints | Increase for write-heavy                            |
| `random_page_cost`     | Planner cost for random I/O    | 1.1–1.5 on SSD                                      |
| `autovacuum_*`         | Auto vacuum tuning             | Ensure enabled; tune scale factors                  |

```sql
-- Inspect & tweak dynamically where allowed
SHOW shared_buffers;
ALTER SYSTEM SET work_mem = '32MB';
SELECT pg_reload_conf();
```

> 📏 Use `ALTER SYSTEM` sparingly; prefer config management & env files.

---

### 6) Connections & Pooling

* Each connection = one process ⇒ expensive at high concurrency.
* Use **PgBouncer** (transaction/statement pooling) to stabilize connection count.

**PgBouncer minimal INI:**

```ini
[databases]
app_db = host=127.0.0.1 port=5432 dbname=app_db

[pgbouncer]
listen_addr = 0.0.0.0
listen_port = 6432
pool_mode = transaction
max_client_conn = 1000
default_pool_size = 50
```

> 💡 Point your app to `:6432` instead of `:5432`.

---

### 7) Routine Maintenance

* **VACUUM (AUTO)**: prevents bloat; tune `autovacuum_vacuum_scale_factor`, `autovacuum_analyze_scale_factor`.
* **ANALYZE**: keeps planner stats fresh.
* **REINDEX**: fix bloat or corruption on indexes.
* **pg_stat_statements**: track slow/high-cost queries.

```sql
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
SELECT * FROM pg_stat_statements ORDER BY total_exec_time DESC LIMIT 20;
VACUUM (VERBOSE, ANALYZE);
REINDEX TABLE my_table;
```

---

### 8) Logging & Observability

* Set: `log_min_duration_statement = 500ms` (or lower during tuning)
* Enable: `log_checkpoints`, `log_autovacuum_min_duration`, `log_lock_waits`
* Dashboards: Prometheus + Grafana (exporter: `postgres_exporter`)
* Runtime views: `pg_stat_activity`, `pg_locks`, `pg_stat_io` (v16+)

```sql
SELECT pid, usename, datname, state, query
FROM pg_stat_activity
ORDER BY query_start NULLS LAST;

SELECT * FROM pg_locks WHERE granted = false;
```

---

### 9) Encryption & Network

* **In transit**: enable SSL/TLS (`ssl=on`, provide `server.key`/`server.crt`)
* **At rest**: OS/disk encryption (LUKS, BitLocker); managed cloud options
* Secret handling via env vars / vaults; avoid plaintext in repo.

---

### 10) Extensions for Admins

* `pg_stat_statements` – query stats
* `pg_cron` – in-DB scheduling
* `pg_partman` – partition maintenance
* `pg_audit` – auditing

```sql
CREATE EXTENSION IF NOT EXISTS pg_cron;
SELECT cron.schedule('nightly_analyze', '0 3 * * *', $$VACUUM (ANALYZE);$$);
```

---

### 11) Mini Project – Secure, Tuned App Database

1. **Create DB & roles**

```sql
CREATE DATABASE app_db;
CREATE ROLE app_rw LOGIN PASSWORD 'Strong#1';
CREATE ROLE app_ro LOGIN PASSWORD 'Strong#2';
GRANT CONNECT ON DATABASE app_db TO app_rw, app_ro;
```

2. **Schema & default privileges**

```sql
\c app_db
CREATE SCHEMA app AUTHORIZATION app_rw;
GRANT USAGE ON SCHEMA app TO app_ro;
ALTER DEFAULT PRIVILEGES IN SCHEMA app
  GRANT SELECT ON TABLES TO app_ro;
```

3. **pg_hba.conf** (restrict by subnet; use `scram-sha-256`)
4. **Tune** `shared_buffers`, `work_mem`, `max_connections`; install PgBouncer.
5. **Enable** `pg_stat_statements`; set `log_min_duration_statement`.

---

### 12) Cheatsheet

| Task                | Command                             |
| ------------------- | ----------------------------------- |
| Reload config       | `SELECT pg_reload_conf();`          |
| Online param change | `ALTER SYSTEM SET ...;`             |
| Who’s connected     | `SELECT * FROM pg_stat_activity;`   |
| Kill backend        | `SELECT pg_terminate_backend(pid);` |
| Slow queries        | `pg_stat_statements`                |
| Check locks         | `SELECT * FROM pg_locks;`           |
| Vacuum/Analyze      | `VACUUM (ANALYZE);`                 |
| Create role         | `CREATE ROLE name LOGIN ...;`       |
| Grant privs         | `GRANT SELECT ON ... TO role;`      |

---

## 🧠 Next Step → Part V: Indexing & Performance

Go deep into index types (B-Tree, GIN, GiST, BRIN), partial/expressions, EXPLAIN/ANALYZE, statistics, and autovacuum tuning for speed.
