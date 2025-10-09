# 🐘 PostgreSQL Mastery Series – Part II: SQL Mastery

---

## 🎯 Objective

By the end of this part, you’ll master **complex querying** with PostgreSQL — including **JOINs, subqueries, CTEs, window functions**, and **aggregate analysis**.

---

## 🧩 Topics Covered

### 1. **JOINs – Combining Tables**

JOINs allow combining rows from multiple tables based on related columns.

#### 🧠 Types of JOINs

| Type       | Description                                        | Example                            |
| ---------- | -------------------------------------------------- | ---------------------------------- |
| INNER JOIN | Returns matching rows from both tables             | Employees with departments         |
| LEFT JOIN  | All rows from left table, matching rows from right | Employees even without departments |
| RIGHT JOIN | All rows from right table, matching from left      | Departments even without employees |
| FULL JOIN  | Combines LEFT and RIGHT                            | All employees and departments      |

```sql
SELECT e.name, d.name AS department
FROM employees e
INNER JOIN departments d ON e.dept_id = d.id;

SELECT e.name, d.name AS department
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.id;
```

---

### 2. **Subqueries (Nested Queries)**

Subqueries can appear in WHERE, FROM, or SELECT clauses.

#### Example – Subquery in WHERE

```sql
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

#### Example – Subquery in FROM (Derived Table)

```sql
SELECT dept_name, avg_salary
FROM (
  SELECT d.name AS dept_name, AVG(e.salary) AS avg_salary
  FROM employees e
  JOIN departments d ON e.dept_id = d.id
  GROUP BY d.name
) AS dept_avg
WHERE avg_salary > 60000;
```

---

### 3. **Common Table Expressions (CTEs)**

CTEs make queries more readable and reusable.

```sql
WITH high_earners AS (
  SELECT name, salary, dept_id
  FROM employees
  WHERE salary > 60000
)
SELECT h.name, d.name AS department
FROM high_earners h
JOIN departments d ON h.dept_id = d.id;
```

> 💡 Tip: You can chain multiple CTEs for multi-step transformations.

---

### 4. **Window Functions**

Perform calculations across related rows **without collapsing results**.

#### Ranking Example

```sql
SELECT name, department, salary,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank
FROM employees;
```

#### Running Totals

```sql
SELECT name, salary,
       SUM(salary) OVER (ORDER BY id) AS running_total
FROM employees;
```

#### Difference Between Aggregate and Window Functions

| Feature             | Aggregate | Window |
| ------------------- | --------- | ------ |
| Collapses rows      | ✅         | ❌      |
| Works with GROUP BY | ✅         | ❌      |
| Works with OVER()   | ❌         | ✅      |

---

### 5. **GROUP BY & Aggregate Functions**

```sql
SELECT d.name AS department, COUNT(e.id) AS emp_count, AVG(e.salary) AS avg_salary
FROM employees e
JOIN departments d ON e.dept_id = d.id
GROUP BY d.name
HAVING AVG(e.salary) > 60000;
```

| Function      | Purpose       |
| ------------- | ------------- |
| COUNT()       | Count rows    |
| SUM()         | Sum values    |
| AVG()         | Average value |
| MAX() / MIN() | Extremes      |

---

### 6. **Set Operations**

Combine results from multiple queries.

```sql
-- UNION: distinct
SELECT name FROM employees WHERE dept_id = 1
UNION
SELECT name FROM employees WHERE dept_id = 2;

-- UNION ALL: includes duplicates
SELECT name FROM employees WHERE dept_id = 1
UNION ALL
SELECT name FROM employees WHERE dept_id = 2;

-- INTERSECT: common rows
SELECT name FROM employees WHERE dept_id = 1
INTERSECT
SELECT name FROM employees WHERE dept_id = 2;

-- EXCEPT: rows in first not in second
SELECT name FROM employees WHERE dept_id = 1
EXCEPT
SELECT name FROM employees WHERE dept_id = 2;
```

---

### 7. **Views & Materialized Views**

#### Normal View

```sql
CREATE VIEW active_employees AS
SELECT name, department, salary
FROM employees
WHERE salary > 40000;

SELECT * FROM active_employees;
```

#### Materialized View

```sql
CREATE MATERIALIZED VIEW dept_salary_summary AS
SELECT dept_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY dept_id;

REFRESH MATERIALIZED VIEW dept_salary_summary;
```

> 💡 Use materialized views for caching heavy queries.

---

### 8. **Mini Project – Employee Analytics Dashboard**

Create analytics reports for a growing HR database.

```sql
-- Top 3 highest paid employees per department
SELECT name, dept_id, salary
FROM (
  SELECT *, RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS r
  FROM employees
) ranked
WHERE r <= 3;

-- Department salary distribution
SELECT d.name AS department, MIN(e.salary) AS min_salary, MAX(e.salary) AS max_salary, AVG(e.salary) AS avg_salary
FROM employees e
JOIN departments d ON e.dept_id = d.id
GROUP BY d.name;

-- Employee counts by department using CTE
WITH dept_counts AS (
  SELECT dept_id, COUNT(*) AS total FROM employees GROUP BY dept_id
)
SELECT d.name AS department, total FROM dept_counts dc
JOIN departments d ON dc.dept_id = d.id;
```

---

### 9. **Cheatsheet Summary**

| Concept           | Syntax                                        | Example                   |
| ----------------- | --------------------------------------------- | ------------------------- |
| JOIN              | `SELECT ... FROM A JOIN B ON ...`             | `INNER JOIN`, `LEFT JOIN` |
| Subquery          | `(SELECT ... FROM ...)`                       | Nested filtering          |
| CTE               | `WITH alias AS (...) SELECT ...`              | Readable chaining         |
| Window            | `func() OVER (PARTITION BY ... ORDER BY ...)` | Ranking, totals           |
| GROUP BY          | `GROUP BY col HAVING condition`               | Aggregation               |
| UNION             | `UNION`, `UNION ALL`                          | Merge results             |
| View              | `CREATE VIEW name AS SELECT ...`              | Logical layer             |
| Materialized View | `CREATE MATERIALIZED VIEW`                    | Cached results            |

---

## 🧠 Next Step → Part III: Data Modeling & Constraints

You’ll learn about **normalization**, **primary/foreign keys**, **check constraints**, **partitioning**, and how to structure efficient relational models.
