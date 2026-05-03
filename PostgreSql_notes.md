# 📘 PostgreSQL (Beginner to Advanced)

## Summary

Essential PostgreSQL commands for database operations including connection, CRUD operations, data types, constraints, operators, clauses, functions, relationships, joins, views, subqueries, stored routines, and more.

## Key Points

* **psql** is PostgreSQL's interactive terminal
* SQL commands end with semicolons `;`
* Meta-commands (starting with `\`) don't require semicolons
* Always be careful with UPDATE and DELETE operations (use WHERE clauses)
* Constraints and conditions ensure data integrity and valid filtering

---

## Connection & Database Management

**What it is:** `psql` is the command-line interface to interact with PostgreSQL. You connect to the server first, then select a database to work in.

**Syntax:**

```sql
psql -U postgres
```

**Example — Database operations:**

| Command                          | Purpose             |
| -------------------------------- | ------------------- |
| `CREATE DATABASE database_name;` | Create new database |
| `\list` or `\l`                  | Show all databases  |
| `\c database_name`               | Switch database     |

```sql
-- Alternative way to list databases
SELECT datname FROM pg_database;
```

---

## Data Types in PostgreSQL

**What it is:** Data types define what kind of value a column can hold. Choosing the right type ensures data accuracy and storage efficiency.

| Type           | Description                 |
| -------------- | --------------------------- |
| `SERIAL`       | Auto-increment number field |
| `VARCHAR(n)`   | Variable-length string      |
| `INTEGER`      | Whole number                |
| `DECIMAL(p,s)` | Fixed precision number      |
| `DATE`         | Stores date only            |
| `BOOLEAN`      | True/False                  |
| `TEXT`         | Unlimited length text       |

---

## Table Constraints

**What it is:** Constraints are rules applied to columns to enforce data integrity. They prevent invalid data from being inserted into the table.

**When to use:** Always define constraints at table creation to avoid bad data from entering the system — this is standard practice in every production database.

| Constraint    | Purpose                     |
| ------------- | --------------------------- |
| `PRIMARY KEY` | Unique row identifier       |
| `NOT NULL`    | Prevents empty values       |
| `UNIQUE`      | No duplicate values allowed |
| `DEFAULT`     | Sets default value          |
| `CHECK`       | Validates condition         |
| `FOREIGN KEY` | Refers to another table key |

---

## Create Table

**What it is:** `CREATE TABLE` defines a new table with its columns, data types, and constraints. Constraints can be applied inline on each column or given a custom name using the `CONSTRAINT` keyword — named constraints are easier to identify and drop later.

**Syntax:**

```sql
CREATE TABLE table_name (
  column_name DATA_TYPE CONSTRAINT,
  ...
  CONSTRAINT constraint_name CHECK (condition)
);
```

**Example:**

```sql
CREATE TABLE emp (
  emp_id    SERIAL PRIMARY KEY NOT NULL,
  fname     VARCHAR(100) NOT NULL,
  lname     VARCHAR(100) NOT NULL,
  email     VARCHAR(100) NOT NULL UNIQUE,
  dept      VARCHAR(100),
  salary    DECIMAL(8,2) NOT NULL DEFAULT 30000,
  hire_date DATE NOT NULL DEFAULT CURRENT_DATE,
  mob_no    VARCHAR(15),
  -- Named constraint: easier to reference when dropping
  CONSTRAINT chk_mob_no CHECK (LENGTH(mob_no) = 10)
);
```

> Without a name, PostgreSQL auto-generates one (e.g. `emp_mob_no_check`), which is hard to reference later. Naming it explicitly keeps things clean.

---

## CRUD Operations

**What it is:** CRUD stands for Create, Read, Update, Delete — the four fundamental operations performed on any database table. Every application in the industry uses these operations constantly.

### INSERT (Create)

**Syntax:**

```sql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);
```

**Example:**

```sql
INSERT INTO emp (fname, lname, email, dept, hire_date)
VALUES ('Fatima', 'Farooqui', 'test@gmail.com', 'H.O.E', '2025-10-10');
```

### SELECT (Read)

**Syntax:**

```sql
SELECT column1, column2 FROM table_name WHERE condition;
```

**Example:**

```sql
SELECT * FROM emp;
SELECT fname, lname FROM emp;
SELECT * FROM emp WHERE dept = 'IT';
```

### UPDATE (Modify)

**Syntax:**

```sql
UPDATE table_name
SET column = value
WHERE condition;
```

**Example:**

```sql
UPDATE emp
SET salary = 15000
WHERE emp_id = 2;
```

⚠ Always use WHERE to avoid modifying all rows

### DELETE (Remove)

**Syntax:**

```sql
DELETE FROM table_name WHERE condition;
```

**Example:**

```sql
DELETE FROM emp
WHERE emp_id = 1;
```

⚠ Without WHERE → deletes all rows

---

## Operators & Filtering Clauses

**What it is:** Operators are used inside `WHERE` clauses to filter rows based on conditions. They are the backbone of any meaningful SELECT, UPDATE, or DELETE query.

### Relational Operators

| Operator    | Use                |
| ----------- | ------------------ |
| `=`         | Equal              |
| `<>` / `!=` | Not equal          |
| `>` / `<`   | Greater/Less than  |
| `>=` / `<=` | Greater/Less equal |

### Logical Operators

| Operator | Use                         |
| -------- | --------------------------- |
| `AND`    | All conditions must be true |
| `OR`     | Any condition true          |
| `NOT`    | Negates condition           |

### BETWEEN

Filters rows where a value falls within an inclusive range.

```sql
SELECT * FROM emp WHERE salary BETWEEN 20000 AND 50000;
```

### IN

Filters rows where a value matches any item in a list.

```sql
SELECT * FROM emp WHERE dept IN ('IT','Intern');
```

### IS NULL / IS NOT NULL

Checks whether a column value is empty or not.

```sql
SELECT * FROM emp WHERE dept IS NULL;
SELECT * FROM emp WHERE dept IS NOT NULL;
```

---

## DISTINCT

**What it is:** Removes duplicate values from query results, returning only unique values.

**Syntax:**

```sql
SELECT DISTINCT column FROM table_name;
```

**Example:**

```sql
SELECT DISTINCT dept FROM emp;
```

---

## ORDER BY

**What it is:** Sorts the result set by one or more columns in ascending (`ASC`) or descending (`DESC`) order.

**Syntax:**

```sql
SELECT * FROM table_name ORDER BY column ASC|DESC;
```

**Example:**

```sql
SELECT * FROM emp ORDER BY fname DESC;
```

---

## LIMIT

**What it is:** Restricts the number of rows returned by a query. Commonly used with `ORDER BY` to get top-N results.

**Syntax:**

```sql
SELECT * FROM table_name LIMIT n;
```

**Example:**

```sql
SELECT * FROM emp LIMIT 3;
```

---

## LIKE (Pattern Matching)

**What it is:** Filters rows based on a text pattern using wildcard characters. Used when you don't know the exact value but know part of it.

**Syntax:**

```sql
SELECT * FROM table_name WHERE column LIKE 'pattern';
```

| Pattern | Meaning              |
| ------- | -------------------- |
| `J%`    | Starts with J        |
| `%a`    | Ends with a          |
| `%i%`   | Contains i           |
| `__`    | Exactly 2 characters |
| `_a%`   | Second char is a     |

**Example:**

```sql
SELECT * FROM emp WHERE fname LIKE 'J%';
SELECT * FROM emp WHERE fname LIKE '%a';
SELECT * FROM emp WHERE fname LIKE '%i%';
SELECT * FROM emp WHERE dept  LIKE '__';
SELECT * FROM emp WHERE fname LIKE '_a%';
```

---

## Aggregate Functions

**What it is:** Aggregate functions perform a calculation on a set of rows and return a single value. They are used to summarize data.

**When to use:** Any time you need totals, counts, averages, or min/max across rows — used constantly in industry dashboards and reports.

**Syntax:**

```sql
SELECT AGGREGATE_FUNCTION(column) FROM table_name;
```

| Function  | Purpose                 |
| --------- | ----------------------- |
| `COUNT()` | Count number of rows    |
| `SUM()`   | Add up values           |
| `AVG()`   | Calculate average       |
| `MIN()`   | Find the smallest value |
| `MAX()`   | Find the largest value  |

**Example:**

```sql
SELECT COUNT(emp_id) FROM emp;
SELECT SUM(salary)   FROM emp;
SELECT AVG(salary)   FROM emp;
SELECT MAX(salary)   FROM emp;
```

Finding employee(s) with minimum salary:

```sql
SELECT fname, lname, salary FROM emp
WHERE salary = (SELECT MIN(salary) FROM emp);
```

---

## GROUP BY

**What it is:** Groups rows that share the same value in a column, then applies an aggregate function to each group.

**When to use:** Whenever you want per-category totals or counts — e.g. total sales per region, headcount per department.

**Syntax:**

```sql
SELECT column, AGGREGATE_FUNCTION(column)
FROM table_name
GROUP BY column;
```

**Example:**

```sql
-- Count employees per department
SELECT dept, COUNT(fname) FROM emp GROUP BY dept;

