# 🐘 PostgreSQL Mastery Series – Part IX: Security, Auditing & Compliance

---

## 🎯 Objective

Harden PostgreSQL environments with **authentication, access control, encryption, auditing, and compliance** best practices. Learn to secure your data both **in transit** and **at rest**, and enforce privacy rules via **row-level security**.

---

## 🧩 Topics Covered

### 1) PostgreSQL Security Model Overview

* **Role-based Access Control (RBAC)** — unified concept for users, groups, and permissions.
* **Authentication methods**: password (`scram-sha-256`), peer, certificate, LDAP, Kerberos, etc.
* **Authorization**: object-level privileges via `GRANT` and `REVOKE`.
* **Encryption**: SSL/TLS for transit, disk-level or column-level for rest.

> 🧠 Security in PostgreSQL = **Authentication + Authorization + Auditing + Encryption**.

---

### 2) Role & Privilege Management

```sql
-- Create login roles
CREATE ROLE readonly LOGIN PASSWORD 'Secure123!' NOSUPERUSER NOCREATEDB;
CREATE ROLE readwrite LOGIN PASSWORD 'RWpass#2024';

-- Database-level access
GRANT CONNECT ON DATABASE company TO readonly, readwrite;

-- Schema access
GRANT USAGE ON SCHEMA public TO readonly, readwrite;

-- Object-level privileges
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO readwrite;

-- Default privileges for future tables
ALTER DEFAULT PRIVILEGES IN SCHEMA public
  GRANT SELECT ON TABLES TO readonly;
```

> ⚙️ Apply **least privilege** principle—grant only what’s needed.

---

### 3) Authentication – `pg_hba.conf`

Define **who can connect**, **from where**, and **how**.

```conf
# TYPE  DATABASE  USER        ADDRESS          METHOD
local   all       all                          peer
host    all       all         127.0.0.1/32     scram-sha-256
hostssl all       all         0.0.0.0/0        scram-sha-256
```

Supported methods:

| Method          | Description                         |
| --------------- | ----------------------------------- |
| `trust`         | No password (unsafe, dev only)      |
| `md5`           | Legacy password (deprecated)        |
| `scram-sha-256` | Secure password-based (recommended) |
| `peer`          | OS user match (local only)          |
| `cert`          | SSL certificate-based               |

Reload config:

```sql
SELECT pg_reload_conf();
```

---

### 4) Row-Level Security (RLS)

Restrict visibility of rows per user.

#### Enable & Apply

```sql
CREATE TABLE accounts (
  id SERIAL PRIMARY KEY,
  owner TEXT,
  balance DECIMAL(10,2)
);

ALTER TABLE accounts ENABLE ROW LEVEL SECURITY;

CREATE POLICY account_owner_policy ON accounts
  FOR SELECT USING (owner = current_user);
```

> ✅ Each user sees only their own rows when querying the same table.

#### Verify

```sql
SET ROLE alice;
SELECT * FROM accounts; -- Returns only Alice’s rows
```

---

### 5) SSL/TLS – Encryption in Transit

Enable SSL in `postgresql.conf`:

```conf
ssl = on
ssl_cert_file = 'server.crt'
ssl_key_file = 'server.key'
```

Generate a self-signed certificate (dev):

```bash
openssl req -new -x509 -days 365 -nodes -out server.crt -keyout server.key
chmod 600 server.key
```

Check SSL usage:

```sql
SHOW ssl;
SELECT ssl, version, cipher FROM pg_stat_ssl WHERE pid = pg_backend_pid();
```

> 💡 Always use SSL/TLS for cloud or external connections.

---

### 6) Encryption at Rest

* PostgreSQL doesn’t natively encrypt data files — use **OS-level encryption**:

  * Linux: LUKS/dm-crypt
  * Windows: BitLocker
* **Cloud-managed encryption:** RDS, Cloud SQL, Azure DB handle it transparently.
* For column-level encryption:

  * Use `pgcrypto` extension:

```sql
CREATE EXTENSION pgcrypto;
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email TEXT,
  secret BYTEA
);

INSERT INTO users (email, secret)
VALUES ('john@demo.com', pgp_sym_encrypt('SensitiveData', 'mysecretkey'));

SELECT email, pgp_sym_decrypt(secret, 'mysecretkey') AS decrypted FROM users;
```

---

### 7) Auditing with `pg_audit`

```sql
CREATE EXTENSION pg_audit;
ALTER SYSTEM SET pgaudit.log = 'ddl, write';
SELECT pg_reload_conf();
```

Logs statements like CREATE, INSERT, UPDATE, DELETE.

```sql
-- Example audit record (in logs)
AUDIT: SESSION,3,1,WRITE,INSERT,TABLE,public.employees,INSERT INTO employees(...)
```

---

### 8) Data Masking (Dynamic Filtering)

Use views or CASE expressions to mask sensitive data.

```sql
CREATE VIEW employees_masked AS
SELECT id,
       name,
       CASE WHEN current_user = 'hr_admin' THEN salary ELSE NULL END AS salary
FROM employees;
```

> 🧩 Combine with RLS for multi-layer security.

---

### 9) Compliance Best Practices

| Standard    | PostgreSQL Approach                                                          |
| ----------- | ---------------------------------------------------------------------------- |
| **GDPR**    | Data anonymization, user consent tracking, right-to-erasure via RLS/policies |
| **HIPAA**   | Encrypt PHI via pgcrypto + audit all access                                  |
| **SOC 2**   | Logging, change tracking, least privilege                                    |
| **PCI-DSS** | Network isolation, encryption, and continuous auditing                       |

> 💡 Document every security control for compliance readiness.

---

### 10) Mini Project – Secure Customer Data Platform

1. Create encrypted customer table with audit logging.
2. Enforce RLS by customer ownership.
3. Enable SSL and pg_audit.

```sql
CREATE EXTENSION IF NOT EXISTS pgcrypto;
CREATE EXTENSION IF NOT EXISTS pg_audit;

CREATE TABLE customers (
  id SERIAL PRIMARY KEY,
  name TEXT,
  email TEXT UNIQUE,
  ssn BYTEA,
  owner TEXT
);

ALTER TABLE customers ENABLE ROW LEVEL SECURITY;
CREATE POLICY own_data_policy ON customers USING (owner = current_user);

INSERT INTO customers (name, email, ssn, owner)
VALUES ('Alice', 'alice@demo.com', pgp_sym_encrypt('123-45-6789', 'key1'), 'alice');
```

> ✅ Each user can see only their encrypted records and audited actions.

---

### 11) Cheatsheet Summary

| Feature            | Tool/Command                         |
| ------------------ | ------------------------------------ |
| Auth control       | `pg_hba.conf`, `scram-sha-256`       |
| Role mgmt          | `CREATE ROLE`, `GRANT`, `REVOKE`     |
| RLS                | `CREATE POLICY ... USING (...)`      |
| SSL/TLS            | `ssl=on`, certificates               |
| At rest encryption | OS-level / `pgcrypto`                |
| Auditing           | `pg_audit` extension                 |
| Masking            | Views with CASE expressions          |
| Compliance         | GDPR, HIPAA, SOC 2, PCI-DSS mappings |

---

## 🧠 Next Step → Part X: Production Ops & Observability

You’ll master **monitoring**, **performance dashboards**, **log analysis**, **migrations**, and **cloud optimization** for production-grade PostgreSQL.
