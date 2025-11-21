# KiCAD MCP Server

A production-ready Model Context Protocol (MCP) server that enables AI assistants like Claude to interact with KiCAD for PCB design automation. Built on the MCP 2025-06-18 specification, this server provides comprehensive tool schemas and real-time project state access for intelligent PCB design workflows.

---

## Quick Navigation

### 🚀 Getting Started
- [Overview](#overview) - What is this project?
- [Prerequisites](#prerequisites) - What you need before installing
- [Installation](#installation) - Step-by-step setup guides
- [Configuration](#configuration) - Connect to your AI assistant

### 📚 Documentation
- [Available Tools](#available-tools) - Complete tool reference by category
- [Usage Examples](#usage-examples) - Common workflows and patterns
- [AI Integration Guide](docs/AI_INTEGRATION_GUIDE.md) - **Comprehensive AI integration documentation**
- [Architecture](#architecture) - Technical implementation details

### 🔧 Configuration & Setup
- [Client Configuration](docs/CLIENT_CONFIGURATION.md) - MCP client setup (Claude, Cline, etc.)
- [Platform Guide](docs/PLATFORM_GUIDE.md) - Platform-specific instructions
- [Windows Setup](docs/WINDOWS_TROUBLESHOOTING.md) - Windows-specific guide

### 🛠️ Development & Advanced
- [Development](#development) - Building from source
- [Contributing](CONTRIBUTING.md) - How to contribute
- [Roadmap](docs/ROADMAP.md) - Future plans
- [Known Issues](docs/KNOWN_ISSUES.md) - Current limitations

### 📋 Project Info
- [What's New](#whats-new-in-v210) - Latest version highlights
- [Project Status](#project-status) - Current features and roadmap
- [Troubleshooting](#troubleshooting) - Common issues and solutions
- [License](#license) - MIT License

---

## Overview

The [Model Context Protocol](https://modelcontextprotocol.io/) is an open standard from Anthropic that allows AI assistants to securely connect to external tools and data sources. This implementation provides a standardized bridge between AI assistants and KiCAD, enabling natural language control of professional PCB design operations.

**Key Capabilities:**
- 52 fully-documented tools with JSON Schema validation
- 8 dynamic resources exposing project state
- Full MCP 2025-06-18 protocol compliance
- Cross-platform support (Linux, Windows, macOS)
- Real-time KiCAD UI integration
- Comprehensive error handling and logging
- Multiple operation modes (MCP, API, Bridge) - [Learn more](docs/AI_INTEGRATION_GUIDE.md#integration-modes)

## What's New in v2.1.0

### Comprehensive Tool Schemas
Every tool now includes complete JSON Schema definitions with:
- Detailed parameter descriptions and constraints
- Input validation with type checking
- Required vs. optional parameter specifications
- Enumerated values for categorical inputs
- Clear documentation of what each tool does

### Resources Capability
Access project state without executing tools. [See all resources →](#available-tools)
- `kicad://project/current/info` - Project metadata
- `kicad://project/current/board` - Board properties
- `kicad://project/current/components` - Component list (JSON)
- `kicad://project/current/nets` - Electrical nets
- `kicad://project/current/layers` - Layer stack configuration
- `kicad://project/current/design-rules` - Current DRC settings
- `kicad://project/current/drc-report` - Design rule violations
- `kicad://board/preview.png` - Board visualization (PNG)

### Multiple Operation Modes
New configuration system supporting three modes: [Learn more →](docs/AI_INTEGRATION_GUIDE.md#integration-modes)
- **MCP Mode** - Direct AI assistant integration (default)
- **API Mode** - REST API for web integrations
- **Bridge Mode** - Connect to external KiCAD instances

### Protocol Compliance
- Updated to MCP SDK 1.21.0 (latest)
- Full JSON-RPC 2.0 support
- Proper capability negotiation
- Standards-compliant error codes

---

### 📖 New Documentation
- **[AI Integration Guide](docs/AI_INTEGRATION_GUIDE.md)** - Comprehensive guide covering:
  - All operation modes (MCP, API, Bridge)
  - Security best practices and configuration
  - Environment variable setup (.env templates)
  - Basic functionality tests
  - Troubleshooting procedures

## Available Tools

The server provides 52 tools organized into functional categories. For detailed usage and AI integration patterns, see the [AI Integration Guide](docs/AI_INTEGRATION_GUIDE.md).

### Quick Reference by Category
- [Project Management](#project-management-4-tools) - Create, open, save projects
- [Board Operations](#board-operations-9-tools) - Board setup and configuration  
- [Component Placement](#component-placement-10-tools) - Place and manipulate components
- [Routing & Nets](#routing--nets-8-tools) - Electrical connections and traces
- [Library Management](#library-management-4-tools) - Search and manage footprints
- [Design Rules](#design-rules-4-tools) - DRC configuration and validation
- [Export](#export-5-tools) - Generate manufacturing files
- [Schematic Design](#schematic-design-6-tools) - Schematic creation and editing
- [UI Management](#ui-management-2-tools) - KiCAD application control

---

### Project Management (4 tools)
- `create_project` - Initialize new KiCAD projects
- `open_project` - Load existing project files
- `save_project` - Save current project state
- `get_project_info` - Retrieve project metadata

### Board Operations (9 tools)
- `set_board_size` - Configure PCB dimensions
- `add_board_outline` - Create board edge (rectangle, circle, polygon)
- `add_layer` - Add custom layers to stack
- `set_active_layer` - Switch working layer
- `get_layer_list` - List all board layers
- `get_board_info` - Retrieve board properties
- `get_board_2d_view` - Generate board preview image
- `add_mounting_hole` - Place mounting holes
- `add_board_text` - Add text annotations

### Component Placement (10 tools)
- `place_component` - Place single component with footprint
- `move_component` - Reposition existing component
- `rotate_component` - Rotate component by angle
- `delete_component` - Remove component from board
- `edit_component` - Modify component properties
- `get_component_properties` - Query component details
- `get_component_list` - List all placed components
- `place_component_array` - Create component grids/patterns
- `align_components` - Align multiple components
- `duplicate_component` - Copy existing component

### Routing & Nets (8 tools)
- `add_net` - Create electrical net
- `route_trace` - Route copper traces
- `add_via` - Place vias for layer transitions
- `delete_trace` - Remove traces
- `get_nets_list` - List all nets
- `create_netclass` - Define net class with rules
- `add_copper_pour` - Create copper zones/pours
- `route_differential_pair` - Route differential signals

### Library Management (4 tools)
- `list_libraries` - List available footprint libraries
- `search_footprints` - Search for footprints
- `list_library_footprints` - List footprints in library
- `get_footprint_info` - Get footprint details

### Design Rules (4 tools)
- `set_design_rules` - Configure DRC parameters
- `get_design_rules` - Retrieve current rules
- `run_drc` - Execute design rule check
- `get_drc_violations` - Get DRC error report

### Export (5 tools)
- `export_gerber` - Generate Gerber fabrication files
- `export_pdf` - Export PDF documentation
- `export_svg` - Create SVG vector graphics
- `export_3d` - Generate 3D models (STEP/VRML)
- `export_bom` - Produce bill of materials

### Schematic Design (6 tools)
- `create_schematic` - Initialize new schematic
- `load_schematic` - Open existing schematic
- `add_schematic_component` - Place symbols
- `add_schematic_wire` - Connect component pins
- `list_schematic_libraries` - List symbol libraries
- `export_schematic_pdf` - Export schematic PDF

### UI Management (2 tools)
- `check_kicad_ui` - Check if KiCAD is running
- `launch_kicad_ui` - Launch KiCAD application

## Prerequisites

### Required Software

**KiCAD 9.0 or Higher**
- Download from [kicad.org/download](https://www.kicad.org/download/)
- Must include Python module (pcbnew)
- Verify installation:
  ```bash
  python3 -c "import pcbnew; print(pcbnew.GetBuildVersion())"
  ```

**Node.js 18 or Higher**
- Download from [nodejs.org](https://nodejs.org/)
- Verify: `node --version` and `npm --version`

**Python 3.10 or Higher**
- Usually included with KiCAD
- Required packages (auto-installed):
  - kicad-skip >= 0.1.0 (schematic support)
  - Pillow >= 9.0.0 (image processing)
  - cairosvg >= 2.7.0 (SVG rendering)
  - colorlog >= 6.7.0 (logging)
  - pydantic >= 2.5.0 (validation)
  - requests >= 2.32.5 (HTTP client)
  - python-dotenv >= 1.0.0 (environment)

**MCP Client**
Choose one:
- [Claude Desktop](https://claude.ai/download) - Official Anthropic desktop app
- [Claude Code](https://docs.claude.com/claude-code) - Official CLI tool
- [Cline](https://github.com/cline/cline) - VSCode extension

### Supported Platforms
- **Linux** (Ubuntu 22.04+, Fedora, Arch) - Primary platform, fully tested
- **Windows 10/11** - Fully supported with automated setup
- **macOS** - Experimental support

## Installation

### Linux (Ubuntu/Debian)

```bash
# Install KiCAD 9.0
sudo add-apt-repository --yes ppa:kicad/kicad-9.0-releases
sudo apt-get update
sudo apt-get install -y kicad kicad-libraries

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# Clone and build
git clone https://github.com/mixelpixx/KiCAD-MCP-Server.git
cd KiCAD-MCP-Server
npm install
pip3 install -r requirements.txt
npm run build

# Verify
python3 -c "import pcbnew; print(pcbnew.GetBuildVersion())"
```

### Windows 10/11

**Automated Setup (Recommended):**
```powershell
git clone https://github.com/mixelpixx/KiCAD-MCP-Server.git
cd KiCAD-MCP-Server
.\setup-windows.ps1
```

The script will:
- Detect KiCAD installation
- Verify prerequisites
- Install dependencies
- Build project
- Generate configuration
- Run diagnostics

**Manual Setup:**
See [Windows Installation Guide](docs/WINDOWS_SETUP.md) for detailed instructions.

### macOS

```bash
# Install KiCAD 9.0 from kicad.org/download/macos

# Install Node.js
brew install node@20

# Clone and build
git clone https://github.com/mixelpixx/KiCAD-MCP-Server.git
cd KiCAD-MCP-Server
npm install
pip3 install -r requirements.txt
npm run build
```

## Configuration

### Quick Setup

Choose your AI assistant client and follow the guide:

- **[Claude Desktop](docs/CLIENT_CONFIGURATION.md#claude-desktop)** - Official Anthropic desktop app
- **[Cline (VSCode)](docs/CLIENT_CONFIGURATION.md#cline-vscode)** - VSCode extension
- **[Claude Code](docs/CLIENT_CONFIGURATION.md#claude-code)** - Official CLI tool
- **[Custom Clients](docs/AI_INTEGRATION_GUIDE.md#integration-modes)** - API/Bridge mode setup

### Configuration Files

- **`config/mcp-server-config.json`** - Main server configuration
  - Set operation mode (MCP, API, Bridge)
  - Configure security restrictions
  - Enable/disable features
  - [View schema →](config/mcp-server-config.json)

- **`.env`** - Environment variables (create from template)
  - KiCAD paths (PYTHONPATH, KICAD_PATH)
  - Logging configuration
  - Security settings
  - API credentials (when using API mode)
  - [See template →](docs/AI_INTEGRATION_GUIDE.md#environment-variables)

### Quick Start Example (Claude Desktop)

1. Edit configuration file:
   - **Linux/macOS:** `~/.config/Claude/claude_desktop_config.json`
   - **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

2. Add server configuration:
```json
{
  "mcpServers": {
    "kicad": {
      "command": "node",
      "args": ["/path/to/KiCAD-MCP-Server/dist/index.js"],
      "env": {
        "PYTHONPATH": "/path/to/kicad/python",
        "LOG_LEVEL": "info"
      }
    }
  }
}
```

3. **Platform-specific PYTHONPATH:**
   - **Linux:** `/usr/lib/kicad/lib/python3/dist-packages`
   - **Windows:** `C:\Program Files\KiCad\9.0\lib\python3\dist-packages`
   - **macOS:** `/Applications/KiCad/KiCad.app/Contents/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages`

4. Restart your AI assistant client

### Advanced Configuration

For advanced setup including API mode, security restrictions, and environment variables:
- **[Complete Configuration Guide →](docs/AI_INTEGRATION_GUIDE.md#configuration)**
- **[Client-Specific Instructions →](docs/CLIENT_CONFIGURATION.md)**
- **[Platform-Specific Setup →](docs/PLATFORM_GUIDE.md)**

## Usage Examples

### Basic PCB Design Workflow

```text
Create a new KiCAD project named 'LEDBoard' in my Documents folder.
Set the board size to 50mm x 50mm and add a rectangular outline.
Place a mounting hole at each corner, 3mm from the edges, with 3mm diameter.
Add text 'LED Controller v1.0' on the front silkscreen at position x=25mm, y=45mm.
```

### Component Placement

```text
Place an LED at x=10mm, y=10mm using footprint LED_SMD:LED_0805_2012Metric.
Create a grid of 4 resistors (R1-R4) starting at x=20mm, y=20mm with 5mm spacing.
Align all resistors horizontally and distribute them evenly.
```

### Routing

```text
Create a net named 'LED1' and route a 0.3mm trace from R1 pad 2 to LED1 anode.
Add a copper pour for GND on the bottom layer covering the entire board.
Create a differential pair for USB_P and USB_N with 0.2mm width and 0.15mm gap.
```

### Design Verification

```text
Set design rules with 0.15mm clearance and 0.2mm minimum track width.
Run a design rule check and show me any violations.
Export Gerber files to the 'fabrication' folder.
```

### Using Resources

Resources provide read-only access to project state:

```text
Show me the current component list.
What are the current design rules?
Display the board preview.
List all electrical nets.
```

## Architecture

### High-Level Overview

```
AI Assistant (Claude, etc.)
    ↓ MCP Protocol (JSON-RPC 2.0)
TypeScript Server (src/)
    ↓ STDIO / HTTP
Python Interface (python/)
    ↓ Python API
KiCAD (pcbnew, kicad-skip)
```

### Components

#### MCP Protocol Layer
- **Transport:** STDIO (MCP mode) or HTTP (API/Bridge mode)
- **Protocol Version:** MCP 2025-06-18 specification
- **Capabilities:** Tools (52), Resources (8), Prompts
- **Error Handling:** Standard JSON-RPC error codes
- [Operation modes →](docs/AI_INTEGRATION_GUIDE.md#integration-modes)

#### TypeScript Server (`src/`)
- Implements MCP protocol specification
- Manages Python subprocess lifecycle
- Handles message routing and validation
- Provides logging and error recovery
- Supports multiple operation modes (MCP, API, Bridge)

#### Python Interface (`python/`)
- **kicad_interface.py:** Main entry point, MCP message handler
- **schemas/tool_schemas.py:** JSON Schema definitions for all tools
- **resources/resource_definitions.py:** Resource handlers and URIs
- **commands/:** Modular command implementations
  - `project.py` - Project operations
  - `board.py` - Board manipulation
  - `component.py` - Component placement
  - `routing.py` - Trace routing and nets
  - `design_rules.py` - DRC operations
  - `export.py` - File generation
  - `schematic.py` - Schematic design
  - `library.py` - Footprint libraries

#### KiCAD Integration
- **pcbnew API:** Direct Python bindings to KiCAD
- **kicad-skip:** Schematic file manipulation
- **Platform Detection:** Cross-platform path handling
- **UI Management:** Automatic KiCAD UI launch/detection

### Configuration System

- **`config/mcp-server-config.json`** - Schema-based configuration
- **`.env` files** - Environment-specific settings
- **MCP client configs** - Client-side server registration
- [Full configuration guide →](docs/AI_INTEGRATION_GUIDE.md#configuration)

## Development

### Building from Source

```bash
# Install dependencies
npm install
pip3 install -r requirements.txt

# Build TypeScript
npm run build

# Watch mode for development
npm run dev
```

### Running Tests

```bash
# TypeScript tests
npm run test:ts

# Python tests
npm run test:py

# All tests with coverage
npm run test:coverage
```

### Linting and Formatting

```bash
# Lint TypeScript and Python
npm run lint

# Format code
npm run format
```

## Troubleshooting

### Common Issues

Below are quick solutions to common problems. For comprehensive troubleshooting including API mode, security issues, and detailed debugging steps, see the **[AI Integration Guide - Troubleshooting](docs/AI_INTEGRATION_GUIDE.md#troubleshooting)**.

#### Server Not Appearing in Client

**Symptoms:** MCP server doesn't show up in Claude Desktop or Cline

**Quick Fixes:**
1. Verify build completed: `ls dist/index.js`
2. Check configuration paths are absolute
3. Restart MCP client completely
4. Check client logs for error messages

[Detailed diagnostics →](docs/AI_INTEGRATION_GUIDE.md#server-wont-start)

---

#### Python Module Import Errors

**Symptoms:** `ModuleNotFoundError: No module named 'pcbnew'`

**Quick Fixes:**
1. Verify KiCAD installation: `python3 -c "import pcbnew"`
2. Check PYTHONPATH in configuration matches your KiCAD installation
3. Ensure KiCAD was installed with Python support

[Platform-specific paths →](#quick-start-example-claude-desktop)

---

#### Tool Execution Failures

**Symptoms:** Tools fail with unclear errors

**Quick Fixes:**
1. Check server logs: `~/.kicad-mcp/logs/kicad_interface.log`
2. Verify a project is loaded before running board operations
3. Ensure file paths are absolute, not relative
4. Check tool parameter types match schema requirements

[Detailed debugging →](docs/AI_INTEGRATION_GUIDE.md#tools-not-working)

---

#### Windows-Specific Issues

**Symptoms:** Server fails to start on Windows

**Quick Fixes:**
1. Run automated diagnostics: `.\setup-windows.ps1`
2. Verify Python path uses double backslashes: `C:\\Program Files\\KiCad\\9.0`
3. Check Windows Event Viewer for Node.js errors

[Complete Windows guide →](docs/WINDOWS_TROUBLESHOOTING.md)

---

### Additional Resources

- **[AI Integration Guide](docs/AI_INTEGRATION_GUIDE.md)** - Comprehensive troubleshooting procedures
- **[Basic Functionality Tests](docs/AI_INTEGRATION_GUIDE.md#basic-functionality-tests)** - Verify your setup
- **[Known Issues](docs/KNOWN_ISSUES.md)** - Current limitations and workarounds

### Getting Help

1. **Check existing resources:**
   - [GitHub Issues](https://github.com/mixelpixx/KiCAD-MCP-Server/issues)
   - [AI Integration Guide](docs/AI_INTEGRATION_GUIDE.md)
   - Server logs: `~/.kicad-mcp/logs/kicad_interface.log`

2. **Open a new issue with:**
   - Operating system and version
   - KiCAD version: `python3 -c "import pcbnew; print(pcbnew.GetBuildVersion())"`
   - Node.js version: `node --version`
   - Full error message and stack trace
   - Relevant log excerpts

## Project Status

**Current Version:** 2.1.0-alpha

**Production-Ready Features:**
- Project creation and management
- Board outline and sizing
- Layer management
- Component placement (with library integration needed)
- Mounting holes and text annotations
- Design rule checking
- Export to Gerber, PDF, SVG, 3D
- Schematic creation and editing
- UI auto-launch
- Full MCP protocol compliance

**In Development:**
- JLCPCB parts integration
- Digikey API integration
- Advanced routing algorithms
- Real-time UI synchronization via IPC API
- Smart BOM management

**Roadmap:**
- AI-assisted component selection
- Design pattern library (Arduino shields, RPi HATs)
- Interactive design review mode
- Automated documentation generation
- Multi-board projects

See [ROADMAP.md](docs/ROADMAP.md) for detailed development timeline.

## Contributing

Contributions are welcome! Please follow these guidelines:

1. **Report Bugs:** Open an issue with reproduction steps
2. **Suggest Features:** Describe use case and expected behavior
3. **Submit Pull Requests:**
   - Fork the repository
   - Create a feature branch
   - Follow existing code style
   - Add tests for new functionality
   - Update documentation
   - Submit PR with clear description

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

## Documentation Index

This README serves as your starting point. Detailed documentation is organized by topic:

### Core Documentation
| Document | Description |
|----------|-------------|
| **[AI Integration Guide](docs/AI_INTEGRATION_GUIDE.md)** | **Complete guide to AI integration** - modes, security, configuration, testing |
| [Client Configuration](docs/CLIENT_CONFIGURATION.md) | MCP client setup for Claude Desktop, Cline, Claude Code |
| [Platform Guide](docs/PLATFORM_GUIDE.md) | Platform-specific installation and configuration |
| [Windows Troubleshooting](docs/WINDOWS_TROUBLESHOOTING.md) | Comprehensive Windows setup and debugging |

### Reference
| Document | Description |
|----------|-------------|
| [Contributing](CONTRIBUTING.md) | Contribution guidelines and code style |
| [Roadmap](docs/ROADMAP.md) | Future development plans |
| [Known Issues](docs/KNOWN_ISSUES.md) | Current limitations and workarounds |
| [Status Summary](docs/STATUS_SUMMARY.md) | Current project status |

### Development Logs
| Document | Description |
|----------|-------------|
| [Build & Test Session](docs/BUILD_AND_TEST_SESSION.md) | Build system and testing documentation |
| [Library Integration](docs/LIBRARY_INTEGRATION.md) | Component library integration plans |
| [IPC API Migration](docs/IPC_API_MIGRATION_PLAN.md) | Real-time UI synchronization plans |
| [JLCPCB Integration](docs/JLCPCB_INTEGRATION_PLAN.md) | Parts database integration plans |

### Configuration Files
| File | Description |
|------|-------------|
| `config/mcp-server-config.json` | Main server configuration with schema |
| `.env` (create from template) | Environment variables for deployment |
| Platform-specific configs | Example configs for Linux, Windows, macOS |

---

## Acknowledgments

- Built on the [Model Context Protocol](https://modelcontextprotocol.io/) by Anthropic
- Powered by [KiCAD](https://www.kicad.org/) open-source PCB design software
- Uses [kicad-skip](https://github.com/kicad-skip) for schematic manipulation

## Citation

If you use this project in your research or publication, please cite:

```bibtex
@software{kicad_mcp_server,
  title = {KiCAD MCP Server: AI-Assisted PCB Design},
  author = {mixelpixx},
  year = {2025},
  url = {https://github.com/mixelpixx/KiCAD-MCP-Server},
  version = {2.1.0-alpha}
}
```
