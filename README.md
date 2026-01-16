# 📘 SQL JOINS & SUBQUERIES — COMPLETE NOTES

---

## 🔷 SQL JOINS — INTRODUCTION

A **JOIN** is used to **combine rows from two or more tables** based on a related column between them.

### Why JOINs are needed

* Data normalization splits data across tables
* JOINs help retrieve meaningful combined data
* Express **relationships between tables**

---

## 🔶 TYPES OF SQL JOINS

1️⃣ INNER JOIN
2️⃣ LEFT JOIN (LEFT OUTER JOIN)
3️⃣ RIGHT JOIN (RIGHT OUTER JOIN)
4️⃣ FULL JOIN (FULL OUTER JOIN)
5️⃣ SELF JOIN
6️⃣ CROSS JOIN

---

## 1️⃣ INNER JOIN

### 🔹 Definition

Returns **only rows that have matching values in BOTH tables**.

* Rows without matches are **excluded**
* Most commonly used JOIN

### 🔹 Syntax

```sql
SELECT columns
FROM table1
INNER JOIN table2
ON table1.common_column = table2.common_column;
```

### 🔹 Example Tables

**Customers**

| customer_id | name  | city      |
| ----------- | ----- | --------- |
| 1           | Ravi  | Hyderabad |
| 2           | Anita | Delhi     |
| 3           | John  | Chennai   |

**Orders**

| order_id | customer_id | amount |
| -------- | ----------- | ------ |
| 101      | 1           | 500    |
| 102      | 1           | 300    |
| 103      | 2           | 700    |

### 🔹 Query

```sql
SELECT c.name, o.order_id, o.amount
FROM Customers c
INNER JOIN Orders o
ON c.customer_id = o.customer_id;
```

### 🔹 Result

| name  | order_id | amount |
| ----- | -------- | ------ |
| Ravi  | 101      | 500    |
| Ravi  | 102      | 300    |
| Anita | 103      | 700    |

✔ John is excluded (no order)

---

## 2️⃣ LEFT JOIN (LEFT OUTER JOIN)

### 🔹 Definition

Returns:

* **ALL rows from LEFT table**
* **MATCHING rows from RIGHT table**
* Non-matching rows → NULLs on right side

### 🔹 Syntax

```sql
SELECT columns
FROM table1
LEFT JOIN table2
ON table1.column = table2.column;
```

### 🔹 Result

| name  | order_id | amount |
| ----- | -------- | ------ |
| Ravi  | 101      | 500    |
| Ravi  | 102      | 300    |
| Anita | 103      | 700    |
| John  | NULL     | NULL   |

✔ John included even without orders

---

## 3️⃣ RIGHT JOIN (RIGHT OUTER JOIN)

### 🔹 Definition

Returns:

* **ALL rows from RIGHT table**
* Matching rows from LEFT table
* Non-matching rows → NULLs on left side

### 🔹 Syntax

```sql
SELECT columns
FROM table1
RIGHT JOIN table2
ON table1.column = table2.column;
```

### 🔹 Example

```sql
SELECT c.name, o.order_id, o.amount
FROM Customers c
RIGHT JOIN Orders o
ON c.customer_id = o.customer_id;
```

### 🔹 Important Case (Unmatched Order)

| order_id | customer_id | amount |
| -------- | ----------- | ------ |
| 104      | 99          | 900    |

### 🔹 Result

| name | order_id | amount |
| ---- | -------- | ------ |
| NULL | 104      | 900    |

✔ Order kept, customer missing → NULL

---

## 4️⃣ FULL JOIN (FULL OUTER JOIN)

### 🔹 Definition

Returns **ALL rows from BOTH tables**.

* Matching rows → combined
* Left-only rows → NULL on right
* Right-only rows → NULL on left

### ⚠ MySQL Note

❌ MySQL does NOT support FULL JOIN directly
✔ Must be simulated

---

### 🔹 FULL JOIN Simulation (MySQL)

```sql
SELECT c.name, o.order_id, o.amount
FROM Customers c
LEFT JOIN Orders o
ON c.customer_id = o.customer_id

UNION ALL

SELECT c.name, o.order_id, o.amount
FROM Customers c
RIGHT JOIN Orders o
ON c.customer_id = o.customer_id
WHERE c.customer_id IS NULL;
```

### 🔹 Result