-- Total salary spend per department
SELECT dept, SUM(salary) FROM emp GROUP BY dept;
```

---

## HAVING Clause

**What it is:** `HAVING` filters the results of a `GROUP BY` query. It is like `WHERE`, but applied **after** grouping and aggregation — meaning you can filter on aggregate values like `COUNT()`, `SUM()`, `AVG()` etc.

**When to use:** Use `HAVING` when you want to filter groups, not individual rows — e.g. show only departments with more than 5 employees, or customers who spent above a threshold. Very common in industry reporting queries.

**WHERE vs HAVING:**

| Clause   | Filters           | Applied         |
| -------- | ----------------- | --------------- |
| `WHERE`  | Individual rows   | Before grouping |
| `HAVING` | Aggregated groups | After grouping  |

**Syntax:**

```sql
SELECT column, AGGREGATE_FUNCTION(column)
FROM table_name
GROUP BY column
HAVING condition;
```

**Example — Students enrolled in more than 2 courses:**

```sql
SELECT s.name AS student_name, COUNT(c.id) AS no_of_courses
FROM enrollment e
JOIN students s ON e.student_id = s.id
JOIN course c   ON c.id = e.course_id
GROUP BY s.name
HAVING COUNT(c.id) > 2;
```

**Example — Students who spent more than 1500 in total fees:**

```sql
SELECT s.name AS student_name, SUM(c.fees) AS total_spend
FROM enrollment e
JOIN students s ON e.student_id = s.id
JOIN course c   ON c.id = e.course_id
GROUP BY s.name
HAVING SUM(c.fees) > 1500;
```

> ⚠ You cannot use a column alias (like `total_spend`) inside `HAVING` — you must repeat the aggregate function.

---

## String Functions

**What it is:** Built-in functions that operate on text values — used to format, clean, extract, or transform string data.

**When to use:** Whenever you need to display names in a specific format, clean messy data, or extract parts of a string — very common in data pipelines and reporting.

**Syntax:**

```sql
SELECT STRING_FUNCTION(column) FROM table_name;
```

| Function        | Purpose                        |
| --------------- | ------------------------------ |
| `CONCAT()`      | Join strings together          |
| `CONCAT_WS()`   | Join with a separator          |
| `SUBSTR()`      | Extract part of a string       |
| `LEFT/RIGHT()`  | Get n chars from left/right    |
| `LENGTH()`      | Count characters               |
| `UPPER/LOWER()` | Change case                    |
| `TRIM()`        | Remove leading/trailing spaces |
| `REPLACE()`     | Substitute text                |
| `REVERSE()`     | Reverse the string             |

**Example:**

```sql
SELECT CONCAT(fname, lname) AS fullname FROM emp;
SELECT CONCAT_WS(' ', fname, lname) AS fullname FROM emp;
SELECT SUBSTR(fname, 1, 3) FROM emp;
SELECT REPLACE(dept, 'IT', 'Software') FROM emp;
SELECT REVERSE(fname) FROM emp;   -- rarely used
SELECT LENGTH(fname) FROM emp;
SELECT UPPER(fname) FROM emp;
SELECT LOWER(fname) FROM emp;

