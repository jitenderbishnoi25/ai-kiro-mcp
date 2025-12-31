# Project Structure

## Root Directory
```
ai-kiro-mcp/
├── .git/                    # Git version control
├── .kiro/                   # Kiro AI configuration
│   └── steering/            # AI assistant guidance documents
├── mcp_config.json          # MCP server configuration (main config file)
├── README.md                # Project documentation
└── ../kiro-sheets-agent-*.json  # Google service account credentials (external)
```

## Key Files

### `mcp_config.json`
- **Purpose**: Central configuration for MCP servers
- **Structure**: JSON object with `mcpServers` containing server definitions
- **Location**: Project root
- **Critical**: This file defines how Kiro AI connects to external services

### Service Account Credentials
- **Location**: Parent directory (`../kiro-sheets-agent-34e2628de8e6.json`)
- **Purpose**: Google Cloud authentication
- **Security**: Keep outside project root, never commit to version control

## Configuration Patterns

### MCP Server Definition
Each server in `mcp_config.json` follows this pattern:
```json
{
  "server-name": {
    "command": "execution-command",
    "args": ["package-or-arguments"],
    "env": {
      "ENV_VAR": "value"
    }
  }
}
```

### Environment Variables
- Use relative paths for credential files
- Keep sensitive data in environment variables
- Reference Google Cloud project consistently

## Conventions
- Configuration files use JSON format
- Credential files stored outside project directory
- MCP server names use kebab-case
- Environment variables use UPPER_SNAKE_CASE