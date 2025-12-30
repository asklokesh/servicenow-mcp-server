# ServiceNow MCP Server

<!-- mcp-name: io.github.asklokesh/servicenow-mcp -->

[![Python Version](https://img.shields.io/badge/python-3.9%2B-blue)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![MCP Version](https://img.shields.io/badge/MCP-1.1.0%2B-green)](https://modelcontextprotocol.io/)
[![CI Status](https://github.com/asklokesh/servicenow-mcp-server/workflows/CI/badge.svg)](https://github.com/asklokesh/servicenow-mcp-server/actions)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

A highly customizable Model Context Protocol (MCP) server for ServiceNow API integration. This server enables AI assistants and development tools to interact with ServiceNow instances through a standardized interface.

**Enterprise-grade ServiceNow integration** - Connect ServiceNow with modern development tools and automation frameworks through the Model Context Protocol.

## Features

- **Comprehensive ServiceNow API Coverage**
  - Incident Management
  - Change Management
  - Problem Management
  - Configuration Management Database (CMDB)
  - Service Catalog
  - Knowledge Base
  - User Management
  - Custom Table Operations

- **Highly Customizable**
  - Feature flags to enable/disable modules
  - Environment-based configuration
  - JSON configuration files with override support
  - Flexible authentication options

- **Production Ready**
  - Async/await support for high performance
  - Retry logic with exponential backoff
  - Comprehensive error handling
  - Structured JSON logging
  - Rate limit handling

- **Easy Integration**
  - Compatible with Claude Desktop, VS Code, Cursor, and other MCP clients
  - Standard MCP protocol implementation
  - Simple CLI interface

## Installation

### Prerequisites

- Python 3.9 or higher
- ServiceNow instance with API access
- ServiceNow user credentials with appropriate permissions

### Install from Source

```bash
# Clone the repository
git clone https://github.com/asklokesh/servicenow-mcp-server.git
cd servicenow-mcp-server

# Create a virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install the package
pip install -e .
```

### Install from PyPI (coming soon)

```bash
pip install servicenow-mcp-server
```

## Configuration

### Quick Start with Environment Variables

1. Copy the example environment file:
   ```bash
   cp .env.example .env
   ```

2. Edit `.env` with your ServiceNow credentials:
   ```env
   SERVICENOW_INSTANCE=your-instance.service-now.com
   SERVICENOW_USERNAME=your-username
   SERVICENOW_PASSWORD=your-password
   ```

3. Run the server:
   ```bash
   servicenow-mcp
   ```

### Advanced Configuration

The server supports multiple configuration methods with the following precedence:
1. Environment variables (highest priority)
2. `config/local.json` (local overrides)
3. `config/default.json` (base configuration)

#### Configuration Options

```json
{
  "servicenow": {
    "instance": "your-instance.service-now.com",
    "username": "your-username",
    "password": "your-password",
    "api_version": "v2",
    "timeout": 30,
    "max_retries": 3
  },
  "features": {
    "incident_management": true,
    "change_management": true,
    "problem_management": true,
    "service_catalog": true,
    "knowledge_base": true,
    "user_management": true,
    "cmdb": true,
    "custom_tables": true
  },
  "logging": {
    "level": "INFO",
    "format": "json",
    "file": "logs/servicenow-mcp.log"
  }
}
```

## Integration with MCP Clients

### Desktop Configuration

Add to your MCP client configuration:

```json
{
  "mcpServers": {
    "servicenow": {
      "command": "servicenow-mcp",
      "env": {
        "SERVICENOW_INSTANCE": "your-instance.service-now.com",
        "SERVICENOW_USERNAME": "your-username",
        "SERVICENOW_PASSWORD": "your-password"
      }
    }
  }
}
```

### VS Code / Cursor

Add to your workspace settings:

```json
{
  "mcp.servers": {
    "servicenow": {
      "command": "servicenow-mcp",
      "args": ["--config-dir", "./config"],
      "env": {
        "SERVICENOW_INSTANCE": "your-instance.service-now.com",
        "SERVICENOW_USERNAME": "your-username",
        "SERVICENOW_PASSWORD": "your-password"
      }
    }
  }
}
```

## Available Tools

### Table Operations

- **query_table** - Query any ServiceNow table with filters
- **get_record** - Retrieve a single record by sys_id
- **create_record** - Create new records
- **update_record** - Update existing records
- **delete_record** - Delete records

### Incident Management

- **incident_create** - Create new incidents
- **incident_update** - Update incidents (state, notes, resolution)
- **incident_search** - Search incidents with multiple filters

### Change Management

- **change_create** - Create change requests
- **change_search** - Search change requests

### CMDB Operations

- **ci_search** - Search configuration items
- **ci_relationships** - Get CI relationships

### User Management

- **user_search** - Search users by various criteria

### Knowledge Base

- **kb_search** - Search knowledge articles

### Service Catalog

- **catalog_items** - List catalog items

### Analytics

- **get_stats** - Get aggregate statistics from any table

## Usage Examples

### Creating an Incident

```json
{
  "tool": "incident_create",
  "arguments": {
    "short_description": "Email server down",
    "description": "Production email server is not responding",
    "urgency": 1,
    "impact": 1,
    "assignment_group": "Email Support"
  }
}
```

### Searching for Configuration Items

```json
{
  "tool": "ci_search",
  "arguments": {
    "name": "*prod*",
    "class": "cmdb_ci_server",
    "operational_status": 1,
    "limit": 50
  }
}
```

### Custom Table Query

```json
{
  "tool": "query_table",
  "arguments": {
    "table": "u_custom_application",
    "query": "active=true^u_environment=production",
    "fields": ["name", "u_version", "u_owner"],
    "order_by": "-sys_updated_on"
  }
}
```

## Security Considerations

- Never commit credentials to version control
- Use environment variables or secure secret management
- Implement least-privilege access for ServiceNow users
- Enable audit logging in production environments
- Consider using OAuth instead of basic auth for production

## Development

### Setting Up Development Environment

```bash
# Install development dependencies
pip install -e ".[dev]"

# Run tests
pytest

# Run linting
ruff check src/
black src/ --check

# Type checking
mypy src/
```

### Running Tests

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=servicenow_mcp --cov-report=html

# Run specific test file
pytest tests/test_client.py
```

## Troubleshooting

### Common Issues

1. **Authentication Failed**
   - Verify credentials are correct
   - Check if user has API access permissions
   - Ensure instance URL is correct (with or without https://)

2. **Connection Timeout**
   - Increase timeout in configuration
   - Check network connectivity
   - Verify ServiceNow instance is accessible

3. **Rate Limiting**
   - Server automatically handles rate limits with retry
   - Consider reducing request frequency
   - Check ServiceNow rate limit settings

### Debug Mode

Enable debug logging:
```bash
servicenow-mcp --log-level DEBUG
```

Or set in environment:
```bash
export MCP_LOG_LEVEL=DEBUG
```

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Add tests for new functionality
4. Ensure all tests pass
5. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

- GitHub Issues: [https://github.com/asklokesh/servicenow-mcp-server/issues](https://github.com/asklokesh/servicenow-mcp-server/issues)
- Documentation: [https://github.com/asklokesh/servicenow-mcp-server/wiki](https://github.com/asklokesh/servicenow-mcp-server/wiki)

## Acknowledgments

- Built on the [Model Context Protocol](https://modelcontextprotocol.io/) standard
- ServiceNow is a registered trademark of ServiceNow, Inc.