-- Task-based examples
SELECT CONCAT_WS(':', emp_id, fname, lname, dept) AS task1 FROM emp;
SELECT CONCAT_WS(':', emp_id, fname, UPPER(dept)) AS task2 FROM emp;
```

---

## ALTER TABLE

**What it is:** `ALTER TABLE` modifies the structure of an existing table — add/remove columns, rename columns or tables, and change column properties or constraints — without losing existing data.

**When to use:** Used in production when requirements change and a table needs to be updated without being dropped and recreated.

### Adding a Column

**Syntax:**

```sql
ALTER TABLE table_name ADD COLUMN column_name DATA_TYPE DEFAULT value;
```

**Example:**

```sql
ALTER TABLE emp
ADD COLUMN status VARCHAR(100) DEFAULT 'ACTIVE';
```

### Removing a Column

**Syntax:**

```sql
ALTER TABLE table_name DROP COLUMN column_name;
```

**Example:**

```sql
ALTER TABLE emp
DROP COLUMN status;
```

⚠ This action is irreversible — the column and its data are gone

### Renaming a Column

**Syntax:**

```sql
ALTER TABLE table_name RENAME COLUMN old_name TO new_name;
```

**Example:**

```sql
ALTER TABLE emp
RENAME COLUMN dept TO department;
```

### Renaming a Table

**Syntax:**

```sql
ALTER TABLE old_name RENAME TO new_name;
```

**Example:**

```sql
ALTER TABLE emp
RENAME TO employee;
```

### Changing a Column's Data Type

**Syntax:**

```sql
ALTER TABLE table_name
ALTER COLUMN column_name SET DATA TYPE new_type;
```

**Example:**

```sql
ALTER TABLE employee
ALTER COLUMN department
SET DATA TYPE VARCHAR(110);
```

### Adding a Constraint

Always give constraints a custom name using `CONSTRAINT constraint_name` — this makes them easy to identify and drop later. Without a name, PostgreSQL auto-generates one (e.g. `employee_mob_no_check`), which is hard to reference.

**Syntax:**

```sql
ALTER TABLE table_name
ADD CONSTRAINT constraint_name CHECK (condition);
```

**Example:**

```sql
ALTER TABLE employee
ADD CONSTRAINT chk_mob_no CHECK (LENGTH(mob_no) = 10);

-- This will FAIL because mob_no has 11 digits
INSERT INTO employee (mob_no, fname, lname, email)
VALUES (12345678932, 'x', 'y', 'x@wy.com');
```

### Dropping a Constraint

**Syntax:**

```sql
ALTER TABLE table_name DROP CONSTRAINT constraint_name;
```

**Example:**

```sql
ALTER TABLE employee
DROP CONSTRAINT chk_mob_no;
```

---

## CASE Expression

**What it is:** The `CASE` expression works like an if-else statement inside a query. It evaluates conditions in order and returns a value for the first condition that is true.

**When to use:** Use it to categorize, label, or calculate values based on conditions directly inside a query — very common in reporting and dashboards.

**Syntax:**

```sql
CASE
  WHEN condition1 THEN result1
  WHEN condition2 THEN result2
  ...
  ELSE default_result
END AS alias_name
```

**Example — Salary Classification:**

```sql
SELECT fname, salary,
  CASE
    WHEN salary <= 30000 THEN 'Low Salary'
    WHEN salary > 30000 AND salary <= 50000 THEN 'Mid Salary'
    ELSE 'High Salary'
  END AS sal_check
FROM employee;
```

The same result using `BETWEEN`:

```sql
SELECT fname, salary,
  CASE
    WHEN salary <= 30000 THEN 'Low Salary'
    WHEN salary BETWEEN 30001 AND 50000 THEN 'Mid Salary'
    ELSE 'High Salary'
  END AS sal_check
FROM employee;
```

**Example — Calculating Bonus:**

CASE can also perform calculations. If no `ELSE` is provided and no condition matches, the result is `NULL`.

```sql
SELECT fname, salary,
  CASE
    WHEN salary > 0 THEN ROUND(salary * 0.10)
  END AS bonus
FROM employee;
```

**Example — GROUP BY with CASE:**

```sql
SELECT
  CASE
    WHEN salary <= 30000 THEN 'Low Salary'
    WHEN salary > 30000 AND salary <= 50000 THEN 'Mid Salary'
    ELSE 'High Salary'
  END AS sal_check,
  COUNT(*) AS employee_count
