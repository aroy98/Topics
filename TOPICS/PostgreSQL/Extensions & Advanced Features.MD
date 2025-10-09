# 🐘 PostgreSQL Mastery Series – Part VIII: Extensions & Advanced Features

---

## 🎯 Objective

Expand PostgreSQL beyond a traditional RDBMS using **extensions**, **advanced data types**, and **procedural code**. You’ll explore spatial, fuzzy, JSONB, and external data integration capabilities.

---

## 🧩 Topics Covered

### 1) PostgreSQL Extensions 101

Extensions add functionality like spatial data, text search, job scheduling, auditing, etc.

#### Manage Extensions

```sql
-- List available extensions
SELECT * FROM pg_available_extensions;

-- Install an extension
CREATE EXTENSION IF NOT EXISTS pg_trgm;

-- List installed
\dx

-- Remove extension
DROP EXTENSION pg_trgm;
```

Popular ones:

| Extension            | Purpose                                  |
| -------------------- | ---------------------------------------- |
| `pg_trgm`            | Fuzzy string matching using trigrams     |
| `hstore`             | Key-value store for semi-structured data |
| `uuid-ossp`          | UUID generation functions                |
| `citext`             | Case-insensitive text                    |
| `pg_stat_statements` | Query performance statistics             |
| `postgis`            | Geospatial data and queries              |
| `postgres_fdw`       | Connect to other Postgres databases      |
| `file_fdw`           | Read CSVs directly as tables             |
| `pg_cron`            | Schedule SQL jobs inside Postgres        |
| `pg_audit`           | Auditing of SQL commands                 |

---

### 2) JSON & JSONB

Store and query semi-structured documents.

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name TEXT,
  specs JSONB
);

INSERT INTO products (name, specs) VALUES
('Phone', '{"brand": "Samsung", "ram": 8, "storage": 256}');
```

#### JSON Operators

| Operator | Description    | Example                       |
| -------- | -------------- | ----------------------------- |
| `->`     | Get JSON field | `specs->'brand'`              |
| `->>`    | Get text value | `specs->>'brand'`             |
| `#>`     | Nested path    | `specs#>'{dimensions,width}'` |
| `@>`     | Contains       | `specs @> '{"ram":8}'`        |

```sql
SELECT name FROM products WHERE specs->>'brand' = 'Samsung';
SELECT * FROM products WHERE specs @> '{"storage":256}';
```

#### Indexing JSONB

```sql
CREATE INDEX idx_products_specs_gin ON products USING GIN (specs jsonb_path_ops);
```

---

### 3) Full-Text Search (FTS)

PostgreSQL natively supports **document search**.

```sql
CREATE TABLE documents (
  id SERIAL PRIMARY KEY,
  content TEXT
);

INSERT INTO documents (content)
VALUES ('PostgreSQL is a powerful open source relational database');

-- Create a tsvector index
CREATE INDEX idx_docs_search ON documents USING GIN (to_tsvector('english', content));

-- Search
SELECT id, ts_rank(to_tsvector('english', content), plainto_tsquery('postgres database')) AS rank
FROM documents
WHERE to_tsvector('english', content) @@ plainto_tsquery('postgres database')
ORDER BY rank DESC;
```

> 🧠 Combine with `pg_trgm` for fuzzy full-text search.

---

### 4) Foreign Data Wrappers (FDWs)

FDWs let PostgreSQL query other databases like they’re local.

#### Example – PostgreSQL to PostgreSQL

```sql
CREATE EXTENSION postgres_fdw;
CREATE SERVER remoteserver FOREIGN DATA WRAPPER postgres_fdw OPTIONS (host '10.0.0.5', dbname 'analytics', port '5432');
CREATE USER MAPPING FOR current_user SERVER remoteserver OPTIONS (user 'remote_user', password 'remote_pass');

IMPORT FOREIGN SCHEMA public FROM SERVER remoteserver INTO remote_schema;

SELECT * FROM remote_schema.sales;
```

#### Example – Reading CSVs

