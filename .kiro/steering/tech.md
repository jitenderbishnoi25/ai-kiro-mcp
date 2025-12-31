# Technology Stack

## MCP (Model Context Protocol)
- Primary technology for AI assistant integration
- Uses `mcp-gsheets@latest` package for Google Sheets connectivity
- Configuration managed through `mcp_config.json`

## Runtime & Package Management
- **Python**: Runtime environment for MCP server execution
- **UV/UVX**: Python package manager and runner for executing MCP servers
- **Google Cloud Platform**: Authentication and API access

## Authentication
- **Google Service Account**: JSON key-based authentication
- **Environment Variables**: Secure credential management
- **Project ID**: `kiro-sheets-agent`

## Configuration
- **mcp_config.json**: Central MCP server configuration
- **Environment Variables**:
  - `GOOGLE_PROJECT_ID`: Google Cloud project identifier
  - `GOOGLE_APPLICATION_CREDENTIALS`: Path to service account key file

## Common Commands
Since this is a configuration-only project, there are no build or test commands. The MCP server is executed automatically by the Kiro AI system when needed.

### Manual Testing (if needed)
```bash
# Test MCP server connection (run from project root)
uvx mcp-gsheets@latest
```