| name  | order_id | amount |
| ----- | -------- | ------ |
| Ravi  | 101      | 500    |
| Ravi  | 102      | 300    |
| Anita | 103      | 700    |
| John  | NULL     | NULL   |
| NULL  | 104      | 900    |

---

## 5️⃣ SELF JOIN

### 🔹 Definition

A **SELF JOIN** joins a table **to itself**.

* Uses table aliases
* No separate SELF JOIN keyword
* Used for hierarchical data

### 🔹 Example Table (Employees)

| employee_id | name  | manager_id |
| ----------- | ----- | ---------- |
| 1           | Ravi  | NULL       |
| 2           | Anita | 1          |
| 3           | John  | 1          |

### 🔹 Query

```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m
ON e.manager_id = m.employee_id;
```

### 🔹 Result

| employee | manager |
| -------- | ------- |
| Ravi     | NULL    |
| Anita    | Ravi    |
| John     | Ravi    |

---

## 6️⃣ CROSS JOIN

### 🔹 Definition

Returns the **Cartesian Product**.

Every row from table A is combined with every row from table B.

### 🔹 Syntax

```sql
SELECT columns
FROM table1
CROSS JOIN table2;
```

### 🔹 Example

**Sizes**

| size |
| ---- |
| S    |
| M    |
| L    |

**Colors**

| color |
| ----- |
| Red   |
| Blue  |

### 🔹 Result

| size | color |
| ---- | ----- |
| S    | Red   |
| S    | Blue  |
| M    | Red   |
| M    | Blue  |
| L    | Red   |
| L    | Blue  |

### 🔹 Formula

If:

* Table A → M rows
* Table B → N rows

👉 Result = **M × N rows**

✔ Powerful
⚠ Use carefully on large tables

---

# 🔷 SUBQUERIES (NESTED QUERIES)

---

## 🔹 Definition

A **Subquery** is a query written **inside another SQL query**.

* Enclosed in parentheses
* Used where expressions or tables are allowed

---

## 🔹 Key Characteristics

* Can return:

  * Single value (Scalar)
  * Single column (Multiple rows)
  * Table-like result (FROM clause)
* May be:

  * Independent
  * Correlated

---

## 🔹 Why Subqueries Are Needed

### 1️⃣ Data Dependency

```sql
SELECT name
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

---

### 2️⃣ Filtering Without JOIN

```sql
SELECT name
FROM customers
WHERE customer_id IN (
    SELECT customer_id FROM orders
);
```

---

### 3️⃣ Derived Data Sets

```sql
SELECT dept_id, avg_salary
FROM (
    SELECT dept_id, AVG(salary) avg_salary
    FROM employees
    GROUP BY dept_id
) t;
```

---

### 4️⃣ Business Logic Encapsulation

“Products priced above category average”

---

## 🔹 Types of Subqueries

### 1️⃣ Subquery in WHERE Clause

✔ Scalar

```sql
WHERE salary > (SELECT AVG(salary) FROM employees)
```

✔ Multi-row

```sql
WHERE dept_id IN (SELECT dept_id FROM departments)
```

---

### 2️⃣ Subquery in SELECT Clause (Correlated)

```sql
SELECT c.name,
       (SELECT COUNT(*)
        FROM orders o
        WHERE o.customer_id = c.customer_id) AS order_count
FROM customers c;
```

---

### 3️⃣ Subquery in FROM Clause (Derived Table)

```sql
SELECT dept_id, avg_salary
FROM (
    SELECT dept_id, AVG(salary) avg_salary
    FROM employees
    GROUP BY dept_id
) d;
```

---

### 4️⃣ Subquery in HAVING Clause

```sql
SELECT dept_id
FROM employees
GROUP BY dept_id
HAVING AVG(salary) >
      (SELECT AVG(salary) FROM employees);
