# How to Create an MCP Client

## Overview
An MCP (Model Context Protocol) client communicates with MCP servers using JSON-RPC over stdio (standard input/output). Here's how to create one:

## Method 1: Using the Official MCP Python SDK

### Installation
```bash
pip install mcp
```

### Basic Client Example
```python
import asyncio
import os
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def test_google_sheets():
    # Set environment variables
    os.environ["GOOGLE_PROJECT_ID"] = "kiro-sheets-agent"
    os.environ["GOOGLE_APPLICATION_CREDENTIALS"] = "../kiro-sheets-agent-34e2628de8e6.json"
    
    # Configure server
    server_params = StdioServerParameters(
        command="uvx",
        args=["mcp-google-sheets@latest"],
        env=dict(os.environ)
    )
    
    async with stdio_client(server_params) as (read, write):
        async with ClientSession(read, write) as session:
            # Initialize
            await session.initialize()
            
            # List tools
            tools = await session.list_tools()
            print(f"Available tools: {[t.name for t in tools.tools]}")
            
            # Call a tool
            result = await session.call_tool(
                "read_sheet",
                arguments={
                    "spreadsheet_id": "1pQkjKOzOpVtQ4QIAYeb26IKcRIAFwQez",
                    "range": "A1:Z10"
                }
            )
            print(f"Result: {result.content}")

# Run the client
asyncio.run(test_google_sheets())
```

## Method 2: Manual JSON-RPC Implementation

### Basic JSON-RPC Messages

1. **Initialize the server:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "initialize",
  "params": {
    "protocolVersion": "2024-11-05",
    "capabilities": {},
    "clientInfo": {
      "name": "test-client",
      "version": "1.0.0"
    }
  }
}
```

2. **List available tools:**
```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "tools/list"
}
```

3. **Call a tool:**
```json
{
  "jsonrpc": "2.0",
  "id": 3,
  "method": "tools/call",
  "params": {
    "name": "read_sheet",
    "arguments": {
      "spreadsheet_id": "1pQkjKOzOpVtQ4QIAYeb26IKcRIAFwQez",
      "range": "A1:Z10"
    }
  }
}
```

## Method 3: Using Existing MCP Clients

### Claude Desktop
Claude Desktop has built-in MCP support. Add to your config:

**Windows:** `%APPDATA%\Claude\claude_desktop_config.json`
**macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "google-sheets": {
      "command": "uvx",
      "args": ["mcp-google-sheets@latest"],
      "env": {
        "GOOGLE_PROJECT_ID": "kiro-sheets-agent",
        "GOOGLE_APPLICATION_CREDENTIALS": "../kiro-sheets-agent-34e2628de8e6.json"
      }
    }
  }
}
```

### Kiro AI
Kiro AI uses the `mcp_config.json` file in your project root (which you already have configured).

## Testing Your Setup

### Quick Test Script
```python
#!/usr/bin/env python3
import subprocess
import json
import os

def test_mcp_server():
    env = os.environ.copy()
    env.update({
        "GOOGLE_PROJECT_ID": "kiro-sheets-agent",
        "GOOGLE_APPLICATION_CREDENTIALS": "../kiro-sheets-agent-34e2628de8e6.json"
    })
    
    # Start server
    proc = subprocess.Popen(
        ["uvx", "mcp-google-sheets@latest"],
        stdin=subprocess.PIPE,
        stdout=subprocess.PIPE,
        stderr=subprocess.PIPE,
        text=True,
        env=env
    )
    
    # Send initialize
    init_msg = {
        "jsonrpc": "2.0",
        "id": 1,
        "method": "initialize",
        "params": {
            "protocolVersion": "2024-11-05",
            "capabilities": {},
            "clientInfo": {"name": "test", "version": "1.0"}
        }
    }
    
    proc.stdin.write(json.dumps(init_msg) + "\n")
    proc.stdin.flush()
    
    # Read response
    response = proc.stdout.readline()
    print(f"Server response: {response}")
    
    proc.terminate()

if __name__ == "__main__":
    test_mcp_server()
```

## Common Tools in Google Sheets MCP Server

Based on typical MCP Google Sheets implementations, you'll likely have these tools:

- `read_sheet` - Read data from a spreadsheet
- `write_sheet` - Write data to a spreadsheet  
- `create_sheet` - Create a new spreadsheet
- `list_sheets` - List sheets in a spreadsheet
- `get_sheet_info` - Get metadata about a spreadsheet

## Next Steps

1. Install the MCP Python SDK: `pip install mcp`
2. Use the basic client example above
3. Test with your Google Sheets URL: `https://docs.google.com/spreadsheets/d/1pQkjKOzOpVtQ4QIAYeb26IKcRIAFwQez/edit`
4. Extract the sheet ID: `1pQkjKOzOpVtQ4QIAYeb26IKcRIAFwQez`
5. Call the `read_sheet` tool with appropriate parameters

Your MCP server is already properly configured and authenticated - you just need a client to communicate with it!