# 🐘 PostgreSQL Mastery Series – Part III: Data Modeling & Constraints

---

## 🎯 Objective

By the end of this part, you’ll understand how to **design efficient, normalized database schemas**, apply **constraints for data integrity**, and explore **partitioning strategies** for scalability.

---

## 🧩 Topics Covered

### 1. **Database Design Principles**

A good data model ensures data integrity, consistency, and scalability.

#### Core Concepts

* **Entity:** A real-world object (e.g., Employee, Product, Customer)
* **Attribute:** Describes entity properties (e.g., name, salary)
* **Relationship:** Connection between entities (e.g., Employee → Department)

#### Steps for Designing a Database

1. Identify entities and relationships.
2. Normalize the schema to avoid redundancy.
3. Define appropriate constraints.
4. Optimize with indexes and partitioning if needed.

---

### 2. **Normalization & Denormalization**

Normalization organizes data to reduce redundancy.

#### Common Normal Forms

| Normal Form | Rule                                 | Example                           |
| ----------- | ------------------------------------ | --------------------------------- |
| 1NF         | Each field holds atomic values       | Avoid arrays in a column          |
| 2NF         | 1NF + remove partial dependencies    | Split composite keys              |
| 3NF         | 2NF + remove transitive dependencies | Move derived data to other tables |

> ⚙️ **Denormalization** improves read performance by allowing some redundancy.

---

### 3. **Primary & Foreign Keys**

Ensure entity uniqueness and relationships between tables.

```sql
CREATE TABLE departments (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE employees (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  dept_id INT REFERENCES departments(id) ON DELETE SET NULL
);
```

> 💡 Use `ON DELETE CASCADE` to automatically delete dependent records.

---

### 4. **Unique & Check Constraints**

Ensure logical correctness of data.

```sql
CREATE TABLE accounts (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  balance DECIMAL(10,2) DEFAULT 0 CHECK (balance >= 0)
);
```

* **UNIQUE:** Ensures distinct values per column.
* **CHECK:** Validates business logic (e.g., non-negative balance).

---

### 5. **Default & Generated Columns**

Provide default values or computed fields.

```sql
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  amount DECIMAL(10,2) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  tax DECIMAL(10,2) GENERATED ALWAYS AS (amount * 0.18) STORED
);
```

> 💡 Generated columns simplify calculations and ensure consistency.

---

### 6. **Composite & Surrogate Keys**

| Type          | Description                               | Example                  |
| ------------- | ----------------------------------------- | ------------------------ |
| Composite Key | Multiple columns form a unique identifier | `(order_id, product_id)` |
| Surrogate Key | Artificial primary key (auto-generated)   | `SERIAL`, `UUID`         |

```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

> 🧠 Surrogate keys simplify relationships; composite keys enforce natural constraints.

---

### 7. **Partitioning Strategies**

Partitioning splits a table for performance and scalability.

#### Types of Partitioning

| Type  | Description                  | Example               |
| ----- | ---------------------------- | --------------------- |
| Range | Based on numeric/date ranges | Orders per year       |
| List  | Based on discrete values     | Country-specific data |
| Hash  | Evenly distributes rows      | Random partitioning   |

#### Example – Range Partitioning

```sql
CREATE TABLE sales (
  id SERIAL,
  sale_date DATE NOT NULL,
  amount NUMERIC(10,2)
) PARTITION BY RANGE (sale_date);

CREATE TABLE sales_2024 PARTITION OF sales
FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');
```

#### Example – List Partitioning

```sql
CREATE TABLE users (
  id SERIAL,
  region TEXT
) PARTITION BY LIST (region);

CREATE TABLE users_us PARTITION OF users FOR VALUES IN ('US');
CREATE TABLE users_eu PARTITION OF users FOR VALUES IN ('EU');
```

> 💡 PostgreSQL automatically routes data to the correct partition based on the key.

---

### 8. **Schema Design Example: E-Commerce**

```sql
CREATE TABLE customers (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL
);

CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  price DECIMAL(10,2) CHECK (price > 0)
);

CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  customer_id INT REFERENCES customers(id),
  order_date DATE DEFAULT CURRENT_DATE
);

CREATE TABLE order_items (
  order_id INT REFERENCES orders(id) ON DELETE CASCADE,
  product_id INT REFERENCES products(id),
  quantity INT CHECK (quantity > 0),
  PRIMARY KEY (order_id, product_id)
);
```

> ⚙️ This schema ensures referential integrity and supports analytics efficiently.

---

### 9. **Best Practices**

* Always define a **primary key**.
* Use **CHECK** constraints instead of app logic.
* Normalize until 3NF, denormalize if necessary for analytics.
* Use **foreign keys** with proper cascading rules.
* Prefer **UUIDs** for distributed systems.

---

### 10. **Mini Project – HR Management System**

Design a normalized HR schema that supports employees, departments, roles, and attendance.

```sql
CREATE TABLE roles (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50) UNIQUE NOT NULL
);

CREATE TABLE departments (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50) UNIQUE NOT NULL
);

CREATE TABLE employees (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  dept_id INT REFERENCES departments(id),
  role_id INT REFERENCES roles(id),
  salary NUMERIC(10,2) CHECK (salary > 0)
);

CREATE TABLE attendance (
  id SERIAL PRIMARY KEY,
  emp_id INT REFERENCES employees(id) ON DELETE CASCADE,
  attendance_date DATE DEFAULT CURRENT_DATE,
  status VARCHAR(10) CHECK (status IN ('Present','Absent','Leave'))
);
```

---

### 11. **Cheatsheet Summary**

| Concept     | Syntax                             | Example                 |
| ----------- | ---------------------------------- | ----------------------- |
| Primary Key | `PRIMARY KEY(col)`                 | `PRIMARY KEY(id)`       |
| Foreign Key | `REFERENCES table(col)`            | `REFERENCES dept(id)`   |
| Unique      | `UNIQUE(col)`                      | `email UNIQUE`          |
| Check       | `CHECK(condition)`                 | `CHECK(salary > 0)`     |
| Default     | `DEFAULT value`                    | `DEFAULT CURRENT_DATE`  |
| Generated   | `GENERATED ALWAYS AS (...) STORED` | `amount * 0.18`         |
| Partition   | `PARTITION BY RANGE/LIST/HASH`     | Range partition by year |

---

## 🧠 Next Step → Part IV: Administration & Configuration

You’ll learn PostgreSQL architecture, configuration parameters, role management, and performance tuning at the server level.