```

---

## 🔹 How to Write a Subquery (Step-by-Step)

### 🧠 Step 1: Identify Dependency

Do you need derived data?

### 🧠 Step 2: Write Inner Query First

Test it independently

### 🧠 Step 3: Identify Return Type

| Result        | Use         |
| ------------- | ----------- |
| One value     | Scalar      |
| Multiple rows | IN / EXISTS |
| Table         | FROM        |

### 🧠 Step 4: Embed into Outer Query

### 🧠 Step 5: Optimize

* Index usage
* Execution plan

---

## 🔶 JOIN vs SUBQUERY

| JOIN               | SUBQUERY           |
| ------------------ | ------------------ |
| Relationship logic | Dependency logic   |
| Combines rows      | Filters rows       |
| Often faster       | Often clearer      |
| Flat structure     | Step-by-step logic |

---

# 📘 SQL VIEWS — COMPLETE & PRACTICAL EXPLANATION

---

## 🔷 1. Definition — What is a SQL View?

A **View** in SQL is a **virtual table** that is created using a **stored SELECT query**.

* A View **does not store data physically** (in most databases)
* It stores **only the SQL query definition**
* Whenever a View is queried, the database **executes the underlying SELECT query**

### ✅ Key Point

> A View is a **saved query** that behaves like a table.

---

## 🔷 2. Functionality & Execution — How Views Work Internally

### 🔹 How a View is Created

```sql
CREATE VIEW view_name AS
SELECT columns
FROM tables
WHERE conditions;
```

The database stores:

* The **query definition**
* Metadata (column names, permissions)

❌ It does NOT store result rows (except materialized views)

---

### 🔹 What Happens When You Query a View?

```sql
SELECT * FROM view_name;
```

### 🧠 Internal Execution Flow

1️⃣ User queries the View
2️⃣ SQL engine **expands the View definition**
3️⃣ Optimizer **merges the View query** with outer query
4️⃣ Execution plan is generated
5️⃣ Base tables are accessed
6️⃣ Final result is returned

📌 **Important**

> The View itself is never executed independently — it is **inlined** into the main query.

---

### 🔹 View Execution Example

```sql
CREATE VIEW active_customers AS
SELECT customer_id, name, city
FROM customers
WHERE status = 'ACTIVE';
```

Query:

```sql
SELECT name FROM active_customers WHERE city = 'Delhi';
```

Internally converted to:

```sql
SELECT name
FROM customers
WHERE status = 'ACTIVE'
  AND city = 'Delhi';
```

✔ View logic is merged
✔ Filters are optimized
✔ No performance penalty by default


## 🔷 3. Real-World Business Example

### 🏢 Business Scenario: E-Commerce Company

**Problem**

* `orders` table contains sensitive columns:

  * payment_details
  * internal_notes
* Business team needs:

  * order_id
  * customer_name
  * order_amount
  * order_status
* Without exposing sensitive data

---

### 🔹 Solution: Create a View

```sql
CREATE VIEW order_summary AS
SELECT
    o.order_id,
    c.name AS customer_name,
    o.amount,
    o.status
FROM orders o
JOIN customers c
ON o.customer_id = c.customer_id;
```

---

### 🔹 Who Uses This View?

| Role              | Usage          |
| ----------------- | -------------- |
| Business Analysts | Reports        |
| Support Team      | Order lookup   |
| BI Tools          | Dashboards     |
| Developers        | Faster queries |



## 🔹 4 Security & Data Access Control

### 🔐 Problem

Users should not see:

* Salary
* Payment info
* Internal flags

### ✔ Solution

Expose only required columns using Views

```sql
CREATE VIEW public_employee_view AS
SELECT emp_id, name, department
FROM employees;
```

✔ Grant access to View
✔ Restrict base table access

---

## 🔹 4.2 Simplifying Complex Queries

### ❌ Without View

```sql
SELECT ...
FROM orders o
JOIN customers c
JOIN payments p
JOIN shipments s
WHERE ...
```

### ✔ With View

```sql
SELECT *
FROM order_dashboard;
```

---

## 🔷 When Should You Use Views?

| Scenario               | Use View?              |
| ---------------------- | -----------------------|
| Data security          | ✅ Yes                 |
| Repeated JOIN logic    | ✅ Yes                 |
| Reporting              | ✅ Yes                 |
| Simple SELECT once     | ❌ No                  |
| High-write OLTP tables | ⚠ Careful              |
| Heavy aggregation      | ✅ (Materialized View) |

---

## 🔷 When NOT to Use Views

❌ When:

* Logic changes very frequently
* Deeply nested views (performance hit)
* Very complex correlated subqueries
* High-performance OLTP paths

---

## 🔷 Types of Views (Brief)

| Type              | Description            |
| ----------------- | ---------------------- |
| Simple View       | Single table           |
| Complex View      | Multiple tables, JOINs |
| Updatable View    | Allows DML             |
| Read-Only View    | Aggregations           |
| Materialized View | Physically stores data |

---

