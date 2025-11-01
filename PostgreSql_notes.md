# Basic PostgreSQL Commands

## Summary
Essential PostgreSQL commands for database operations including connection, CRUD operations, data types, constraints, and utility commands.

## Key Points
- **psql** is PostgreSQL's interactive terminal
- SQL commands end with semicolons `;`
- Meta-commands (starting with `\`) don't require semicolons
- Always be careful with UPDATE and DELETE operations (use WHERE clauses)
- Constraints ensure data integrity and validity

## Connection & Database Management

### Connecting to PostgreSQL
```sql
psql -U postgres
```
Logs in as the **postgres** user and starts the interactive **psql** shell.

### Database Operations
| Command | Purpose | Example |
|---------|---------|---------|
| Create Database | Create new database | `CREATE DATABASE database_name;` |
| List Databases | Show all databases | `\list` or `\l` |
| Switch Database | Connect to specific DB | `\c database_name` |

**Alternative way to list databases:**
```sql
SELECT datname FROM pg_database;
```

## Data Types in PostgreSQL

### Common Data Types
| Type | Description | Example |
|------|-------------|---------|
| `SERIAL` | Auto-incrementing integer | `emp_id SERIAL` |
| `VARCHAR(n)` | Variable-length string (max n chars) | `VARCHAR(100)` |
| `DECIMAL(p,s)` | Exact numeric (precision, scale) | `DECIMAL(8,2)` |
| `DATE` | Date without time | `'2025-10-10'` |
| `INTEGER` | Whole numbers | `INTEGER` |
| `BOOLEAN` | True/False values | `BOOLEAN` |
| `TEXT` | Unlimited length text | `TEXT` |

## Table Constraints

### Common Constraints
| Constraint | Purpose | Example |
|------------|---------|---------|
| `PRIMARY KEY` | Unique identifier for each row | `emp_id SERIAL PRIMARY KEY` |
| `NOT NULL` | Column cannot be empty | `fname VARCHAR(100) NOT NULL` |
| `UNIQUE` | All values in column must be unique | `email VARCHAR(100) UNIQUE` |
| `DEFAULT` | Default value when not specified | `salary DECIMAL(8,2) DEFAULT 30000` |
| `CHECK` | Custom validation condition | `CHECK (salary > 0)` |
| `FOREIGN KEY` | References primary key in another table | `dept_id INTEGER REFERENCES departments(id)` |

## Table Creation with Example

### Creating Employee Table
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

**Explanation of each column:**
- `emp_id`: Auto-incrementing primary key
- `fname` & `lname`: Required text fields (cannot be NULL)
- `email`: Required and must be unique across all records
- `dept`: Optional department field (can be NULL)
- `salary`: Decimal number with default value of 30,000
- `hire_date`: Date with default as today's date

## Data Manipulation (CRUD Operations)

### Create - Inserting Data
```sql
-- Insert with specific columns (auto-generates emp_id and hire_date)
INSERT INTO emp (fname, lname, email, dept, hire_date)
VALUES ('Fatima', 'Farooqui', 'farooquifatima10@gmail.com', 'H.O.E', '2025-10-10');

-- Insert multiple rows with auto-generated hire_date
INSERT INTO emp (fname, lname, email, dept, salary)
VALUES 
('Faraz', 'Khan', 'faraz.khan@example.com', 'Intern', 12000),
('John', 'Doe', 'john.doe@example.com', 'IT', 50000);
```

### Read - Selecting Data
```sql
-- Select all columns
SELECT * FROM emp;

-- Select specific columns
SELECT fname, lname, email FROM emp;

-- Select with conditions
SELECT * FROM emp WHERE dept = 'Intern';
```

### Update - Modifying Data
```sql
UPDATE emp
SET salary = 15000
WHERE emp_id = 2;
```
**⚠️ Important:** Always use WHERE clause to avoid updating all rows.

### Delete - Removing Data
```sql
DELETE FROM emp
WHERE emp_id = 1;
```
**⚠️ Warning:** Without WHERE clause, this deletes ALL rows in the table.

## Utility Commands
| Command | Purpose |
|---------|---------|
| `\! cls` | Clear terminal (Windows) |
| `\! clear` | Clear terminal (Linux/Mac) |
| `\q` | Quit psql |
| `\?` | Show help for meta-commands |
| `\h` | Show help for SQL commands |
| `\d table_name` | Describe table structure |
| `\dt` | List all tables |

## Key Takeaways
- **CRUD Operations:** Create (INSERT), Read (SELECT), Update (UPDATE), Delete (DELETE)
- **Data Types:** Choose appropriate types (SERIAL, VARCHAR, DECIMAL, DATE) for your data
- **Constraints:** Use PRIMARY KEY, NOT NULL, UNIQUE, DEFAULT to enforce data integrity
- **Safety First:** Always double-check WHERE clauses in UPDATE/DELETE statements
- **Auto-generation:** SERIAL and DEFAULT values reduce manual data entry
- **Two Command Types:** SQL commands (end with `;`) and Meta-commands (start with `\`)

---

The notes now comprehensively cover data types, constraints, and include your practical table creation example with detailed explanations!
