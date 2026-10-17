# BASIC SQL-NOTES

> **Practice table used in all examples:** `employees`

| emp_id | name    | department | salary | city      | manager_id |
|--------|---------|------------|--------|-----------|------------|
| 1      | Aarav   | IT         | 60000  | Mumbai    | NULL       |
| 2      | Priya   | HR         | 45000  | Pune      | 1          |
| 3      | Rohan   | IT         | 55000  | Mumbai    | 1          |
| 4      | Sneha   | Finance    | 70000  | Delhi     | 1          |
| 5      | Karan   | HR         | 40000  | Pune      | 2          |
| 6      | Anjali  | IT         | 65000  | Bangalore | 1          |

---

## 1. INTRODUCTION TO SQL

### Definition
**SQL (Structured Query Language)** is the standard language used to store, retrieve, update, and manage data in **relational databases** (MySQL, PostgreSQL, SQL Server, Oracle, etc.). Data is stored in **tables** made of **rows (records)** and **columns (fields)**.

### Types of SQL Commands
| Type | Full Form | Purpose | Commands |
|------|-----------|---------|----------|
| DDL | Data Definition Language | Defines structure | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` |
| DML | Data Manipulation Language | Changes data | `INSERT`, `UPDATE`, `DELETE` |
| DQL | Data Query Language | Fetches data | `SELECT` |
| DCL | Data Control Language | Permissions | `GRANT`, `REVOKE` |
| TCL | Transaction Control Language | Manages transactions | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

### Syntax (creating a table)
```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype
);
```

### Example
```sql
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    name VARCHAR(50),
    department VARCHAR(30),
    salary INT,
    city VARCHAR(30),
    manager_id INT
);
```

###  Important Points:-
- SQL keywords are **not case-sensitive** (`SELECT` = `select`), but writing them in UPPERCASE is good practice.
- Every statement ends with a **semicolon (`;`)**.
- **Primary Key** = unique + not null; identifies each row.
- Table and column names may be case-sensitive depending on the OS/database (MySQL on Linux is case-sensitive for table names).

---

## 2. SELECT AND SELECT DISTINCT

### Definition
- **`SELECT`** retrieves data from one or more columns of a table.
- **`SELECT DISTINCT`** retrieves only **unique values**, removing duplicates.

### Syntax
```sql
-- Specific columns
SELECT column1, column2 FROM table_name;

-- All columns
SELECT * FROM table_name;

-- Unique values
SELECT DISTINCT column1 FROM table_name;
```

### Example
```sql
SELECT name, salary FROM employees;

SELECT * FROM employees;

SELECT DISTINCT department FROM employees;
```
**Output of the last query:** `IT`, `HR`, `Finance`

###  Important Points:-
- `*` means "all columns" – avoid it in real projects; select only what you need (faster and cleaner).
- `SELECT` can also do calculations: `SELECT salary * 12 FROM employees;`
- `DISTINCT` applies to the **whole combination** of columns: `SELECT DISTINCT department, city` returns unique *pairs*.
- `NULL` is treated as one distinct value by `DISTINCT`.
- Order of execution: `FROM` → `WHERE` → `SELECT` (SQL reads `FROM` first).

---

## 3. WHERE

### Definition
The **`WHERE`** clause **filters rows** based on a condition. Only rows where the condition is true are returned.

### Syntax
```sql
SELECT column1, column2
FROM table_name
WHERE condition;
```

### Example
```sql
SELECT * FROM employees
WHERE department = 'IT';

SELECT name, salary FROM employees
WHERE salary > 50000;
```

###  Important Points:-
- Text/string values go in **single quotes** `'IT'`; numbers don't need quotes.
- `WHERE` filters **rows before grouping**. (To filter groups, use `HAVING` – covered in Aggregates.)
- `WHERE` can be used with `SELECT`, `UPDATE`, and `DELETE`.
- **Never run `UPDATE` or `DELETE` without `WHERE`** – it affects every row!
- You **cannot use aggregate functions** (`SUM`, `AVG`...) directly in `WHERE`.

---

## 4. AND, OR, NOT

### Definition
Logical operators used to **combine multiple conditions** in `WHERE`.

| Operator | Meaning |
|----------|---------|
| `AND` | **All** conditions must be true |
| `OR` | **At least one** condition must be true |
| `NOT` | **Reverses** a condition |

### Syntax
```sql
SELECT * FROM table_name WHERE condition1 AND condition2;
SELECT * FROM table_name WHERE condition1 OR condition2;
SELECT * FROM table_name WHERE NOT condition;
```

### Example
```sql
-- IT employees earning more than 58000
SELECT * FROM employees
WHERE department = 'IT' AND salary > 58000;

