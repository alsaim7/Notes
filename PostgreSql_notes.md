# PostgreSQL Commands

## Summary

Essential PostgreSQL commands for database operations including connection, CRUD operations, data types, constraints, operators, clauses, functions, and utility commands.

## Key Points

* **psql** is PostgreSQL's interactive terminal
* SQL commands end with semicolons `;`
* Meta-commands (starting with `\`) don't require semicolons
* Always be careful with UPDATE and DELETE operations (use WHERE clauses)
* Constraints and conditions ensure data integrity and valid filtering

---

## Connection & Database Management

### Connecting to PostgreSQL

```sql
psql -U postgres
```

Logs in as the **postgres** user and opens the **psql** shell.

### Database Operations

| Command                          | Purpose             |
| -------------------------------- | ------------------- |
| `CREATE DATABASE database_name;` | Create new database |
| `\list` or `\l`                  | Show all databases  |
| `\c database_name`               | Switch database     |

Alternative SQL method:

```sql
SELECT datname FROM pg_database;
```

---

## Data Types in PostgreSQL

### Common Data Types

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

| Constraint    | Purpose                     |
| ------------- | --------------------------- |
| `PRIMARY KEY` | Unique row identifier       |
| `NOT NULL`    | Prevents empty values       |
| `UNIQUE`      | No duplicate values allowed |
| `DEFAULT`     | Sets default value          |
| `CHECK`       | Validates condition         |
| `FOREIGN KEY` | Refers to another table key |

---

## Create Table Example

Constraints can be applied directly inline on each column. You can also give a constraint a **custom name** using the `CONSTRAINT` keyword — this makes it easier to identify and drop later.

```sql
CREATE TABLE emp (
  emp_id SERIAL PRIMARY KEY NOT NULL,
  fname VARCHAR(100) NOT NULL,
  lname VARCHAR(100) NOT NULL,
  email VARCHAR(100) NOT NULL UNIQUE,
  dept VARCHAR(100),
  salary DECIMAL(8,2) NOT NULL DEFAULT 30000,
  hire_date DATE NOT NULL DEFAULT CURRENT_DATE,
  mob_no VARCHAR(15),
  -- Named constraint: easier to reference when dropping
  CONSTRAINT chk_mob_no CHECK (LENGTH(mob_no) = 10)
);
```

Without a name, PostgreSQL auto-generates one (e.g. `emp_mob_no_check`), which is hard to reference later. Naming it explicitly keeps things clean.

---

## CRUD Operations

### INSERT (Create)

```sql
INSERT INTO emp (fname, lname, email, dept, hire_date)
VALUES ('Fatima', 'Farooqui', 'test@gmail.com', 'H.O.E', '2025-10-10');
```

### SELECT (Read)

```sql
SELECT * FROM emp;
SELECT fname, lname FROM emp;
SELECT * FROM emp WHERE dept = 'IT';
```

### UPDATE (Modify)

```sql
UPDATE emp
SET salary = 15000
WHERE emp_id = 2;
```

⚠ Always use WHERE to avoid modifying all rows

### DELETE (Remove)

```sql
DELETE FROM emp
WHERE emp_id = 1;
```

⚠ Without WHERE → deletes all rows

---

## Operators & Filtering Clauses

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

```sql
SELECT * FROM emp WHERE salary BETWEEN 20000 AND 50000;
```

### IN

```sql
SELECT * FROM emp WHERE dept IN ('IT','Intern');
```

### IS NULL / IS NOT NULL

```sql
SELECT * FROM emp WHERE dept IS NULL;
SELECT * FROM emp WHERE dept IS NOT NULL;
```

---

## DISTINCT

```sql
SELECT DISTINCT dept FROM emp;
```

Removes duplicate values

---

## ORDER BY

```sql
SELECT * FROM emp ORDER BY fname DESC;
```

Sort ASC (default) or DESC

---

## LIMIT

```sql
SELECT * FROM emp LIMIT 3;
```

Fetch limited rows

---

## LIKE (Pattern Matching)

| Pattern | Meaning              |
| ------- | -------------------- |
| `J%`    | Starts with J        |
| `%a`    | Ends with a          |
| `%i%`   | Contains i           |
| `__`    | Exactly 2 characters |
| `_a%`   | Second char is a     |

Examples:

```sql
SELECT * FROM emp WHERE fname LIKE 'J%';
SELECT * FROM emp WHERE fname LIKE '%a';
SELECT * FROM emp WHERE fname LIKE '%i%';
SELECT * FROM emp WHERE dept LIKE '__';
SELECT * FROM emp WHERE fname LIKE '_a%';
```

---

## Aggregate Functions

Used to perform calculations on multiple rows and return a single value.

* `COUNT()`
* `SUM()`
* `AVG()`
* `MIN()`
* `MAX()`

Examples:

```sql
SELECT COUNT(emp_id) FROM emp;
SELECT SUM(salary) FROM emp;
SELECT AVG(salary) FROM emp;
SELECT MAX(salary) FROM emp;
```

Finding employee(s) with minimum salary:

```sql
SELECT fname, lname, salary FROM emp
WHERE salary = (SELECT MIN(salary) FROM emp);
```

---

## GROUP BY

Used to group rows that have the same values and apply aggregate functions.

```sql
SELECT dept, COUNT(fname) FROM emp
GROUP BY dept;
```

```sql
SELECT dept, SUM(salary) FROM emp
GROUP BY dept;
```

---

## String Functions

### Common String Functions

* `CONCAT()`, `CONCAT_WS()`
* `SUBSTR()`
* `LEFT()`, `RIGHT()`
* `LENGTH()`
* `UPPER()`, `LOWER()`
* `TRIM()`, `LTRIM()`, `RTRIM()`
* `REPLACE()`
* `POSITION()`
* `STRING_AGG()`

Examples:

```sql
SELECT CONCAT(fname, lname) AS fullname FROM emp;
```

```sql
SELECT CONCAT_WS(' ', fname, lname) AS fullname FROM emp;
```

```sql
SELECT SUBSTR(fname, 1, 3) FROM emp;
```

```sql
SELECT REPLACE(dept, 'IT', 'Software') FROM emp;
```

Reverse string (rarely used):

```sql
SELECT REVERSE(fname) FROM emp;
```

```sql
SELECT LENGTH(fname) FROM emp;
```

```sql
SELECT UPPER(fname) FROM emp;
SELECT LOWER(fname) FROM emp;
```

Task-based examples:

```sql
SELECT CONCAT_WS(':', emp_id, fname, lname, dept) AS task1 FROM emp;
```

```sql
SELECT CONCAT_WS(':', emp_id, fname, UPPER(dept)) AS task2 FROM emp;
```

---

## ALTER TABLE

Used to modify the structure of an existing table — add/remove columns, rename columns or tables, and change column properties or constraints.

### Adding a Column

Adds a new column to an existing table. A `DEFAULT` value can be provided so existing rows are not left empty.

```sql
ALTER TABLE emp
ADD COLUMN status VARCHAR(100) DEFAULT 'ACTIVE';
```

### Removing a Column

Permanently deletes a column and all its data from the table.

```sql
ALTER TABLE emp
DROP COLUMN status;
```

⚠ This action is irreversible — the column and its data are gone

### Renaming a Column

Renames an existing column without affecting its data or data type.

```sql
ALTER TABLE emp
RENAME COLUMN dept TO department;
```

### Renaming a Table

Changes the name of an entire table.

```sql
ALTER TABLE emp
RENAME TO employee;
```

### Changing a Column's Data Type

Modifies the data type or size of an existing column using `SET DATA TYPE`.

```sql
ALTER TABLE employee
ALTER COLUMN department
SET DATA TYPE VARCHAR(110);
```

### Adding a Constraint

Adds a constraint to an existing column. Always give constraints a custom name using `CONSTRAINT constraint_name` — this makes them easy to identify and drop later.

```sql
-- Add a CHECK constraint with a custom name
ALTER TABLE employee
ADD CONSTRAINT chk_mob_no CHECK (LENGTH(mob_no) = 10);
```

Without a custom name, PostgreSQL auto-generates one (e.g. `employee_mob_no_check`), which is hard to reference when dropping.

If a value violates the constraint, the insert/update is rejected:

```sql
-- This will FAIL because mob_no has 11 digits
INSERT INTO employee (mob_no, fname, lname, email)
VALUES (12345678932, 'x', 'y', 'x@wy.com');
```

### Dropping a Constraint

Removes a named constraint from the table.

```sql
ALTER TABLE employee
DROP CONSTRAINT mob_no;
```

---

## CASE Expression

The `CASE` expression works like an if-else statement inside a query. It evaluates conditions in order and returns a value for the first condition that is true. The result can be aliased as a new column.

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

The same result can also be written using `BETWEEN` for cleaner range conditions:

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

CASE can also be used for calculations. If no `ELSE` is provided and no condition matches, the result is `NULL`.

```sql
SELECT fname, salary,
  CASE
    WHEN salary > 0 THEN ROUND(salary * 0.10)
  END AS bonus
FROM employee;
```

**Example — GROUP BY with CASE:**

You can group rows by the result of a CASE expression to get counts or aggregates per category.

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

## Relationships

A **relationship** links two tables together using a `FOREIGN KEY`. The foreign key in one table references the `PRIMARY KEY` of another, ensuring only valid, existing values can be inserted — this is called **referential integrity**.

There are three main types of relationships in relational databases:

| Type           | Description                                      | Example                          |
| -------------- | ------------------------------------------------ | -------------------------------- |
| One-to-One     | One row in A links to exactly one row in B       | User ↔ User Profile              |
| **One-to-Many**    | One row in A links to many rows in B             | Customer → Orders                |
| **Many-to-Many**   | Many rows in A link to many rows in B            | Students ↔ Courses               |

> 💡 **Industry note:** One-to-Many is by far the most commonly used relationship in real-world applications (e.g. users & posts, customers & orders, employees & departments). Many-to-Many is also widely used wherever there is a subscription, tagging, or enrollment system. One-to-One is the least common and is usually used to split a large table for performance or security reasons.

---

### One-to-Many Relationship

One record in the parent table can have **many** related records in the child table, but each child record belongs to only **one** parent. The foreign key always lives on the "many" side.

**Example:** One customer can place many orders, but each order belongs to one customer.

```sql
-- Parent table (the "one" side)
CREATE TABLE customers (
  id SERIAL PRIMARY KEY NOT NULL,
  name TEXT NOT NULL,
  email TEXT UNIQUE,
  phone TEXT UNIQUE NOT NULL
);

-- Child table (the "many" side) — each order belongs to one customer
CREATE TABLE orders (
  id SERIAL PRIMARY KEY NOT NULL,
  date DATE NOT NULL DEFAULT CURRENT_DATE,
  price DECIMAL(8,2) NOT NULL,
  cust_id INTEGER NOT NULL,
  FOREIGN KEY (cust_id) REFERENCES customers (id)
);
```

### Inserting Related Data

The parent record must exist before inserting into the child table.

```sql
INSERT INTO customers (name, email, phone)
VALUES
  ('Karan Verma', 'karan.verma@email.com', '9876501234'),
  ('Priya Nair',  'priya.nair@email.com',  '9765412300'),
  ('Manish Gupta', NULL,                   '9898989898');

INSERT INTO orders (price, cust_id)
VALUES (349.75, 3);
```

⚠ Inserting an order with a `cust_id` that doesn't exist in `customers` will be rejected

---

### Many-to-Many Relationship

A **many-to-many** relationship exists when one record in table A can relate to many records in table B, and vice versa. This cannot be represented with a single foreign key — it requires a **junction table** (also called a bridge or linking table) that sits between the two and holds both foreign keys.

**Example:** A student can enroll in many courses, and a course can have many students.

```sql
CREATE TABLE students (
  id SERIAL PRIMARY KEY NOT NULL,
  name TEXT NOT NULL
);

CREATE TABLE course (
  id SERIAL PRIMARY KEY NOT NULL,
  name TEXT NOT NULL,
  fees DECIMAL(8,2) NOT NULL
);

-- Junction table — links students and courses (the "many-to-many" bridge)
CREATE TABLE enrollment (
  id SERIAL PRIMARY KEY NOT NULL,
  date DATE NOT NULL DEFAULT CURRENT_DATE,
  student_id INTEGER NOT NULL,
  course_id INTEGER NOT NULL,
  FOREIGN KEY (student_id) REFERENCES students(id),
  FOREIGN KEY (course_id) REFERENCES course(id)
);
```

Always insert into the parent tables first, then the junction table:

```sql
-- 1. Insert students
INSERT INTO students (name) VALUES
  ('John Smith'), ('Emma Johnson'), ('Michael Brown'),
  ('Sarah Wilson'), ('David Lee'), ('Lisa Anderson'),
  ('James Taylor'), ('Maria Garcia');

-- 2. Insert courses
INSERT INTO course (name, fees) VALUES
  ('Mathematics 101', 499.99),
  ('Physics 101', 549.99),
  ('Computer Science 101', 599.99),
  ('English Literature', 449.99),
  ('History 101', 399.99),
  ('Chemistry 101', 529.99),
  ('Biology 101', 519.99),
  ('Economics 101', 479.99);

-- 3. Insert enrollments (both students and courses must exist first)
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

Joins combine rows from two or more tables based on a related column.

### CROSS JOIN

Returns every possible combination of rows from both tables (cartesian product). Rarely useful in practice.

```sql
SELECT * FROM customers
CROSS JOIN orders;
```

### INNER JOIN

Returns only the rows where there is a match in **both** tables. Non-matching rows are excluded.

```sql
SELECT * FROM customers c
INNER JOIN orders d
ON c.id = d.cust_id;
```

**INNER JOIN with GROUP BY:**

Combine with aggregate functions to summarize data per customer.

```sql
-- Total number of orders per customer
SELECT c.id, c.name, COUNT(d.id) AS no_of_orders
FROM customers c
INNER JOIN orders d
ON c.id = d.cust_id
GROUP BY c.id, c.name;

-- Total amount spent per customer
SELECT c.id, c.name, SUM(d.price) AS total_spent
FROM customers c
INNER JOIN orders d
ON c.id = d.cust_id
GROUP BY c.id, c.name;
```

### LEFT JOIN

Returns **all rows from the left table** and matching rows from the right. If there is no match, right side columns show `NULL`.

```sql
SELECT * FROM customers c
LEFT JOIN orders d
ON c.id = d.cust_id;
```

> Use this when you want all customers — even those who haven't placed any orders.

### RIGHT JOIN

Returns **all rows from the right table** and matching rows from the left. If there is no match, left side columns show `NULL`.

```sql
SELECT * FROM customers c
RIGHT JOIN orders d
ON c.id = d.cust_id;
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

When working with a many-to-many relationship, you join through the junction table by chaining multiple `JOIN` clauses. Each `JOIN` connects one additional table.

```sql
-- Get full enrollment details: student name, course name, date and fee
SELECT s.name AS student_name, c.name AS course_name,
       e.date AS enroll_date, c.fees AS course_fee
FROM enrollment e
JOIN students s ON e.student_id = s.id
JOIN course c   ON c.id = e.course_id;
```

**Example — Count of courses per student:**

```sql
SELECT s.name AS student_name, COUNT(c.id) AS no_of_courses
FROM enrollment e
JOIN students s ON e.student_id = s.id
JOIN course c   ON c.id = e.course_id
GROUP BY s.name;
```

**Example — Total courses and total fees per student:**

```sql
SELECT s.name AS student_name,
       COUNT(c.id)   AS no_of_courses,
       SUM(c.fees)   AS total_spend
FROM enrollment e
JOIN students s ON e.student_id = s.id
JOIN course c   ON c.id = e.course_id
GROUP BY s.name;
```

### Subquery with ORDER BY and LIMIT

A **subquery** (inner query) runs first and its result is treated as a temporary table by the outer query. This is useful when you want to sort or filter on an aggregated value.

```sql
-- Find the student who spent the most on courses
SELECT *
FROM (
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

### EXTRACT

`EXTRACT` pulls out a specific part of a date (year, month, day, etc.) and can be used in `SELECT` or `GROUP BY` to group data by time periods.

**Syntax:**

```sql
EXTRACT(part FROM date_column)
```

Common parts: `YEAR`, `MONTH`, `DAY`, `HOUR`, `MINUTE`

**Example — Enrollments and revenue grouped by year:**

```sql
SELECT COUNT(c.id)                      AS no_of_courses,
       SUM(c.fees)                      AS total_spend,
       EXTRACT(YEAR FROM e.date)        AS year
FROM enrollment e
JOIN students s ON e.student_id = s.id
JOIN course c   ON c.id = e.course_id
GROUP BY year;
```

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

* CRUD = Create, Read, Update, Delete
* Aggregate functions summarize data
* GROUP BY works with aggregate functions
* String functions help format and clean text
* WHERE + operators for filtering
* DISTINCT removes duplicates
* ORDER BY sorts data
* LIMIT restricts rows
* ALTER TABLE modifies table structure (columns, names, types, constraints)
* CASE expression adds conditional logic directly inside queries
* FOREIGN KEY links tables and enforces referential integrity
* Many-to-many relationships use a junction table with two foreign keys
* Joins combine data from multiple tables based on related columns
* Subqueries wrap a query as a temporary table for further filtering or sorting
* EXTRACT pulls out a specific part (year, month, day) from a date column