FROM employee
GROUP BY sal_check;
```

---

## Subquery

**What it is:** A **subquery** is a query nested inside another query. The inner query runs first and its result is used by the outer query. Subqueries can appear in `WHERE`, `FROM`, or `SELECT` clauses.

**When to use:** Use subqueries when the value you need to filter on can only be determined by running another query first — e.g. finding employees who earn the max salary in their department. In industry, subqueries appear in reporting, data validation, and complex filtering. For performance-critical applications they are sometimes replaced with JOINs.

**Syntax:**

```sql
-- In WHERE clause
SELECT * FROM table_name
WHERE column = (SELECT AGGREGATE(column) FROM table_name WHERE condition);

-- As a derived table in FROM clause
SELECT * FROM (
  SELECT ... FROM table_name
) alias_name;
```

**Example — Employee with the highest salary in the IT department:**

```sql
SELECT * FROM employee
WHERE salary = (
  SELECT MAX(salary) FROM employee
  WHERE department = 'IT'
);
```

**Example — Employee with the highest salary in their own department (correlated subquery):**

A **correlated subquery** references a column from the outer query (`e.department`). It re-runs once for every row in the outer query.

```sql
SELECT * FROM employee e
WHERE salary = (
  SELECT MAX(salary) FROM employee
  WHERE department = e.department
);
```

**Example — Top spending student using a subquery in FROM:**

```sql
SELECT * FROM (
  SELECT s.name        AS student_name,
         COUNT(c.id)   AS no_of_courses,
         SUM(c.fees)   AS total_spend
  FROM enrollment e
  JOIN students s ON e.student_id = s.id
  JOIN course c   ON c.id = e.course_id
  GROUP BY s.name
) a
ORDER BY total_spend DESC
LIMIT 1;
```

> The inner query (`a`) groups and aggregates the data. The outer query then sorts it and picks the top 1 row.

---

## Relationships

**What it is:** A **relationship** links two tables together using a `FOREIGN KEY`. The foreign key in one table references the `PRIMARY KEY` of another, ensuring only valid, existing values can be inserted — this is called **referential integrity**.

**When to use:** Used in virtually every production database. Instead of storing all data in one large table (which causes duplication), data is split across related tables and linked via foreign keys.

There are three main types:

| Type             | Description                                | Example             |
| ---------------- | ------------------------------------------ | ------------------- |
| One-to-One       | One row in A links to exactly one row in B | User ↔ User Profile |
| **One-to-Many**  | One row in A links to many rows in B       | Customer → Orders   |
| **Many-to-Many** | Many rows in A link to many rows in B      | Students ↔ Courses  |

> 💡 **Industry note:** One-to-Many is by far the most commonly used — e.g. users & posts, customers & orders, employees & departments. Many-to-Many is widely used in subscription, tagging, and enrollment systems. One-to-One is the least common and mainly used to split a large table for performance or security.

---

### One-to-Many Relationship

One record in the parent table can have **many** related records in the child table, but each child record belongs to only **one** parent. The foreign key always lives on the "many" side.

**Syntax:**

```sql
CREATE TABLE child_table (
  ...
  parent_id INTEGER NOT NULL,
  FOREIGN KEY (parent_id) REFERENCES parent_table(id)
);
```

**Example — One customer, many orders:**

```sql
-- Parent table (the "one" side)
CREATE TABLE customers (
  id    SERIAL PRIMARY KEY NOT NULL,
  name  TEXT NOT NULL,
  email TEXT UNIQUE,
  phone TEXT UNIQUE NOT NULL
);

-- Child table (the "many" side)
CREATE TABLE orders (
  id      SERIAL PRIMARY KEY NOT NULL,
  date    DATE NOT NULL DEFAULT CURRENT_DATE,
  price   DECIMAL(8,2) NOT NULL,
  cust_id INTEGER NOT NULL,
  FOREIGN KEY (cust_id) REFERENCES customers (id)
);
```

Inserting data — parent must exist first:

```sql
INSERT INTO customers (name, email, phone) VALUES
  ('Karan Verma',  'karan.verma@email.com', '9876501234'),
  ('Priya Nair',   'priya.nair@email.com',  '9765412300'),
  ('Manish Gupta', NULL,                    '9898989898');

INSERT INTO orders (price, cust_id) VALUES (349.75, 3);
```

⚠ Inserting an order with a `cust_id` that doesn't exist in `customers` will be rejected

---

### Many-to-Many Relationship

A **many-to-many** relationship exists when one record in table A can relate to many records in table B, and vice versa. This requires a **junction table** that holds the foreign keys of both sides.

**Syntax:**

```sql
CREATE TABLE junction_table (
  ...
  table_a_id INTEGER NOT NULL,
  table_b_id INTEGER NOT NULL,
  FOREIGN KEY (table_a_id) REFERENCES table_a(id),
  FOREIGN KEY (table_b_id) REFERENCES table_b(id)
);
```

**Example — Students and Courses:**

```sql
CREATE TABLE students (
  id   SERIAL PRIMARY KEY NOT NULL,
  name TEXT NOT NULL
);

CREATE TABLE course (
  id   SERIAL PRIMARY KEY NOT NULL,
  name TEXT NOT NULL,
  fees DECIMAL(8,2) NOT NULL
);

