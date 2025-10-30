# Basic PostgreSQL Commands

## Summary
Essential PostgreSQL commands for database operations including connection, CRUD operations (Create, Read, Update, Delete), and utility commands.

## Key Points
- **psql** is PostgreSQL's interactive terminal
- SQL commands end with semicolons `;`
- Meta-commands (starting with `\`) don't require semicolons
- Always be careful with UPDATE and DELETE operations (use WHERE clauses)

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

## Data Manipulation (CRUD Operations)

### Create - Inserting Data
```sql
INSERT INTO example_table (name, city, id)
VALUES 
('Levi Ackerman', 'Japan', 2),
('Tony Stark', 'USA', 3);
```
*Inserts multiple rows into the table.*

### Read - Selecting Data
```sql
-- Select all columns
SELECT * FROM example_table;

-- Select specific columns
SELECT name, city FROM example_table;

-- Select with conditions
SELECT * FROM example_table WHERE id = 2;
```
*Retrieves data from the table.*

### Update - Modifying Data
```sql
UPDATE example_table
SET city = 'Wall Maria'
WHERE id = 2;
```
**⚠️ Important:** Always use WHERE clause to avoid updating all rows.

### Delete - Removing Data
```sql
DELETE FROM example_table
WHERE id = 2;
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
