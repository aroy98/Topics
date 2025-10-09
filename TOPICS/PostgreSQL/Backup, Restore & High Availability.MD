# 🐘 PostgreSQL Mastery Series – Part VII: Backup, Restore & High Availability

---

## 🎯 Objective

Learn how to **back up**, **restore**, and **replicate** PostgreSQL databases for high availability (HA) and disaster recovery (DR). You’ll set up logical and physical backups, enable streaming replication, and test failover scenarios.

---

## 🧩 Topics Covered

### 1) Backup Strategies Overview

| Type            | Tool                    | Scope         | Use Case               |
| --------------- | ----------------------- | ------------- | ---------------------- |
| **Logical**     | `pg_dump`, `pg_dumpall` | Schema & data | Migration, small DBs   |
| **Physical**    | `pg_basebackup`, WAL    | Byte-level    | HA, large DBs          |
| **Incremental** | WAL archiving + PITR    | Continuous    | Point-in-time recovery |

> 💡 Combine both logical (for portability) and physical (for durability) backups.

---

### 2) Logical Backups – `pg_dump`

```bash
# Single database
pg_dump -U postgres -d mydb -F c -f backup_mydb.dump

# Plain SQL format
pg_dump -U postgres -d mydb > backup_mydb.sql

# Restore
pg_restore -U postgres -d mydb_restored backup_mydb.dump
```

#### Options

| Flag       | Meaning                                 |
| ---------- | --------------------------------------- |
| `-F`       | Format (`c` custom, `t` tar, `p` plain) |
| `-j`       | Parallel dump threads                   |
| `--schema` | Dump only a schema                      |
| `--table`  | Dump only a table                       |

#### Full Cluster Dump

```bash
pg_dumpall -U postgres > full_cluster.sql
```

---

### 3) Physical Backups – `pg_basebackup`

Used for **replication setup** and **binary-level restore**.

```bash
pg_basebackup -h primary_host -D /var/lib/postgresql/replica -U repl_user -Fp -Xs -P -R
```

| Flag  | Description                                       |
| ----- | ------------------------------------------------- |
| `-Fp` | Plain file format                                 |
| `-Xs` | Include WAL files                                 |
| `-R`  | Create recovery.conf/standby.signal automatically |

> 🔒 The user must have replication privileges (`REPLICATION` role).

---

### 4) Point-in-Time Recovery (PITR)

Allows restoring the database to a specific timestamp using WAL logs.

#### Step 1: Enable WAL Archiving

```conf
# postgresql.conf
archive_mode = on
archive_command = 'cp %p /var/lib/postgresql/wal_archive/%f'
```

#### Step 2: Restore

```bash
# Copy base backup to restore directory
cp -R /backups/base /var/lib/postgresql/data

# Add recovery settings
echo "restore_command = 'cp /var/lib/postgresql/wal_archive/%f %p'" >> postgresql.conf
echo "recovery_target_time = '2025-10-09 10:00:00'" >> postgresql.conf
touch standby.signal
```

> 🧠 PostgreSQL replays WAL logs until the recovery target time, then starts normally.

---

### 5) Streaming Replication Setup

Replicates changes in real time from primary → standby.

#### Step 1: On Primary

```sql
CREATE ROLE repl_user WITH REPLICATION LOGIN PASSWORD 'repl_pass';
```

Edit `pg_hba.conf`:

```conf
host replication repl_user 10.0.0.0/24 scram-sha-256
```

Edit `postgresql.conf`:

```conf
wal_level = replica
max_wal_senders = 10
wal_keep_size = 512MB
```

#### Step 2: On Standby

```bash
pg_basebackup -h primary_host -D /var/lib/postgresql/data -U repl_user -Fp -Xs -P -R
```

The `-R` flag auto-generates `standby.signal` and replication connection info.

#### Step 3: Verify Replication

```sql
SELECT client_addr, state, sync_state FROM pg_stat_replication;
```

| State                 | Description                     |
| --------------------- | ------------------------------- |
| `streaming`           | Standby is actively replicating |
| `catchup`             | Syncing missing WALs            |
| `sync_state = 'sync'` | Synchronous (commit waits)      |
| `async`               | Asynchronous                    |

---

### 6) Logical Replication

Allows replicating **specific tables** or **schemas** — ideal for selective or cross-version replication.

#### Step 1: Publisher (Primary)

```sql
ALTER SYSTEM SET wal_level = logical;
CREATE PUBLICATION my_pub FOR TABLE employees, departments;
```

#### Step 2: Subscriber (Replica)

```sql
CREATE SUBSCRIPTION my_sub
CONNECTION 'host=primary port=5432 dbname=mydb user=repl_user password=repl_pass'
PUBLICATION my_pub;
```

#### Monitor

```sql
SELECT * FROM pg_stat_subscription;
```

---

### 7) Failover & Switchover

| Concept        | Description                             |
| -------------- | --------------------------------------- |
| **Failover**   | Standby promoted when primary fails     |
| **Switchover** | Planned role reversal (for maintenance) |

#### Promote Standby

```bash
pg_ctl promote -D /var/lib/postgresql/data
```

#### Reconnect Old Primary

```sql
SELECT pg_wal_replay_resume();
```

#### Tools

* `repmgr` – replication management & failover automation
* `Patroni` – HA cluster manager with etcd/Consul
* `pg_auto_failover` – simple automatic failover solution

---

### 8) Backup & Recovery Best Practices

* Automate dumps via `pg_cron` or system cron.
* Test restores regularly.
* Keep offsite/remote backups.
* Use **point-in-time recovery (PITR)** for DR.
* Compress archives (`gzip`/`zstd`).
* Monitor replication lag via `pg_stat_replication`.
* For cloud, use managed snapshots (AWS RDS, GCP Cloud SQL).

---

### 9) Mini Project – HA & PITR Demo

1. Create a DB, insert some data.
2. Take a base backup.
3. Archive WALs.
4. Delete data accidentally.
5. Restore to pre-delete timestamp using PITR.
6. Verify recovery success.

```sql
-- Example recovery point
SELECT now();
DELETE FROM employees;
-- ... time passes ...
# Restore to the recorded timestamp via recovery_target_time
```

---

### 10) Cheatsheet

| Task               | Command                                          |
| ------------------ | ------------------------------------------------ |
| Logical dump       | `pg_dump -U user -d db > file.sql`               |
| Restore            | `psql -U user -d db < file.sql`                  |
| Base backup        | `pg_basebackup -U repl -D dir -Fp -Xs -P`        |
| Enable WAL archive | `archive_mode=on`, `archive_command='cp %p ...'` |
| PITR recovery      | `restore_command`, `recovery_target_time`        |
| View replication   | `SELECT * FROM pg_stat_replication;`             |
| Promote standby    | `pg_ctl promote -D data_dir`                     |
| Failover tool      | `repmgr`, `Patroni`, `pg_auto_failover`          |

---

## 🧠 Next Step → Part VIII: Extensions & Advanced Features

Dive into **PostGIS**, **pg_trgm**, **FDWs**, **full-text search**, **JSONB indexing**, and **PL/pgSQL** scripting.
