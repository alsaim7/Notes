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