-- Junction table — the many-to-many bridge
CREATE TABLE enrollment (
  id         SERIAL PRIMARY KEY NOT NULL,
  date       DATE NOT NULL DEFAULT CURRENT_DATE,
  student_id INTEGER NOT NULL,
  course_id  INTEGER NOT NULL,
  FOREIGN KEY (student_id) REFERENCES students(id),
  FOREIGN KEY (course_id)  REFERENCES course(id)
);
```

Always insert into parent tables first, then the junction table:

```sql
INSERT INTO students (name) VALUES
  ('John Smith'), ('Emma Johnson'), ('Michael Brown'),
  ('Sarah Wilson'), ('David Lee'), ('Lisa Anderson'),
  ('James Taylor'), ('Maria Garcia');

INSERT INTO course (name, fees) VALUES
  ('Mathematics 101', 499.99), ('Physics 101', 549.99),
  ('Computer Science 101', 599.99), ('English Literature', 449.99),
  ('History 101', 399.99), ('Chemistry 101', 529.99),
  ('Biology 101', 519.99), ('Economics 101', 479.99);

INSERT INTO enrollment (date, student_id, course_id) VALUES
  ('2024-01-15', 1, 1), ('2024-01-15', 1, 3), ('2024-01-16', 1, 5),
  ('2024-01-14', 2, 2), ('2024-01-14', 2, 4), ('2024-01-15', 2, 6),
  ('2024-01-16', 3, 1), ('2024-01-16', 3, 2), ('2024-01-17', 3, 3),
  ('2024-01-17', 3, 7), ('2024-01-15', 4, 4), ('2024-01-15', 4, 8),
  ('2024-01-16', 5, 3), ('2024-01-16', 5, 6), ('2024-01-17', 5, 7),
  ('2024-01-14', 6, 1), ('2024-01-14', 6, 5), ('2024-01-15', 6, 8),
  ('2024-01-17', 7, 2), ('2024-01-17', 7, 4),
  ('2024-01-15', 8, 1), ('2024-01-15', 8, 3), ('2024-01-16', 8, 6);
```

---

## Joins

**What it is:** Joins combine rows from two or more tables based on a related column. Instead of storing everything in one table, joins let you keep data normalized and pull it together only when needed.

**When to use:** Any time you need data from more than one table in a single result — used constantly in every real-world application.

### CROSS JOIN

Returns every possible combination of rows from both tables (cartesian product). Rarely useful in practice.

**Syntax:**

```sql
SELECT * FROM table_a CROSS JOIN table_b;
```

**Example:**

```sql
SELECT * FROM customers CROSS JOIN orders;
```

### INNER JOIN

Returns only the rows where there is a match in **both** tables. Non-matching rows are excluded.

**Syntax:**

```sql
SELECT * FROM table_a a
INNER JOIN table_b b ON a.id = b.foreign_key;
```

**Example:**

```sql
SELECT * FROM customers c
INNER JOIN orders d ON c.id = d.cust_id;

-- With GROUP BY: total orders per customer
SELECT c.id, c.name, COUNT(d.id) AS no_of_orders
FROM customers c
INNER JOIN orders d ON c.id = d.cust_id
GROUP BY c.id, c.name;

-- Total amount spent per customer
SELECT c.id, c.name, SUM(d.price) AS total_spent
FROM customers c
INNER JOIN orders d ON c.id = d.cust_id
GROUP BY c.id, c.name;
```

### LEFT JOIN

Returns **all rows from the left table** and matching rows from the right. If there is no match, right side columns show `NULL`.

**Syntax:**

```sql
SELECT * FROM table_a a
LEFT JOIN table_b b ON a.id = b.foreign_key;
```

**Example:**

```sql
SELECT * FROM customers c
LEFT JOIN orders d ON c.id = d.cust_id;
```

> Use this when you want all customers — even those who haven't placed any orders.

### RIGHT JOIN

Returns **all rows from the right table** and matching rows from the left. If there is no match, left side columns show `NULL`.

**Syntax:**

```sql
SELECT * FROM table_a a
RIGHT JOIN table_b b ON a.id = b.foreign_key;
```

**Example:**

```sql
SELECT * FROM customers c
RIGHT JOIN orders d ON c.id = d.cust_id;
```

> Use this when you want all orders — even if the linked customer record is missing.

### Join Types Summary

| Join Type    | Returns                                                |
| ------------ | ------------------------------------------------------ |
| `CROSS JOIN` | All combinations of both tables                        |
| `INNER JOIN` | Only rows with a match in both tables                  |
| `LEFT JOIN`  | All left rows + matching right rows (NULL if no match) |
| `RIGHT JOIN` | All right rows + matching left rows (NULL if no match) |

### Joining Multiple Tables

Chain multiple `JOIN` clauses to pull data through a junction table.

**Syntax:**

```sql
SELECT ... FROM junction_table j
JOIN table_a a ON j.a_id = a.id
JOIN table_b b ON j.b_id = b.id;
```

**Example — Full enrollment details and aggregates:**

```sql
-- All details
SELECT s.name AS student_name, c.name AS course_name,
       e.date AS enroll_date, c.fees AS course_fee
FROM enrollment e
JOIN students s ON e.student_id = s.id
JOIN course c   ON c.id = e.course_id;

-- Courses count and total spend per student
SELECT s.name AS student_name,
       COUNT(c.id) AS no_of_courses,
       SUM(c.fees) AS total_spend
