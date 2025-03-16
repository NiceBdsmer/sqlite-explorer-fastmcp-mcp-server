# SQLite MCP Guide

A comprehensive guide for using the SQLite MCP server for both personal use and Cursor AI agents.

## Quick Start

### Personal Use

1. Navigate and activate environment:
   ```bash
   cd sqlite-explorer-fastmcp-mcp-server
   source venv/bin/activate
   ```

2. Start the server:
   ```bash
   SQLITE_DB_PATH=/Users/sovereignac/Desktop/testfast/sqlite-explorer-fastmcp-mcp-server/test.db uvicorn sqlite_explorer:mcp --reload
   ```

3. Quick test:
   ```bash
   curl http://localhost:8000/schema/tables
   ```

### For Cursor AI Agents

```json
{
  "mcpServers": {
    "sqlite_explorer": {
      "command": "/opt/homebrew/Cellar/python@3.13/3.13.1/Frameworks/Python.framework/Versions/3.13/Resources/Python.app/Contents/MacOS/Python",
      "args": [
        "sqlite_explorer.py"
      ],
      "env": {
        "SQLITE_DB_PATH": "/Users/sovereignac/Desktop/testfast/sqlite-explorer-fastmcp-mcp-server/test.db",
        "PATH": "/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin"
      }
    }
  }
}
```

## API Reference

### 1. List Tables
- **Purpose**: Get all available tables in the database
- **Endpoint**: `/schema/tables`
- **Method**: GET
- **Usage**:
  ```bash
  # For users
  curl http://localhost:8000/schema/tables

  # For agents
  tables = list_tables()
  ```

### 2. Describe Table
- **Purpose**: Get detailed table schema
- **Endpoint**: `/schema/table/{table_name}`
- **Method**: GET
- **Usage**:
  ```bash
  # For users
  curl http://localhost:8000/schema/table/users

  # For agents
  schema = describe_table("users")
  ```
- **Response Example**:
  ```json
  [
    {
      "name": "id",
      "type": "INTEGER",
      "notnull": 1,
      "pk": 1
    },
    {
      "name": "name",
      "type": "TEXT",
      "notnull": 1,
      "pk": 0
    }
  ]
  ```

### 3. Execute Query
- **Purpose**: Run SQL queries
- **Endpoint**: `/query`
- **Method**: POST
- **Parameters**:
  - query: SQL query string
  - params: (optional) Query parameters
  - fetch_all: (optional) Boolean
  - row_limit: (optional) Integer
- **Usage**:
  ```bash
  # For users
  curl http://localhost:8000/query -d "SELECT * FROM users LIMIT 5"

  # For agents
  result = read_query("SELECT * FROM users WHERE id = ?", params=[1])
  ```

## Common Operations

### Basic Queries
```bash
# Count rows
curl http://localhost:8000/query -d "SELECT COUNT(*) FROM users"

# Get recent records
curl http://localhost:8000/query -d "SELECT * FROM users ORDER BY id DESC LIMIT 5"

# Search records
curl http://localhost:8000/query -d "SELECT * FROM users WHERE name LIKE '%John%'"
```

### Schema Operations
```bash
# List all tables
curl http://localhost:8000/schema/tables

# Get table info
curl http://localhost:8000/schema/table/users

# Get column names
curl http://localhost:8000/query -d "PRAGMA table_info(users)"
```

## Best Practices

### For Personal Use
1. Always use parameterized queries for variables
2. Keep queries simple and readable
3. Use LIMIT when fetching large datasets
4. Test queries with small datasets first
5. Keep the server running in a dedicated terminal

### For Cursor AI Agents
1. Validate input before querying:
   ```python
   def safe_query(table_name, conditions):
       if not re.match(r'^[a-zA-Z0-9_]+$', table_name):
           raise ValueError("Invalid table name")
       return read_query(f"SELECT * FROM {table_name} WHERE {conditions}")
   ```

2. Handle errors gracefully:
   ```python
   try:
       result = read_query(query)
   except Exception as e:
       log_error(e)
       suggest_alternative_query()
   ```

3. Use schema validation:
   ```python
   def validate_table_exists(table_name):
       tables = list_tables()
       if table_name not in tables:
           raise ValueError(f"Table {table_name} does not exist")
   ```

## Troubleshooting

### Common Issues

1. Server Won't Start
   - Check virtual environment is activated
   - Verify database path exists
   - Ensure port 8000 is available
   - Check Python version (3.8+ required)

2. Query Errors
   - Verify table exists
   - Check column names
   - Validate SQL syntax
   - Ensure proper parameter formatting

3. Connection Issues
   - Check server is running
   - Verify port is correct
   - Check for firewall issues
   - Ensure database file is accessible

### Debug Commands
```bash
# Check server status
curl http://localhost:8000/health

# Verify database connection
curl http://localhost:8000/query -d "SELECT 1"

# Test table access
curl http://localhost:8000/schema/tables
```

## Security Considerations

1. Input Validation
   - Always validate table names
   - Use parameterized queries
   - Sanitize user input
   - Validate query types

2. Access Control
   - Limit network access
   - Use read-only queries when possible
   - Monitor query patterns
   - Log suspicious activities

3. Data Protection
   - Backup database regularly
   - Monitor file permissions
   - Limit exposed data
   - Use secure connections

## Performance Tips

1. Query Optimization
   - Use appropriate indexes
   - Keep queries simple
   - Limit result sets
   - Use COUNT(*) sparingly

2. Connection Management
   - Reuse connections when possible
   - Close unused connections
   - Monitor connection pool
   - Handle timeouts properly 