-- Employees in Mumbai or Pune
SELECT * FROM employees
WHERE city = 'Mumbai' OR city = 'Pune';

-- Everyone except HR
SELECT * FROM employees
WHERE NOT department = 'HR';
```

### Important Points:-
- **Precedence:** `NOT` → `AND` → `OR`. Use **parentheses** to control logic:
  ```sql
  WHERE department = 'IT' AND (city = 'Mumbai' OR city = 'Pune')
  ```
- `AND` narrows results; `OR` widens results.
- Common interview trap: `A OR B AND C` is read as `A OR (B AND C)`.

---

## 5. COMPARISON OPERATORS

### Definition
Operators used in conditions to **compare two values**.

| Operator | Meaning |
|----------|---------|
| `=` | Equal to |
| `<>` or `!=` | Not equal to |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal to |
| `<=` | Less than or equal to |

### Syntax
```sql
SELECT * FROM table_name WHERE column operator value;
```

### Example
```sql
SELECT * FROM employees WHERE salary >= 60000;
SELECT * FROM employees WHERE department <> 'IT';
SELECT * FROM employees WHERE city = 'Delhi';
```

### Important Points:-
- SQL uses a **single `=`** for comparison (not `==` like programming languages).
- `<>` is the standard "not equal"; `!=` also works in MySQL.
- You **cannot** use `= NULL` or `!= NULL` – use `IS NULL` / `IS NOT NULL`.
- Strings are compared alphabetically; dates can be compared too (`WHERE hire_date > '2024-01-01'`).

---

## 6. BETWEEN, IN, NOT IN

### Definition
- **`BETWEEN`** – checks if a value lies within a range (**inclusive** of both ends).
- **`IN`** – checks if a value matches **any value in a list**.
- **`NOT IN`** – checks if a value matches **none** of the values in a list.

### Syntax
```sql
SELECT * FROM table_name WHERE column BETWEEN value1 AND value2;
SELECT * FROM table_name WHERE column IN (value1, value2, ...);
SELECT * FROM table_name WHERE column NOT IN (value1, value2, ...);
```

### Example
```sql
-- Salary from 45000 to 60000 (both included)
SELECT * FROM employees
WHERE salary BETWEEN 45000 AND 60000;

-- Employees from Mumbai, Delhi, or Pune
SELECT * FROM employees
WHERE city IN ('Mumbai', 'Delhi', 'Pune');

-- Everyone not in HR or Finance
SELECT * FROM employees
WHERE department NOT IN ('HR', 'Finance');
```

###  Important Points:-
- `BETWEEN 45000 AND 60000` ≡ `salary >= 45000 AND salary <= 60000`.
- Always put the **smaller value first** in `BETWEEN`.
- `IN` is a cleaner replacement for many `OR` conditions.
- **`NOT IN` + `NULL` trap:** if the list contains `NULL`, `NOT IN` returns **no rows**. Use `NOT EXISTS` or filter NULLs out.
- `NOT BETWEEN` also exists for values outside a range.

---

## 7. LIKE AND WILDCARDS

### Definition
**`LIKE`** searches for a **pattern** in text. **Wildcards** are special characters that stand for unknown characters.

| Wildcard | Meaning |
|----------|---------|
| `%` | Zero, one, or **many** characters |
| `_` | Exactly **one** character |

### Syntax
```sql
SELECT * FROM table_name WHERE column LIKE 'pattern';
```

### Example
```sql
-- Names starting with 'A'
SELECT * FROM employees WHERE name LIKE 'A%';

-- Names ending with 'a'
SELECT * FROM employees WHERE name LIKE '%a';

-- Names containing 'an'
SELECT * FROM employees WHERE name LIKE '%an%';

-- Names with exactly 5 letters
SELECT * FROM employees WHERE name LIKE '_____';

