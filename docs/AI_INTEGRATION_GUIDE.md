# AI Integration Guide

This guide provides comprehensive documentation for integrating AI assistants with the KiCAD MCP Server, including configuration modes, security considerations, and testing procedures.

---

## Table of Contents

1. [Overview](#overview)
2. [Integration Modes](#integration-modes)
3. [Configuration](#configuration)
4. [Environment Variables](#environment-variables)
5. [Security Considerations](#security-considerations)
6. [Basic Functionality Tests](#basic-functionality-tests)
7. [Troubleshooting](#troubleshooting)
8. [Best Practices](#best-practices)

---

## Overview

The KiCAD MCP Server supports multiple integration modes to accommodate different AI assistant architectures and use cases. The server implements the [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) specification, enabling standardized communication between AI assistants and KiCAD.

**Key Features:**
- Multiple operation modes (MCP, API, Bridge)
- Secure project path restrictions
- Environment-based configuration
- Real-time project state access via resources
- Comprehensive tool validation

---

## Integration Modes

The server supports three distinct operation modes, configurable via the `clientMode` parameter in `config/mcp-server-config.json`:

### 1. MCP Mode (Default)

**Description:** Standard Model Context Protocol mode for direct integration with MCP-compatible AI assistants.

**Use Cases:**
- Claude Desktop application
- Claude Code CLI tool
- Cline VSCode extension
- Custom MCP clients

**Communication:** STDIO-based JSON-RPC 2.0

**Configuration:**
```json
{
  "clientMode": "mcp"
}
```

**Supported Capabilities:**
- Tools (52 PCB design operations)
- Resources (8 dynamic project state resources)
- Prompts (guided workflows)

---

### 2. API Mode

**Description:** REST API mode exposing KiCAD operations via HTTP endpoints.

**Use Cases:**
- Web-based AI integrations
- Custom dashboards
- Remote KiCAD control
- Cross-language integrations

**Communication:** HTTP/REST with JSON payloads

**Configuration:**
```json
{
  "clientMode": "api",
  "api": {
    "port": 3000,
    "host": "localhost",
    "enableCors": false
  }
}
```

**Endpoints:**
- `POST /api/tools/{toolName}` - Execute tool
- `GET /api/resources/{resourceUri}` - Fetch resource
- `GET /api/health` - Health check
- `GET /api/capabilities` - List available tools and resources

---

### 3. Bridge Mode

**Description:** Bridge mode for connecting to external KiCAD instances or forwarding operations.

**Use Cases:**
- Distributed KiCAD workflows
- Remote PCB design collaboration
- Load balancing across multiple KiCAD instances
- Integration with external design tools

**Communication:** HTTP with persistent connections

**Configuration:**
```json
{
  "clientMode": "bridge",
  "api": {
    "port": 3000,
    "host": "0.0.0.0"
  }
}
```

**Note:** Bridge mode is experimental and requires additional setup for production use.

---

## Configuration

### Main Configuration File

**Location:** `config/mcp-server-config.json`

This file contains all server configuration options with JSON schema validation.

**Example Configuration:**
```json
{
  "clientMode": "mcp",
  "server": {
    "logLevel": "info",
    "logDir": "",
    "autoLaunchUI": false
  },
  "kicad": {
    "pythonPath": "",
    "kicadPath": ""
  },
  "security": {
    "allowedProjectPaths": [],
    "restrictFileOperations": false
  },
  "features": {
    "enableResources": true,
    "enablePrompts": true,
    "maxConcurrentOperations": 3
  }
}
```

### Configuration Parameters

#### clientMode
- **Type:** `string`
- **Options:** `"mcp"`, `"api"`, `"bridge"`
- **Default:** `"mcp"`
- **Description:** Determines the server operation mode

#### server.logLevel
- **Type:** `string`
- **Options:** `"error"`, `"warn"`, `"info"`, `"debug"`, `"trace"`
- **Default:** `"info"`
- **Description:** Controls logging verbosity

#### server.autoLaunchUI
- **Type:** `boolean`
- **Default:** `false`
- **Description:** Automatically launch KiCAD UI when operations require it

#### kicad.pythonPath
- **Type:** `string`
- **Default:** `""` (auto-detect)
- **Description:** Path to KiCAD Python modules (e.g., `/usr/lib/kicad/lib/python3/dist-packages`)

#### security.allowedProjectPaths
- **Type:** `array` of `string`
- **Default:** `[]` (allow all)
- **Description:** Whitelist of base directories for project operations

#### security.restrictFileOperations
- **Type:** `boolean`
- **Default:** `false`
- **Description:** Restrict all file operations to project directories only

#### features.maxConcurrentOperations
- **Type:** `integer`
- **Range:** 1-10
- **Default:** `3`
- **Description:** Maximum number of simultaneous KiCAD operations

---

## Environment Variables

Environment variables provide runtime configuration and credentials without modifying configuration files.

### Creating a .env File

Create a `.env` file in the project root directory:

```bash
# KiCAD MCP Server Environment Configuration
# This file should NEVER be committed to version control

# ============================================
# KiCAD Paths
# ============================================

# Path to KiCAD Python modules (auto-detected if not set)
PYTHONPATH=/usr/lib/kicad/lib/python3/dist-packages

# Path to KiCAD installation directory
KICAD_PATH=/usr/share/kicad

# ============================================
# Server Configuration
# ============================================

# Logging level: error, warn, info, debug, trace
LOG_LEVEL=info

# Custom log directory (optional)
LOG_DIR=/var/log/kicad-mcp

# Auto-launch KiCAD UI when needed
KICAD_AUTO_LAUNCH=false

# Node environment
NODE_ENV=production

# ============================================
# Security
# ============================================

# Comma-separated list of allowed project base paths
ALLOWED_PROJECT_PATHS=/home/user/kicad-projects,/mnt/shared/pcb-designs

# Restrict file operations to project directories only
RESTRICT_FILE_OPERATIONS=false

# ============================================
# API/Bridge Mode (only used when clientMode is 'api' or 'bridge')
# ============================================

# API server port
API_PORT=3000

# API server host
API_HOST=localhost

# Enable CORS for API mode
ENABLE_CORS=false

# API authentication token (required for production API mode)
# API_AUTH_TOKEN=your-secret-token-here

# ============================================
# Feature Flags
# ============================================

# Enable MCP resources capability
ENABLE_RESOURCES=true

# Enable MCP prompts capability
ENABLE_PROMPTS=true

# Maximum concurrent operations
MAX_CONCURRENT_OPERATIONS=3

# ============================================
# External Integrations (Future Use)
# ============================================

# JLCPCB API credentials (when implemented)
# JLCPCB_API_KEY=your-api-key-here
# JLCPCB_API_SECRET=your-api-secret-here

# Digikey API credentials (when implemented)
# DIGIKEY_CLIENT_ID=your-client-id-here
# DIGIKEY_CLIENT_SECRET=your-client-secret-here

# ============================================
# Development & Debugging
# ============================================

# Enable debug mode
DEBUG=false

# Python debug logging
PYTHON_DEBUG=false

# Enable performance profiling
ENABLE_PROFILING=false
```

### Environment Variable Priority

Configuration values are resolved in this order (highest to lowest priority):

1. Environment variables (`.env` file or system environment)
2. `config/mcp-server-config.json` settings
3. Default values from schema

### Platform-Specific Paths

#### Linux
```bash
PYTHONPATH=/usr/lib/kicad/lib/python3/dist-packages
KICAD_PATH=/usr/share/kicad
```

#### Windows
```bash
PYTHONPATH=C:\\Program Files\\KiCad\\9.0\\lib\\python3\\dist-packages
KICAD_PATH=C:\\Program Files\\KiCad\\9.0
```

#### macOS
```bash
PYTHONPATH=/Applications/KiCad/KiCad.app/Contents/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages
KICAD_PATH=/Applications/KiCad/KiCad.app
```

---

## Security Considerations

### Project Path Restrictions

To prevent unauthorized file system access, configure `allowedProjectPaths`:

```json
{
  "security": {
    "allowedProjectPaths": [
      "/home/user/kicad-projects",
      "/mnt/shared/pcb-designs"
    ],
    "restrictFileOperations": true
  }
}
```

**Impact:**
- Project creation/opening restricted to specified directories
- File export operations restricted to project subdirectories
- Prevents path traversal attacks

### Credential Management

**Never commit credentials to version control:**
- Use `.env` files for sensitive data (already in `.gitignore`)
- Use environment variables for production deployments
- Rotate API keys regularly
- Use minimal permission scopes

**Best Practices:**
```bash
# ✓ Good - Environment variable
API_AUTH_TOKEN=${SECRET_TOKEN}

# ✗ Bad - Hardcoded in config
"apiToken": "sk-1234567890abcdef"
```

### Network Security (API/Bridge Mode)

When using API or Bridge mode:

1. **Bind to localhost only** for local-only access:
   ```json
   "api": { "host": "localhost" }
   ```

2. **Enable CORS selectively**:
   ```json
   "api": { "enableCors": false }
   ```

3. **Use authentication** in production:
   ```bash
   API_AUTH_TOKEN=your-secure-random-token
   ```

4. **Use HTTPS** for remote access:
   - Deploy behind reverse proxy (nginx, Apache)
   - Configure TLS certificates
   - Enable HSTS headers

### File Operation Safety

The server includes several safety mechanisms:

1. **Path validation** - Prevents directory traversal
2. **File type restrictions** - Only processes valid KiCAD files
3. **Size limits** - Prevents resource exhaustion
4. **Sandbox mode** - Optional restriction to project directories

### Audit Logging

Enable detailed logging for security auditing:

```bash
LOG_LEVEL=info
# Logs include:
# - All tool invocations
# - File operations
# - Authentication attempts (API mode)
# - Error conditions
```

Logs location: `~/.kicad-mcp/logs/kicad_interface.log`

---

## Basic Functionality Tests

### Test 1: Server Connection (MCP Mode)

**Purpose:** Verify the server starts and responds to MCP protocol

**Steps:**
1. Configure your MCP client (e.g., Claude Desktop)
2. Start the client application
3. Check server appears in available tools list
4. Send a simple query: "Check if KiCAD is available"

**Expected Result:**
- Server initializes without errors
- Client lists 52 available tools
- 8 resources are accessible

**Verification:**
```bash
# Check server logs
tail -f ~/.kicad-mcp/logs/kicad_interface.log

# Should see:
# "MCP server initialized"
# "Capabilities: tools=52, resources=8, prompts=X"
```

---

### Test 2: Project Creation

**Purpose:** Test basic KiCAD operation

**Steps:**
1. In your AI assistant, request: "Create a new KiCAD project called 'test-board' in /tmp"
2. Wait for confirmation
3. Check that project files were created

**Expected Result:**
- Project directory created at `/tmp/test-board/`
- Files created: `test-board.kicad_pro`, `test-board.kicad_pcb`, `test-board.kicad_sch`

**Verification:**
```bash
ls -la /tmp/test-board/
# Should show:
# test-board.kicad_pro
# test-board.kicad_pcb
# test-board.kicad_sch
```

---

### Test 3: Resource Access

**Purpose:** Test MCP resources capability

**Steps:**
1. After creating a project, request: "Show me the current project information"
2. Request: "List all components in the project"

**Expected Result:**
- AI assistant retrieves and displays project metadata
- Component list returned (empty for new project)

**MCP Resource URIs Tested:**
- `kicad://project/current/info`
- `kicad://project/current/components`

---

### Test 4: Tool Execution

**Purpose:** Test tool parameter validation and execution

**Steps:**
1. Request: "Set the board size to 100mm x 100mm"
2. Request: "Add a rectangular board outline"
3. Request: "Export a board preview image"

**Expected Result:**
- Each operation completes successfully
- Board properties updated
- Preview image generated

**Verification:**
```bash
# Check board file was modified
stat /tmp/test-board/test-board.kicad_pcb

# Check for preview image
ls -la ~/.kicad-mcp/previews/
```

---

### Test 5: Error Handling

**Purpose:** Verify proper error reporting

**Steps:**
1. Request an invalid operation: "Place a component without specifying a footprint"
2. Request an operation without loading a project: "Set board size to 50x50"

**Expected Result:**
- Clear error messages returned to AI assistant
- Server continues operating after errors
- Errors logged with context

**Log Verification:**
```bash
grep -i error ~/.kicad-mcp/logs/kicad_interface.log
# Should show descriptive error messages
```

---

### Test 6: API Mode (If Enabled)

**Purpose:** Test REST API functionality

**Prerequisites:**
```json
{
  "clientMode": "api",
  "api": { "port": 3000, "host": "localhost" }
}
```

**Steps:**
1. Start server in API mode
2. Test health endpoint:
   ```bash
   curl http://localhost:3000/api/health
   ```
3. Test capabilities endpoint:
   ```bash
   curl http://localhost:3000/api/capabilities
   ```
4. Execute a tool:
   ```bash
   curl -X POST http://localhost:3000/api/tools/check_kicad_ui \
     -H "Content-Type: application/json" \
     -d '{}'
   ```

**Expected Results:**
- Health endpoint returns `{"status": "ok"}`
- Capabilities endpoint lists all tools and resources
- Tool execution returns result or error

---

### Test 7: Security Restrictions

**Purpose:** Verify path restrictions work

**Prerequisites:**
```json
{
  "security": {
    "allowedProjectPaths": ["/home/user/kicad-projects"],
    "restrictFileOperations": true
  }
}
```

**Steps:**
1. Request: "Create a project in /tmp/test" (outside allowed paths)
2. Request: "Create a project in /home/user/kicad-projects/test" (inside allowed paths)

**Expected Results:**
- First request denied with security error
- Second request succeeds

---

## Troubleshooting

### Server Won't Start

**Symptoms:**
- Server not appearing in MCP client
- Immediate crash after launch
- No logs generated

**Debugging Steps:**

1. **Check Node.js and npm versions:**
   ```bash
   node --version  # Should be 18+
   npm --version
   ```

2. **Verify build completed:**
   ```bash
   ls -la dist/index.js
   # File should exist
   ```

3. **Check KiCAD Python module:**
   ```bash
   python3 -c "import pcbnew; print(pcbnew.GetBuildVersion())"
   # Should print KiCAD version
   ```

4. **Check PYTHONPATH:**
   ```bash
   echo $PYTHONPATH
   # Should include KiCAD Python path
   ```

5. **Run server manually for detailed errors:**
   ```bash
   node dist/index.js
   # Watch for error messages
   ```

---

### Tools Not Working

**Symptoms:**
- Tools execute but produce no results
- Errors about missing project
- File operation failures

**Debugging Steps:**

1. **Verify project is loaded:**
   - Always create/open project before board operations
   - Check resource: `kicad://project/current/info`

2. **Check file permissions:**
   ```bash
   # Project directory should be writable
   ls -la /path/to/project/
   ```

3. **Enable debug logging:**
   ```bash
   LOG_LEVEL=debug node dist/index.js
   ```

4. **Review Python errors:**
   ```bash
   tail -f ~/.kicad-mcp/logs/kicad_interface.log | grep -i error
   ```

---

### Resource Access Fails

**Symptoms:**
- Resources return empty or error
- AI assistant can't see project state

**Debugging Steps:**

1. **Verify resources enabled:**
   ```json
   { "features": { "enableResources": true } }
   ```

2. **Check project loaded:**
   ```bash
   # In AI assistant:
   "What project is currently open?"
   ```

3. **Test resource URIs directly** (if using API mode):
   ```bash
   curl http://localhost:3000/api/resources/kicad://project/current/info
   ```

---

### API Mode Issues

**Symptoms:**
- Can't connect to API endpoint
- Connection refused errors
- CORS errors in browser

**Debugging Steps:**

1. **Check server is in API mode:**
   ```json
   { "clientMode": "api" }
   ```

2. **Verify port not in use:**
   ```bash
   netstat -tuln | grep 3000
   # Or:
   lsof -i :3000
   ```

3. **Check firewall rules:**
   ```bash
   sudo ufw status  # Linux
   # Ensure port 3000 is allowed if needed
   ```

4. **Enable CORS if accessing from browser:**
   ```json
   { "api": { "enableCors": true } }
   ```

---

## Best Practices

### 1. Project Organization

**Structure:**
```
~/kicad-projects/
├── project-name/
│   ├── project-name.kicad_pro
│   ├── project-name.kicad_pcb
│   ├── project-name.kicad_sch
│   ├── gerbers/
│   ├── exports/
│   └── docs/
```

**Tips:**
- Use descriptive project names
- Keep one project per directory
- Use subdirectories for outputs (gerbers, PDFs, etc.)
- Back up projects regularly

### 2. AI Assistant Usage

**Effective Prompts:**
```
✓ "Create a 50x50mm board with 4 mounting holes at the corners"
✓ "Place an LED at x=10, y=10 using footprint LED_SMD:LED_0805_2012Metric"
✓ "Export Gerber files to the 'fabrication' folder"

✗ "Make me a circuit board"  (too vague)
✗ "Add a part at position 5,5"  (missing footprint, units unclear)
```

**Best Practices:**
- Be specific with coordinates and units (mm recommended)
- Specify footprint library and name for components
- Use absolute paths for file operations
- Check project state with resources before major operations

### 3. Performance Optimization

**For Large Projects:**
- Limit concurrent operations (set `maxConcurrentOperations: 1-2`)
- Avoid frequent resource polling
- Use batch operations when available
- Close projects when not in use

**For API Mode:**
- Implement connection pooling
- Use caching for static resources
- Enable gzip compression
- Rate limit requests

### 4. Error Recovery

**Strategies:**
- Always check operation results before proceeding
- Implement retry logic for transient failures
- Keep backups before destructive operations
- Log all operations for audit trail

### 5. Security Hardening

**Production Checklist:**
- [ ] Set `restrictFileOperations: true`
- [ ] Configure `allowedProjectPaths`
- [ ] Use strong `API_AUTH_TOKEN` in API mode
- [ ] Bind API to `localhost` unless remote access needed
- [ ] Enable audit logging (`LOG_LEVEL: info` minimum)
- [ ] Keep `.env` out of version control
- [ ] Rotate credentials regularly
- [ ] Use HTTPS for remote API access
- [ ] Monitor logs for suspicious activity
- [ ] Keep KiCAD and dependencies updated

---

## Additional Resources

### Documentation
- [Main README](../README.md) - Getting started and installation
- [Client Configuration Guide](./CLIENT_CONFIGURATION.md) - MCP client setup
- [Platform Guide](./PLATFORM_GUIDE.md) - Platform-specific instructions
- [Troubleshooting](./WINDOWS_TROUBLESHOOTING.md) - Common issues and solutions

### External Links
- [Model Context Protocol Specification](https://modelcontextprotocol.io/)
- [KiCAD Python API Documentation](https://docs.kicad.org/doxygen-python/)
- [KiCAD Official Website](https://www.kicad.org/)

### Support
- [GitHub Issues](https://github.com/PoleG97/KiCAD-MCP-Server/issues) - Bug reports and feature requests
- [Contributing Guide](../CONTRIBUTING.md) - How to contribute

---

## Changelog

**Version 1.0** (November 2025)
- Initial AI Integration Guide
- Documented all three operation modes (MCP, API, Bridge)
- Added comprehensive security section
- Included basic functionality tests
- Added troubleshooting procedures

---

**Note:** This guide is maintained alongside the codebase. If you find discrepancies or areas for improvement, please open an issue or submit a pull request.
