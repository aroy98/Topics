# 🐘 PostgreSQL Mastery Series – Part I: Core Fundamentals

---

## 🎯 Objective

By the end of this part, you’ll be able to **install, configure, and interact** with PostgreSQL confidently using both the **psql CLI** and **GUI tools**, and understand essential SQL operations (DDL, DML, DQL).

---

## 🧩 Topics Covered

1. **Installation & Setup**

   * Installation methods:

     * Windows/macOS/Linux native installers
     * Docker container setup (`postgres` official image)
     * Homebrew (macOS): `brew install postgresql`
   * Starting and stopping PostgreSQL service
   * Setting up `postgres` user and password
   * Connecting via `psql`

   ```bash
   psql -U postgres -h localhost -p 5432
   ```

   * Common GUI Clients:

     * pgAdmin 4
     * DBeaver
     * TablePlus

---

2. **Connecting & Exploring Databases**

   * List databases:

     ```sql
     \l
     ```
   * Connect to a database:

     ```sql
     \c mydb
     ```
   * List tables:

     ```sql
     \dt
     ```
   * Show table schema:

     ```sql
     \d table_name
     ```

---

3. **Creating and Managing Databases & Schemas**

   ```sql
   CREATE DATABASE company_db;
   \c company_db;

   CREATE SCHEMA hr;
   SET search_path TO hr;
   ```

   * Difference between **Database** vs **Schema**:

     * **Database**: top-level container.
     * **Schema**: namespace inside a database.

---

4. **Data Types Overview**

   | Category  | Examples                                            |
   | --------- | --------------------------------------------------- |
   | Numeric   | `INTEGER`, `BIGINT`, `DECIMAL`, `NUMERIC`, `SERIAL` |
   | Text      | `CHAR`, `VARCHAR`, `TEXT`                           |
   | Date/Time | `DATE`, `TIME`, `TIMESTAMP`, `INTERVAL`             |
   | Boolean   | `BOOLEAN`                                           |
   | JSON      | `JSON`, `JSONB`                                     |
   | Array     | `INTEGER[]`, `TEXT[]`                               |

   ```sql
   CREATE TABLE products (
     id SERIAL PRIMARY KEY,
     name TEXT NOT NULL,
     tags TEXT[],
     specs JSONB,
     price DECIMAL(10,2),
     created_at TIMESTAMP DEFAULT NOW()
   );
   ```

---

5. **Basic SQL Commands**

   **Data Definition Language (DDL)**:

   ```sql
   CREATE TABLE employees (
     id SERIAL PRIMARY KEY,
     name VARCHAR(50),
     department VARCHAR(50),
     salary NUMERIC(10,2)
   );

   ALTER TABLE employees ADD COLUMN hire_date DATE;
   DROP TABLE employees;
   ```

   **Data Manipulation Language (DML):**

   ```sql
   INSERT INTO employees (name, department, salary)
   VALUES ('Alice', 'HR', 50000.00);

   UPDATE employees SET salary = 52000 WHERE name = 'Alice';
   DELETE FROM employees WHERE name = 'Alice';
   ```

   **Data Query Language (DQL):**

   ```sql
   SELECT name, salary FROM employees
   WHERE department = 'HR'
   ORDER BY salary DESC;
   ```

---

6. **Handling NULLs**

   ```sql
   SELECT * FROM employees WHERE salary IS NULL;
   SELECT COALESCE(salary, 0) FROM employees;
   ```

   > 💡 `COALESCE` replaces NULLs with a default value.

---

7. **Filtering, Sorting, and Limiting**

   ```sql
   SELECT * FROM products
   WHERE price > 100
   ORDER BY price DESC
   LIMIT 10 OFFSET 5;
   ```

---

8. **Schema & Table Management Best Practices**

   * Use **snake_case** for table/column names.
   * Use plural for tables: `employees`, `orders`.
   * Keep schema-specific tables grouped.
   * Prefer `UUID` or `BIGSERIAL` for primary keys.

---

9. **Mini Project: Employee Database**
   Create a small employee database with the following requirements:

   ```sql
   CREATE DATABASE hr_db;
   \c hr_db;

   CREATE TABLE departments (
     id SERIAL PRIMARY KEY,
     name VARCHAR(100) UNIQUE NOT NULL
   );

   CREATE TABLE employees (
     id SERIAL PRIMARY KEY,
     name VARCHAR(100) NOT NULL,
     dept_id INT REFERENCES departments(id),
     salary DECIMAL(10,2),
     hire_date DATE DEFAULT CURRENT_DATE
   );

   INSERT INTO departments (name) VALUES ('Engineering'), ('Finance'), ('HR');

   INSERT INTO employees (name, dept_id, salary) VALUES
   ('Alice', 1, 80000),
   ('Bob', 2, 60000),
   ('Carol', 3, 55000);

   SELECT e.name, d.name AS department, salary
   FROM employees e
   JOIN departments d ON e.dept_id = d.id;
   ```

---

10. **Cheatsheet Summary**

| Task              | Command                       |
| ----------------- | ----------------------------- |
| List databases    | `\l`                          |
| Connect to DB     | `\c db_name`                  |
| List tables       | `\dt`                         |
| Show table schema | `\d table_name`               |
| Create database   | `CREATE DATABASE db;`         |
| Create table      | `CREATE TABLE ...;`           |
| Insert data       | `INSERT INTO ... VALUES ...;` |
| Query data        | `SELECT ... FROM ...;`        |
| Update data       | `UPDATE ... SET ...;`         |
| Delete data       | `DELETE FROM ...;`            |

---

## 🧠 Next Step → Part II: SQL Mastery

Dive deeper into JOINs, CTEs, window functions, and advanced querying techniques to optimize and scale SQL workloads.
