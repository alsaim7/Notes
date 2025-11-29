# Basic PostgreSQL Commands

## Summary

Essential PostgreSQL commands for database operations including connection, CRUD operations, data types, constraints, operators, clauses, and utility commands.

## Key Points

* **psql** is PostgreSQL's interactive terminal
* SQL commands end with semicolons `;`
* Meta-commands (starting with `\`) don't require semicolons
* Always be careful with UPDATE and DELETE operations (use WHERE clauses)
* Constraints and conditions ensure data integrity and valid filtering

## Connection & Database Management

### Connecting to PostgreSQL

```sql
psql -U postgres
```

Logs in as the **postgres** user and opens the **psql** shell.

### Database Operations

| Command                          | Purpose                     |
| -------------------------------- | --------------------------- |
| `CREATE DATABASE database_name;` | Create new database         |
| `\list` or `\l`                  | Show all databases          |
| `\c database_name`               | Switch to specific database |

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

```sql
CREATE TABLE emp (
  emp_id SERIAL PRIMARY KEY NOT NULL,
  fname VARCHAR(100) NOT NULL,
  lname VARCHAR(100) NOT NULL,
  email VARCHAR(100) NOT NULL UNIQUE,
  dept VARCHAR(100),
  salary DECIMAL(8,2) NOT NULL DEFAULT 30000,
  hire_date DATE NOT NULL DEFAULT CURRENT_DATE
);
```

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

# Operators & Filtering Clauses

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

Sort ASC(default) or DESC

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

## Utility Commands

| Command         | Purpose                    |
| --------------- | -------------------------- |
| `\! cls`        | Clear terminal (Windows)   |
| `\! clear`      | Clear terminal (Linux/Mac) |
| `\q`            | Quit psql                  |
| `\?`            | Meta-commands help         |
| `\h`            | SQL command help           |
| `\d table_name` | Describe table             |
| `\dt`           | List tables                |

---

## Key Takeaways

* CRUD = Create, Read, Update, Delete
* WHERE + operators for filtering
* DISTINCT removes duplicates
* ORDER BY sorts data
* LIMIT restricts rows
* LIKE searches text patterns