-- Second letter is 'a'
SELECT * FROM employees WHERE name LIKE '_a%';
```

### Pattern Cheat Sheet
| Pattern | Matches |
|---------|---------|
| `'A%'` | Starts with A |
| `'%A'` | Ends with A |
| `'%A%'` | Contains A anywhere |
| `'_A%'` | A is the 2nd letter |
| `'A_%_%'` | Starts with A, at least 3 characters long |

### Important Points:-
- `LIKE` is for **text only**, always use quotes.
- In MySQL, `LIKE` is **case-insensitive** by default (depends on collation).
- Use `NOT LIKE` to exclude a pattern.
- Patterns starting with `%` (like `'%an'`) are **slower** on large tables since indexes can't be used.
- To search for a literal `%` or `_`, use `ESCAPE`: `LIKE '50\%'`.

---

## 8. IS NULL AND IS NOT NULL

### Definition
**`NULL`** means a **missing / unknown value** (it is *not* zero and *not* an empty string). `IS NULL` and `IS NOT NULL` test for it.

### Syntax
```sql
SELECT * FROM table_name WHERE column IS NULL;
SELECT * FROM table_name WHERE column IS NOT NULL;
```

### Example
```sql
-- Employees with no manager (top boss)
SELECT * FROM employees WHERE manager_id IS NULL;

-- Employees who have a manager
SELECT * FROM employees WHERE manager_id IS NOT NULL;
```

### Important Points:-
- `WHERE manager_id = NULL` **never works** – NULL can't be compared with `=`.
- `NULL = NULL` is **not true** – it's *unknown*.
- Any arithmetic with NULL gives NULL: `5 + NULL = NULL`.
- Aggregate functions (`SUM`, `AVG`, `COUNT(column)`) **ignore NULLs**; `COUNT(*)` counts all rows.
- `0` and `''` (empty string) are **not** NULL.

---

## 9. ORDER BY AND LIMIT

### Definition
- **`ORDER BY`** sorts the result set (ascending by default).
- **`LIMIT`** restricts the **number of rows** returned.

### Syntax
```sql
SELECT column1, column2
FROM table_name
ORDER BY column1 ASC | DESC
LIMIT number;

-- Skip rows (pagination)
LIMIT offset, count;
-- or
LIMIT count OFFSET offset;
```

### Example
```sql
-- Lowest to highest salary
SELECT * FROM employees ORDER BY salary ASC;

-- Highest to lowest salary
SELECT * FROM employees ORDER BY salary DESC;

-- Top 3 highest paid employees
SELECT * FROM employees ORDER BY salary DESC LIMIT 3;

-- Sort by department (A-Z), then salary (high to low)
SELECT * FROM employees ORDER BY department ASC, salary DESC;

-- 2nd highest salary (skip 1 row, take 1)
SELECT DISTINCT salary FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

### Important Points:-
- Default order is **`ASC`** (ascending).
- You can sort by **multiple columns**; the second column breaks ties in the first.
- You can sort by **column position**: `ORDER BY 2`, or by an **alias**.
- `ORDER BY` always comes **before `LIMIT`**, and both are at the **end** of the query.
- `LIMIT` is MySQL/PostgreSQL syntax. SQL Server uses `TOP`; Oracle uses `FETCH FIRST`.
- Top-N and "Nth highest salary" are **very common interview questions**.
- NULLs appear first in `ASC` order in MySQL.

---

## 10. ALIASES(AS)

### Definition
An **alias** gives a **temporary name** to a column or table to make output or queries more readable. It exists only during that query.

### Syntax
```sql
-- Column alias
SELECT column_name AS alias_name FROM table_name;

-- Table alias
SELECT alias.column_name FROM table_name AS alias;
```

### Example
```sql
-- Column alias
SELECT name AS employee_name, salary * 12 AS annual_salary
FROM employees;

-- Alias with spaces (use quotes/backticks)
SELECT name AS `Employee Name` FROM employees;

-- Table alias
SELECT e.name, e.salary
FROM employees AS e
WHERE e.department = 'IT';
```

### Important Points:-
- The `AS` keyword is **optional**: `SELECT salary * 12 annual_salary` works too (but `AS` is more readable).
- If the alias contains **spaces**, wrap it in backticks `` ` ` `` (MySQL) or quotes.
- Aliases are essential for **JOINs and self-joins**, where the same table is used twice.
- A column alias **cannot be used in `WHERE`** (because `WHERE` runs before `SELECT`), but it **can** be used in `ORDER BY`.

---

## ✅ Practice Questions (Solve These!)

1. Display all unique cities from the `employees` table.
2. Find employees in the IT department earning more than 55000.
3. Show employees whose salary is between 40000 and 60000.
4. Find employees whose name starts with 'S' or ends with 'n'.
5. List employees who do **not** have a manager.
6. Display the top 2 highest-paid employees.
7. Show name and annual salary (`salary * 12`) using an alias, sorted high to low.
8. Find employees working in cities other than Mumbai and Delhi.
9. Find the 3rd highest salary.
10. Show all employees sorted by department (A–Z) and then by salary (high to low).

---
*Author: Priya Yadav — Learning, practicing, and improving one query at a time.*