FROM enrollment e
JOIN students s ON e.student_id = s.id
JOIN course c   ON c.id = e.course_id
GROUP BY s.name;
```

---

## EXTRACT

**What it is:** `EXTRACT` pulls out a specific part of a date value — such as year, month, or day. It can be used in `SELECT` or `GROUP BY` to analyze data by time periods.

**When to use:** Used in analytics and reporting to break data down by time — e.g. monthly revenue, yearly enrollment counts.

**Syntax:**

```sql
EXTRACT(part FROM date_column)
```

Common parts: `YEAR`, `MONTH`, `DAY`, `HOUR`, `MINUTE`

**Example — Enrollments and revenue grouped by year:**

```sql
SELECT COUNT(c.id)               AS no_of_courses,
       SUM(c.fees)               AS total_spend,
       EXTRACT(YEAR FROM e.date) AS year
FROM enrollment e
JOIN students s ON e.student_id = s.id
JOIN course c   ON c.id = e.course_id
GROUP BY year;
```

---

## Views

**What it is:** A **view** is a saved query stored in the database under a name. It behaves like a virtual table — you can `SELECT` from it just like a real table, but it doesn't store data itself. Every time you query the view, it runs the underlying query fresh.

**When to use:** Used in industry to simplify complex joins queried repeatedly, hide sensitive columns from certain users, and keep query logic in one place. Very common in analytics layers and API backends.

**Syntax:**

```sql
-- Create
CREATE VIEW view_name AS
<your query>;

-- Query it like a table
SELECT * FROM view_name;

-- Remove it
DROP VIEW view_name;
```

**Example — Save a complex join as a view:**

```sql
CREATE VIEW temp_query AS
SELECT s.name AS student_name, c.name AS course_name,
       e.date AS enroll_date, c.fees AS course_fee
FROM enrollment e
JOIN students s ON e.student_id = s.id
JOIN course c   ON c.id = e.course_id;

-- Now query it simply
SELECT * FROM temp_query;
```

---

## GROUP BY ROLLUP

**What it is:** `ROLLUP` is an extension of `GROUP BY` that automatically generates **subtotals and a grand total** row alongside your regular grouped rows — all in a single query.

**When to use:** Used in reports and dashboards where you need both per-group totals and an overall total — e.g. total sales per region plus a company-wide total at the bottom.

**Syntax:**

```sql
SELECT column, AGGREGATE_FUNCTION(column)
FROM table_name
GROUP BY ROLLUP(column);
```

**Example — Course count per student with a grand total row:**

```sql
SELECT COALESCE(s.name, 'Total') AS student_name,
       COUNT(c.id) AS no_of_courses
FROM enrollment e
JOIN students s ON e.student_id = s.id
JOIN course c   ON c.id = e.course_id
GROUP BY ROLLUP(s.name)
ORDER BY SUM(c.id);
```

The result includes one row per student **plus** an extra summary row representing the total across all students.

### COALESCE

**What it is:** `COALESCE` returns the **first non-NULL value** from a list of arguments. In the `ROLLUP` example above, the grand total row has `NULL` in `s.name` (because it represents all students, not one). `COALESCE` replaces that `NULL` with a readable label like `'Total'`.

**When to use:** Any time you want to replace `NULL` values with a default label or fallback value — in ROLLUP summaries, LEFT JOIN results, or any nullable column.

**Syntax:**

```sql
COALESCE(value1, value2, ...)
```

**Example:**

```sql
SELECT COALESCE(NULL, NULL, 'Total');  -- Returns: 'Total'
SELECT COALESCE('Alice', 'Total');     -- Returns: 'Alice'
```

---

## Stored Routines

**What it is:** Stored routines are reusable blocks of SQL logic saved directly in the database. There are two types — **Procedures** and **User Defined Functions**. Both are written in `PL/pgSQL`, PostgreSQL's procedural language.

**When to use:** Used in industry to encapsulate business logic inside the database, avoid repetition, enforce consistency, and improve security by exposing only the routine instead of raw table access. Common in ERP systems, payroll engines, and any system with complex recurring operations.

---

### Procedure

**What it is:** A stored procedure is a named block of SQL that performs an action (like INSERT, UPDATE, DELETE). It does **not** return a value — it is called with `CALL` and executes the operation.

**When to use:** Use procedures for operations that modify data — salary updates, batch inserts, status changes. In industry, procedures are used to wrap DML operations so application code stays clean and the logic lives in one central place in the database.

**Syntax:**

```sql
CREATE PROCEDURE procedure_name(param1 datatype, param2 datatype)
LANGUAGE plpgsql
AS $$
  BEGIN
    -- SQL logic here
  END;
$$;

-- Call it
CALL procedure_name(value1, value2);
```

**Example — Update an employee's salary:**

```sql
CREATE PROCEDURE up_sal(p_emp_id INT, p_salary DECIMAL(8,2))
LANGUAGE plpgsql
AS $$
  BEGIN
    UPDATE employee
    SET salary = p_salary
    WHERE emp_id = p_emp_id;
  END;
$$;

-- Call the procedure
CALL up_sal(8, 48000);
```

---

### User Defined Function (UDF)

**What it is:** A user defined function is a named block of SQL that **returns a value** — either a single scalar value or a full table of results. It can be used directly inside a `SELECT` statement just like a built-in function.

**When to use:** Use functions when you need to reuse a calculation or query that returns a result — e.g. finding the highest-paid employee in any given department. In industry, UDFs are used in reporting layers, APIs, and anywhere business logic needs to be reusable and composable inside queries.

**Syntax:**

```sql
CREATE OR REPLACE FUNCTION function_name(param datatype)
RETURNS return_type AS
$$
  BEGIN
    RETURN QUERY
    -- SELECT query here;
  END;
$$
LANGUAGE plpgsql;

