# Basic PostgreSQL Commands

## Detailed Notes

### Connecting to psql
- Use the command `psql -U postgres` to log in as the **postgres** user.
- This starts the interactive **psql** shell.

### Creating Databases
- Inside psql, run `CREATE DATABASE database_name;` to create a new database.
- Replace `database_name` with your desired name.

### Listing Databases
- Query system catalog: `SELECT datname FROM pg_database;`
- Or use meta-command: `\list` (or `\l`) to display all databases.

### Utility Commands
- Clear the terminal: `\! cls`
- This executes the system command **cls** from within psql.

### Switching Databases
- Use `\c database_name` to connect to a specific database.
- Shortcut for `CONNECT TO database_name`.

