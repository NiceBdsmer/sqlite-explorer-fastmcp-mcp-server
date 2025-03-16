# Cursor Agent Guide to SQLite and Playwright MCPs

This guide explains how to use both the SQLite Explorer and Playwright MCPs for Cursor AI agents.

## Configuration Reference

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
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@executeautomation/playwright-mcp-server"],
      "env": {
        "PLAYWRIGHT_CHROMIUM_PATH": "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome",
        "PLAYWRIGHT_SKIP_BROWSER_DOWNLOAD": "1"
      }
    }
  }
}
```

## SQLite Explorer MCP

### Available Tools

1. `read_query`
   - Purpose: Execute SELECT queries
   - Parameters:
     - query: SQL query string
     - params: Optional list of parameters
     - fetch_all: Boolean (default: true)
     - row_limit: Integer (default: 1000)
   - Example:
     ```python
     result = read_query("SELECT * FROM users WHERE id = ?", params=[1])
     ```

2. `list_tables`
   - Purpose: Get all table names
   - No parameters required
   - Example:
     ```python
     tables = list_tables()
     ```

3. `describe_table`
   - Purpose: Get table schema
   - Parameters:
     - table_name: String
   - Example:
     ```python
     schema = describe_table("users")
     ```

### Best Practices for Agents

1. Always validate SQL queries before execution
2. Use parameterized queries for user input
3. Handle potential errors:
   - Table not found
   - Invalid SQL syntax
   - Connection issues
4. Respect row limits for large queries
5. Check schema before operations

## Playwright MCP (Browser Automation)

### Available Tools

1. `navigate`
   - Purpose: Load a webpage
   - Parameters:
     - url: String
     - waitUntil: String (optional: "load", "domcontentloaded", "networkidle")
   - Example:
     ```python
     await navigate("https://example.com", waitUntil="networkidle")
     ```

2. `screenshot`
   - Purpose: Capture page or element
   - Parameters:
     - selector: String (optional)
     - path: String (optional)
     - fullPage: Boolean (optional)
   - Example:
     ```python
     await screenshot(selector="#main-content")
     ```

3. `evaluate`
   - Purpose: Execute JavaScript
   - Parameters:
     - script: String
     - args: List (optional)
   - Example:
     ```python
     result = await evaluate("document.title")
     ```

### Best Practices for Agents

1. Error Handling:
   ```python
   try:
       await navigate(url)
   except PlaywrightError as e:
       handle_error(e)
   ```

2. Waiting for Elements:
   ```python
   await waitForSelector("#element", state="visible")
   ```

3. Form Interactions:
   ```python
   await type("#search", "query")
   await click("#submit")
   ```

4. Resource Management:
   - Close pages when done
   - Handle multiple tabs properly
   - Clean up screenshots

## Integration Patterns

### Combining Both MCPs

1. Web Scraping to Database:
   ```python
   # Navigate and extract data
   await navigate(url)
   data = await evaluate("script to extract data")
   
   # Store in database
   read_query("INSERT INTO scraped_data VALUES (?)", [data])
   ```

2. Database-Driven Automation:
   ```python
   # Get URLs from database
   urls = read_query("SELECT url FROM sites")
   
   # Process each URL
   for url in urls:
       await navigate(url["url"])
       # Perform actions
   ```

### Error Handling Strategy

1. Database Errors:
   ```python
   try:
       result = read_query(query)
   except SQLiteError as e:
       log_error(e)
       suggest_fix(e)
   ```

2. Browser Errors:
   ```python
   try:
       await navigate(url)
   except TimeoutError:
       retry_with_longer_timeout()
   except NetworkError:
       check_connectivity()
   ```

## Performance Optimization

1. Database:
   - Use indexes appropriately
   - Limit result sets
   - Optimize queries

2. Browser:
   - Minimize page loads
   - Reuse browser contexts
   - Handle resources efficiently

## Security Considerations

1. SQL Injection Prevention:
   - Always use parameterized queries
   - Validate table names
   - Escape special characters

2. Browser Security:
   - Handle cookies securely
   - Respect robots.txt
   - Validate URLs before navigation

## Debugging Tips

1. Database Issues:
   - Check connection string
   - Verify table existence
   - Review query syntax

2. Browser Issues:
   - Check selector validity
   - Verify page load status
   - Monitor network conditions 