-- Call it
SELECT * FROM function_name(value);
```

**Example — Get the highest paid employee in a given department:**

```sql
CREATE OR REPLACE FUNCTION max_emp_sal_br_dept(f_dept TEXT)
RETURNS TABLE (emp_id INT, fname TEXT, salary DECIMAL(8,2)) AS
$$
  BEGIN
    RETURN QUERY
    SELECT e.emp_id, e.fname, e.salary
    FROM employee e
    WHERE e.department = f_dept
      AND e.salary = (
        SELECT MAX(e2.salary) FROM employee e2
        WHERE e2.department = f_dept
      );
  END;
$$
LANGUAGE plpgsql;

-- Call it for the IT department
SELECT * FROM max_emp_sal_br_dept('IT');
```

**Procedure vs Function:**

| Feature        | Procedure              | Function                   |
| -------------- | ---------------------- | -------------------------- |
| Returns value  | No                     | Yes                        |
| Called with    | `CALL`                 | `SELECT`                   |
| Used for       | DML actions            | Calculations / queries     |
| Used in SELECT | No                     | Yes                        |

---

## Window Functions

**What it is:** Window functions perform a calculation across a set of rows that are related to the current row — similar to aggregate functions, but **without collapsing the rows into one**. Every row keeps its own identity in the result while also getting an extra calculated column alongside it.

**When to use:** Use window functions when you need per-row calculations that involve other rows — e.g. running totals, rankings, comparing a row's value to the previous/next row, or comparing an employee's salary to their department average. Used heavily in industry for analytics, reporting dashboards, and data science pipelines.

### OVER()

`OVER()` is the clause that turns a regular aggregate into a window function. The calculation is applied across all rows (or a defined window of rows) without grouping.

**Syntax:**

```sql
SELECT column, AGGREGATE_FUNCTION(column) OVER(ORDER BY column)
FROM table_name;
```

**Example — Running average of salary ordered by salary:**

```sql
SELECT fname, salary, AVG(salary) OVER(ORDER BY salary)
FROM employee;
```

> Each row shows its own salary plus the running average of all salaries up to and including that row.

---

### ROW_NUMBER()

**What it is:** Assigns a unique sequential number to each row in the result set. The numbering restarts when used with `PARTITION BY`.

**When to use:** Use when you need to number rows for pagination, pick the first record per group, or de-duplicate data. Very common in industry ETL pipelines and APIs.

**Syntax:**

```sql
SELECT ROW_NUMBER() OVER(ORDER BY column), column FROM table_name;

-- With PARTITION BY — restarts numbering per group
SELECT ROW_NUMBER() OVER(PARTITION BY column), column FROM table_name;
```

**Example:**

```sql
-- Number all employees ordered by first name
SELECT ROW_NUMBER() OVER(ORDER BY fname), fname, department, salary
FROM employee;

-- Number employees within each department (restarts per department)
SELECT ROW_NUMBER() OVER(PARTITION BY department), fname, department, salary
FROM employee;
```

---

### RANK() and DENSE_RANK()

**What it is:** Both assign a rank to each row based on a sort order. The difference is how they handle ties — `RANK()` skips numbers after a tie, `DENSE_RANK()` does not.

**When to use:** Use when you need leaderboards, top-N per group, or any ranked output. Used in industry for sales rankings, performance reviews, and competition results.

**Syntax:**

```sql
SELECT column, RANK()       OVER(ORDER BY column DESC) FROM table_name;
SELECT column, DENSE_RANK() OVER(ORDER BY column DESC) FROM table_name;
```

**Example:**

```sql
-- RANK: if two people tie at rank 1, the next person gets rank 3 (skips 2)
SELECT fname, salary, RANK() OVER(ORDER BY salary DESC)
FROM employee;

