# Personal Guide to Using SQLite and Playwright MCPs

This guide explains how to use both the SQLite Explorer and Playwright MCPs for personal use.

## SQLite Explorer MCP

### Setup and Configuration
1. Navigate to the project directory:
   ```bash
   cd sqlite-explorer-fastmcp-mcp-server
   ```

2. Activate the virtual environment:
   ```bash
   source venv/bin/activate
   ```

3. Start the SQLite MCP server:
   ```bash
   SQLITE_DB_PATH=/Users/sovereignac/Desktop/testfast/sqlite-explorer-fastmcp-mcp-server/test.db uvicorn sqlite_explorer:mcp --reload
   ```

### Testing the SQLite MCP
1. List all tables:
   ```bash
   curl http://localhost:8000/schema/tables
   ```

2. Describe a specific table:
   ```bash
   curl http://localhost:8000/schema/table/users
   ```

3. Execute a query:
   ```bash
   curl http://localhost:8000/query -d "SELECT * FROM users"
   ```

### Common Operations
- View table structure:
  ```bash
  curl http://localhost:8000/schema/table/[TABLE_NAME]
  ```
- Count rows:
  ```bash
  curl http://localhost:8000/query -d "SELECT COUNT(*) FROM [TABLE_NAME]"
  ```
- Get sample data:
  ```bash
  curl http://localhost:8000/query -d "SELECT * FROM [TABLE_NAME] LIMIT 5"
  ```

## Playwright MCP (Browser Automation)

### Setup and Configuration
1. Start the Playwright MCP server:
   ```bash
   npx -y @executeautomation/playwright-mcp-server
   ```

### Testing Browser Automation
1. Navigate to a webpage:
   ```bash
   curl http://localhost:8000/navigate -d '{"url": "https://example.com"}'
   ```

2. Take a screenshot:
   ```bash
   curl http://localhost:8000/screenshot -d '{"selector": "body"}'
   ```

3. Get page content:
   ```bash
   curl http://localhost:8000/content
   ```

### Common Operations
- Click an element:
  ```bash
  curl http://localhost:8000/click -d '{"selector": "#submit-button"}'
  ```
- Fill a form:
  ```bash
  curl http://localhost:8000/type -d '{"selector": "#search", "text": "query"}'
  ```
- Execute JavaScript:
  ```bash
  curl http://localhost:8000/evaluate -d '{"script": "document.title"}'
  ```

## Troubleshooting

### SQLite MCP
- If the server won't start, check:
  1. Virtual environment is activated
  2. Database path is correct
  3. Port 8000 is available

### Playwright MCP
- If Chrome doesn't launch:
  1. Verify Chrome path in config
  2. Check Chrome version compatibility
  3. Ensure no other instances are running

## Tips and Best Practices
1. Always check server responses for error messages
2. Use appropriate timeouts for browser operations
3. Close browser sessions when done
4. Keep database queries optimized
5. Use proper error handling in scripts 