```sql
CREATE EXTENSION file_fdw;
CREATE SERVER file_server FOREIGN DATA WRAPPER file_fdw;
CREATE FOREIGN TABLE csv_data (id INT, name TEXT, amount DECIMAL)
SERVER file_server OPTIONS (filename '/tmp/data.csv', format 'csv', header 'true');

SELECT * FROM csv_data;
```

> 💡 Use FDWs for cross-database joins and ETL pipelines.

---

### 5) PostGIS – Spatial Data

Add GIS (Geographic Information System) support.

```sql
CREATE EXTENSION postgis;
CREATE TABLE landmarks (
  id SERIAL PRIMARY KEY,
  name TEXT,
  location GEOGRAPHY(Point, 4326)
);

INSERT INTO landmarks (name, location)
VALUES ('Eiffel Tower', ST_GeogFromText('SRID=4326;POINT(2.2945 48.8584)'));

-- Query nearby locations
SELECT name, ST_Distance(location, ST_GeogFromText('SRID=4326;POINT(2.295 48.8585)')) AS distance
FROM landmarks
ORDER BY distance ASC LIMIT 5;
```

> 🌍 Perfect for mapping, logistics, and proximity search applications.

---

### 6) Advanced Indexing: Trigrams & Similarity

Using `pg_trgm` for fuzzy matching:

```sql
CREATE EXTENSION pg_trgm;
CREATE INDEX idx_name_trgm ON employees USING GIN (name gin_trgm_ops);

SELECT name, similarity(name, 'Jon') AS score
FROM employees
WHERE name %% 'Jon'
ORDER BY score DESC;
```

> 🔍 Use `%` or `%%` operators for LIKE/ILIKE performance boosts.

---

### 7) Case-Insensitive Text (`citext`)

```sql
CREATE EXTENSION citext;
CREATE TABLE users (
  email CITEXT UNIQUE
);

INSERT INTO users VALUES ('john@demo.com');
INSERT INTO users VALUES ('JOHN@DEMO.COM'); -- Error: duplicate
```

> Ensures case-insensitive uniqueness automatically.

---

### 8) Procedural Language – PL/pgSQL

Create stored procedures, triggers, and logic.

```sql
CREATE OR REPLACE FUNCTION give_raise(emp_id INT, pct NUMERIC)
RETURNS VOID AS $$
BEGIN
  UPDATE employees
  SET salary = salary * (1 + pct/100)
  WHERE id = emp_id;
END;
$$ LANGUAGE plpgsql;

-- Call
SELECT give_raise(1, 10);
```

#### Triggers Example

```sql
CREATE OR REPLACE FUNCTION log_changes() RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO audit_log(table_name, operation, changed_at)
  VALUES (TG_TABLE_NAME, TG_OP, NOW());
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER track_changes
AFTER INSERT OR UPDATE OR DELETE ON employees
FOR EACH ROW EXECUTE FUNCTION log_changes();
```

---

### 9) Mini Project – Intelligent Data Hub

Build a database that integrates structured, JSON, and remote data:

1. Use `postgres_fdw` to link external analytics DB.
2. Store customer metadata in `JSONB`.
3. Add `pg_trgm` to enable fuzzy search.
4. Use PL/pgSQL trigger to maintain audit logs.

> ✅ Combine all features for a production-grade data warehouse.

---

### 10) Cheatsheet

| Feature          | Extension                  | Example                    |
| ---------------- | -------------------------- | -------------------------- |
| Fuzzy search     | `pg_trgm`                  | `similarity('abc','abd')`  |
| Semi-structured  | `hstore`, `JSONB`          | `specs->>'ram'`            |
| Spatial          | `postgis`                  | `ST_Distance()`            |
| External data    | `postgres_fdw`, `file_fdw` | Query remote DBs           |
| Case-insensitive | `citext`                   | Unique emails              |
| Scheduling       | `pg_cron`                  | `cron.schedule('job',...)` |
| Audit            | `pg_audit`                 | Query-level audit          |
| Procedural logic | `PL/pgSQL`                 | Stored functions/triggers  |

---

## 🧠 Next Step → Part IX: Security, Auditing & Compliance

We’ll cover **RLS**, **SSL/TLS**, **encryption**, **auditing**, and **data masking** for enterprise security.
