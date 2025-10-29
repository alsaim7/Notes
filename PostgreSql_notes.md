# Basic PostgreSQL Commands

## Summary
This note covers fundamental PostgreSQL (psql) commands for connecting to the server, managing databases, and basic navigation. It includes CLI interactions for creating, listing, and switching databases.

## Key Points
- **Connect** to PostgreSQL using `psql -U postgres`
- **Create** a new database with `CREATE DATABASE database_name;`
- **List** all databases using `SELECT datname FROM pg_database;` or `\list`
- **Clear** the screen with `\! cls`
- **Switch** to a database via `\c database_name`

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

## Key Takeaways
- **psql** is the primary CLI tool for PostgreSQL interactions.
- Meta-commands start with `\` (e.g., `\list`, `\c`).
- SQL commands end with `;` (e.g., `CREATE DATABASE`).
- Combine system commands with `\!` for shell access.

## References
- [PostgreSQL Documentation](https://www.postgresql.org/docs/current/app-psql.html)