-- DENSE_RANK: if two people tie at rank 1, the next person gets rank 2 (no skip)
SELECT fname, salary, DENSE_RANK() OVER(ORDER BY salary DESC)
FROM employee;
```

| Function       | Ties behaviour          | Example ranks    |
| -------------- | ----------------------- | ---------------- |
| `RANK()`       | Skips numbers after tie | 1, 1, 3, 4       |
| `DENSE_RANK()` | No skip after tie       | 1, 1, 2, 3       |

---

### LAG()

**What it is:** `LAG()` gives access to the value of the **previous row** in the ordered window. It returns `NULL` for the first row since there is no row before it.

**When to use:** Use when you need to compare a row's value to the one before it — e.g. month-over-month salary change, previous order value. Common in time-series analysis and financial reporting.

**Syntax:**

```sql
SELECT column, LAG(column) OVER(ORDER BY column) FROM table_name;
```

**Example:**

```sql
SELECT fname, salary, LAG(salary) OVER(ORDER BY salary DESC)
FROM employee;
```

> Each row shows its own salary and the salary of the person ranked just above them.

---

### LEAD()

**What it is:** `LEAD()` gives access to the value of the **next row** in the ordered window. It returns `NULL` for the last row since there is no row after it.

**When to use:** Use when you need to look ahead — e.g. comparing current salary to the next lower salary, or previewing the next event in a sequence.

**Syntax:**

```sql
SELECT column, LEAD(column) OVER(ORDER BY column) FROM table_name;
```

**Example:**

```sql
SELECT fname, salary, LEAD(salary) OVER(ORDER BY salary DESC)
FROM employee;
```

> Each row shows its own salary and the salary of the person ranked just below them.

---

## CTE (Common Table Expression)

**What it is:** A CTE is a temporary named result set defined at the top of a query using the `WITH` keyword. It works like a subquery but is defined once and can be referenced by name in the main query — making complex queries much easier to read and maintain.

**When to use:** Use a CTE when a subquery would need to be repeated, or when a query is getting too nested and hard to follow. In industry, CTEs are the preferred way to break down complex multi-step queries — they are cleaner than nested subqueries and easier to debug. Most analytics and reporting queries in production use CTEs.

**Syntax:**

```sql
WITH cte_name AS (
  -- your subquery here
)
SELECT ... FROM main_table
JOIN cte_name ON condition;
```

**Example — Find employees earning above their department's average salary:**

```sql
WITH avg_sal AS (
  SELECT department, AVG(salary) AS average
  FROM employee
  GROUP BY department
)
SELECT e.fname, e.department, e.salary, a.average
FROM employee e
JOIN avg_sal a ON a.department = e.department
WHERE e.salary > a.average;
```

> The CTE `avg_sal` calculates the average salary per department first. The main query then joins it against the employee table and filters for employees who beat their department's average.

⚠ A CTE only exists for the duration of the single query it belongs to — it is not stored in the database like a View.

---

## Triggers

**What it is:** A trigger is a function that **automatically runs** before or after a specific event on a table — such as INSERT, UPDATE, or DELETE. It consists of two parts: a **trigger function** (the logic) and a **trigger** (which attaches the function to a table event).

**When to use:** Use triggers to enforce business rules automatically at the database level — e.g. prevent negative salaries, log changes to an audit table, auto-update a timestamp. In industry, triggers are used for auditing, data validation, and enforcing constraints that are too complex for a simple CHECK constraint.

⚠ Overusing triggers can make database behaviour hard to debug since they fire silently in the background. Use them only when the logic genuinely belongs in the database.

**Syntax:**

```sql
-- Step 1: Create the trigger function
CREATE OR REPLACE FUNCTION function_name()
RETURNS TRIGGER AS
$$
  BEGIN
    -- logic using NEW (new row values) or OLD (old row values)
    RETURN NEW;
  END;
$$
LANGUAGE plpgsql;

-- Step 2: Attach it to a table event
CREATE TRIGGER trigger_name
BEFORE|AFTER INSERT|UPDATE|DELETE ON table_name
FOR EACH ROW
EXECUTE FUNCTION function_name();
```

**Example — Prevent negative salary on update:**

```sql
-- Step 1: Trigger function — if salary goes negative, reset it to 0
CREATE OR REPLACE FUNCTION no_negative_salary()
RETURNS TRIGGER AS
$$
  BEGIN
    IF NEW.salary < 0 THEN
      NEW.salary := 0;
    END IF;
    RETURN NEW;
  END;
$$
LANGUAGE plpgsql;

-- Step 2: Attach to the employee table, fires before every UPDATE
CREATE TRIGGER before_update_salary
BEFORE UPDATE ON employee
FOR EACH ROW
EXECUTE FUNCTION no_negative_salary();

-- Test it — even though -245 is passed, the trigger resets it to 0
CALL up_sal(1, -245);
```

> `NEW` refers to the incoming row values during an INSERT or UPDATE. `OLD` refers to the existing row values before the change.

---

## Utility Commands

| Command         | Purpose                  |
| --------------- | ------------------------ |
| `\! cls`        | Clear terminal (Windows) |
| `\! clear`      | Clear terminal (Linux)   |
| `\q`            | Quit psql                |
| `\?`            | Meta-commands help       |
| `\h`            | SQL command help         |
| `\d table_name` | Describe table           |
| `\dt`           | List tables              |

---

## Key Takeaways

* CRUD = Create, Read, Update, Delete — the foundation of every application
* Aggregate functions summarize data; GROUP BY groups it by category
* HAVING filters groups after aggregation; WHERE filters rows before
* String functions format and clean text data
* ALTER TABLE modifies table structure without losing data
* CASE expression adds if-else logic directly inside queries
* Subqueries nest one query inside another; correlated subqueries run once per row
* FOREIGN KEY links tables and enforces referential integrity
* One-to-Many is the most common relationship; Many-to-Many uses a junction table
* Joins combine data from multiple tables; INNER excludes non-matches, LEFT/RIGHT keep all rows from one side
* EXTRACT pulls a specific part (year, month, day) from a date
* Views save complex queries as reusable virtual tables
* GROUP BY ROLLUP generates subtotals and a grand total in one query
* COALESCE returns the first non-NULL value — useful for replacing NULLs with labels
* Procedures perform actions (DML) and are called with CALL; Functions return values and are used in SELECT
* Window functions calculate across related rows without collapsing them — unlike GROUP BY
* ROW_NUMBER, RANK, DENSE_RANK assign numbers/ranks per row; RANK skips after ties, DENSE_RANK does not
* LAG accesses the previous row's value; LEAD accesses the next row's value
* CTEs (WITH clause) define a named temporary result — cleaner and more readable than nested subqueries
* Triggers fire automatically on INSERT/UPDATE/DELETE — use for auditing and enforcing complex rules

---

## Credits

These notes were researched, written, and compiled by **Al Saim Shakeel**.

| | |
|---|---|
| 👤 **Author** | Al Saim Shakeel |
| 🐙 **GitHub** | [github.com/alsaim7](https://github.com/alsaim7) |
| 💼 **LinkedIn** | [linkedin.com/in/alsaimshakeel7](https://www.linkedin.com/in/alsaimshakeel7/) |
| 🌐 **Portfolio** | [alsaim.pages.dev](https://alsaim.pages.dev/) |

> If you found these notes helpful, feel free to star the repo or connect on LinkedIn!
