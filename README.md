# 🚀 NotebookLM MCP

**Professional MCP server for Google NotebookLM automation • Available on PyPI • Production Ready**

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)[![DeepWiki](https://img.shields.io/badge/DeepWiki-View%20Docs-blueviolet?logo=gitbook)](https://deepwiki.com/khengyun/notebooklm-mcp)[![Tests](https://github.com/khengyun/notebooklm-mcp/actions/workflows/test.yml/badge.svg)](https://github.com/khengyun/notebooklm-mcp/actions)[![codecov](https://codecov.io/gh/khengyun/notebooklm-mcp/branch/main/graph/badge.svg)](https://codecov.io/gh/khengyun/notebooklm-mcp)[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## ✨ Key Features

- **🔥 FastMCP v2**: Modern decorator-based MCP framework
- **⚡ UV Python Manager**: Lightning-fast dependency management
- **🚀 Multiple Transports**: STDIO, HTTP, SSE support
- **🎯 Type Safety**: Full Pydantic validation
- **🔒 Persistent Auth**: Automatic Google session management
- **📊 Rich CLI**: Beautiful terminal interface with Taskfile automation
- **🐳 Production Ready**: Docker support with monitoring

## 🏃‍♂️ Quick Start

### 🎯 For End Users (Recommended)

```bash
# Install UV (modern Python package manager)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install NotebookLM MCP from PyPI
uv add notebooklm-mcp

# Initialize with your NotebookLM URL
uv run notebooklm-mcp init https://notebooklm.google.com/notebook/YOUR_NOTEBOOK_ID
```

**What happens after `init`:**

- ✅ Creates `notebooklm-config.json` with your settings
- ✅ Creates Chrome profile folder for persistent authentication
- ✅ Opens browser for one-time Google login (if needed)
- ✅ Saves session for future headless operation

```bash
# Start server (STDIO for MCP clients)
uv run notebooklm-mcp --config notebooklm-config.json server

# Start server in headless mode (after initial authentication)
uv run notebooklm-mcp --config notebooklm-config.json server --headless

# Start HTTP server for web testing
uv run notebooklm-mcp --config notebooklm-config.json server --transport http --port 8001

# Interactive chat mode
uv run notebooklm-mcp --config notebooklm-config.json chat --message "Who are you?"
```

### 🎯 Headless Mode (For MCP Clients like Cursor)

To run in headless mode (no visible browser window), you must first authenticate:

1. **First time setup** - Run `init` to authenticate in a visible browser:

   ```bash
   cd /path/to/notebooklm-mcp  # or wherever you want to store config
   uv run notebooklm-mcp init https://notebooklm.google.com/notebook/YOUR_NOTEBOOK_ID
   ```

   Complete the Google login in the browser window that opens.

2. **Then use `--headless` flag** - Once authenticated, the session is saved and you can run headlessly:

   ```bash
   uv run notebooklm-mcp --config notebooklm-config.json server --headless
   ```

> **⚠️ Important:** You must authenticate with a visible browser first. Running `--headless` before completing authentication will fail.

### 🖥️ Cursor MCP Integration

To use NotebookLM MCP with Cursor IDE:

1. **Complete initial setup** (one-time):

   ```bash
   cd ~/path/to/notebooklm-mcp
   uv run notebooklm-mcp init https://notebooklm.google.com/notebook/YOUR_NOTEBOOK_ID
   ```

2. **Add to your Cursor MCP config** (`~/.cursor/mcp.json` or workspace `.cursor/mcp.json`):

   ```json
   {
     "mcpServers": {
       "notebooklm": {
         "command": "uv",
         "args": [
           "run",
           "--directory", "/absolute/path/to/notebooklm-mcp",
           "notebooklm-mcp",
           "--config", "/absolute/path/to/notebooklm-mcp/notebooklm-config.json",
           "server",
           "--headless"
         ]
       }
     }
   }
   ```

3. **Restart Cursor** to load the MCP server.

4. **Use in chat** - Ask Cursor to use the NotebookLM tools (e.g., "Use notebooklm to ask about X").

> **💡 Tip:** The browser runs headlessly in the background. When you disable/remove the MCP server from Cursor's config and restart, the browser process is automatically cleaned up.

### 👨‍💻 For Developers

If you're contributing to this project, check out our [Taskfile](./Taskfile.yml) for enhanced developer experience:

```bash
git clone https://github.com/khengyun/notebooklm-mcp.git
cd notebooklm-mcp

# Complete setup with development tools
task setup

# Show all available development tasks
task --list
```

## 🔧 Alternative Installation

If you prefer pip over UV:

```bash
# Install with pip
pip install notebooklm-mcp

# Initialize
notebooklm-mcp init https://notebooklm.google.com/notebook/YOUR_NOTEBOOK_ID

# Start server
notebooklm-mcp --config notebooklm-config.json server
```

## � Project Structure After Init

After running `init`, your working directory will contain:

```text
your-project/
├── notebooklm-config.json          # Configuration file
├── chrome_profile_notebooklm/      # Browser profile (persistent auth)
│   ├── Default/                    # Chrome profile data
│   ├── SingletonSocket             # Session files
│   └── ...                         # Other Chrome data
└── your-other-files
```

**Key files:**

- **`notebooklm-config.json`**: Contains notebook ID, server settings, auth configuration
- **`chrome_profile_notebooklm/`**: Stores Google authentication session (enables headless operation)

## �🛠️ Available Tools

| Tool | Description | Parameters |
|------|-------------|------------|
| `healthcheck` | Server health status | None |
| `send_chat_message` | Send message to NotebookLM | `message: str`, `wait_for_response: bool` |
| `get_chat_response` | Get response with timeout | `timeout: int` |
| `chat_with_notebook` | Complete interaction | `message: str`, `notebook_id?: str` |
| `navigate_to_notebook` | Switch notebooks | `notebook_id: str` |
| `get_default_notebook` | Current notebook | None |
| `set_default_notebook` | Set default | `notebook_id: str` |
| `get_quick_response` | Instant response | None |

## 👨‍💻 Developer Workflow

For contributors and advanced users who want enhanced productivity, we provide a comprehensive Taskfile with 20+ automation tasks:

```bash
# 📦 Dependency Management
task deps-add -- requests       # Add dependency
task deps-add-dev -- pytest     # Add dev dependency
task deps-remove -- requests    # Remove dependency
task deps-list                  # List dependencies
task deps-update                # Update all dependencies

# 🧪 Testing & Quality
task test                       # Run all tests
task test-quick                 # Quick validation test
task test-coverage              # Coverage analysis
task enforce-test               # MANDATORY after function changes
task lint                       # Run all linting
task format                     # Format code (Black + isort + Ruff)

# 🏗️ Build & Release
task build                      # Build package
task clean                      # Clean artifacts

# 🚀 Server Commands
task server-stdio              # STDIO server
task server-http               # HTTP server
task server-sse                # SSE server

# Show all available tasks
task --list
```

> **💡 Pro Tip**: Install [Task](https://taskfile.dev/) for the best developer experience: `go install github.com/go-task/task/v3/cmd/task@latest`

## 🌐 Transport Options

### STDIO (Default)

```bash
task server-stdio
# For: LangGraph, CrewAI, AutoGen
```

### HTTP

```bash
task server-http  
# Access: http://localhost:8001/mcp
# For: Web testing, REST APIs
```

### SSE

```bash
task server-sse
# Access: http://localhost:8002/
# For: Real-time streaming
```

## 🧪 Testing & Development

### HTTP Client Testing

```python
from fastmcp import Client
from fastmcp.client.transports import StreamableHttpTransport

transport = StreamableHttpTransport(url="http://localhost:8001/mcp")
async with Client(transport) as client:
    tools = await client.list_tools()
    result = await client.call_tool("healthcheck", {})
```

### Command Line Testing

```bash
# Test with curl
curl -X POST http://localhost:8001/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc": "2.0", "id": 1, "method": "tools/list", "params": {}}'
```

## 📊 Client Integration

### LangGraph

```python
from fastmcp import Client
from fastmcp.client.transports import StreamableHttpTransport

# HTTP transport
transport = StreamableHttpTransport(url="http://localhost:8001/mcp")
client = Client(transport)
tools = await client.list_tools()
```

### CrewAI

```python
from crewai_tools import BaseTool
from fastmcp import Client

class NotebookLMTool(BaseTool):
    name = "notebooklm"
    description = "Chat with NotebookLM"
    
    async def _arun(self, message: str):
        client = Client("http://localhost:8001/mcp")
        result = await client.call_tool("chat_with_notebook", {"message": message})
        return result
```

## 🔒 Authentication

### Automatic Setup

```bash
# First time - opens browser for login (REQUIRED before headless mode)
notebooklm-mcp init https://notebooklm.google.com/notebook/abc123

# Subsequent runs - uses saved session (GUI mode)
notebooklm-mcp --config notebooklm-config.json server

# Subsequent runs - uses saved session (headless mode - no browser window)
notebooklm-mcp --config notebooklm-config.json server --headless
```

### Headless Mode Requirements

**Important:** Headless mode requires a pre-authenticated session. The workflow is:

1. Run `init` command first (opens visible browser for Google login)
2. Complete authentication in the browser
3. Then you can use `--headless` flag for subsequent runs

If you try to run `--headless` without first authenticating, the server will fail because Google requires interactive login.

### Manual Setup

```bash
# Interactive browser login
notebooklm-mcp --config notebooklm-config.json server

# Check connection
notebooklm-mcp --config notebooklm-config.json test --notebook YOUR_NOTEBOOK_ID
```

### Browser Cleanup

When running in headless mode, a Chrome browser runs in the background. The browser is automatically cleaned up when:

- The server receives a shutdown signal (Ctrl+C or SIGTERM)
- The MCP client (e.g., Cursor) terminates the server process

If you notice orphaned Chrome processes, you can manually clean them up:

```bash
# macOS/Linux
pkill -f "chrome.*notebooklm-mcp"

# Or find and kill specific PIDs
ps aux | grep chrome | grep notebooklm
```

### One-Time Setup: Disable Profile Picker

On first run, Chrome may show a "Choose a profile" page. To prevent this on subsequent runs:

1. When the profile picker appears, look for the **"Show on startup"** checkbox at the bottom
2. **Uncheck** this checkbox
3. Select your profile to continue

This setting is saved in the Chrome profile and only needs to be done once.

## 🛡️ Security Best Practices

### Profile Storage

The Chrome profile contains your **full Google session credentials**. This includes cookies and tokens that grant access to your entire Google account (Gmail, Drive, etc.) - not just NotebookLM.

**⚠️ Never commit the profile directory to version control.**

By default, profiles are now stored in platform-appropriate secure locations **outside** your repository:

| OS | Default Profile Location |
|----|-------------------------|
| macOS | `~/Library/Application Support/notebooklm-mcp/profile` |
| Linux | `~/.config/notebooklm-mcp/profile` |
| Windows | `%APPDATA%\notebooklm-mcp\profile` |

Create this directory according to your operating system and (ideally) set the permissions to 700.

To use a custom location:

```bash
notebooklm-mcp init <URL> --profile-dir /path/to/secure/location
```

### For Teams

Choose the approach that fits your security requirements:

| Approach | Best For | How It Works |
|----------|----------|--------------|
| **Individual Profiles** | Most teams | Each dev authenticates with their own Google account. Notebooks are shared via Google's native sharing. |
| **Service Account** | Automation, CI/CD | Create a dedicated Google account for automation. Share the profile securely (not in repos). |

**Never share profiles authenticated with personal Google accounts.**

### Security Checklist

- [ ] Profile directory is **outside** any git repository
- [ ] `chrome_profile_*/` is in your `.gitignore`
- [ ] `notebooklm-config.json` is in your `.gitignore` (use `.example.json` as template)
- [ ] Using pinned package versions in production
- [ ] For teams: using individual profiles OR a dedicated service account

### Environment Variables

For CI/CD or containerized deployments, use environment variables to avoid storing paths in config files:

```bash
export NOTEBOOKLM_PROFILE_DIR="/secure/path/to/profile"
export NOTEBOOKLM_NOTEBOOK_ID="your-notebook-id"
```

## 🐳 Docker Deployment

### Quick Start

```bash
# 1. Generate config + Chrome profile with guided login
uv run notebooklm-mcp init https://notebooklm.google.com/notebook/YOUR_NOTEBOOK_ID
# If you installed via pip, run: notebooklm-mcp init https://notebooklm.google.com/notebook/YOUR_NOTEBOOK_ID

# 2. Build the container image
docker build -t notebooklm-mcp .

# 3. Run the server with your mounted config/profile
docker run -d \
  --name notebooklm-mcp \
  --restart unless-stopped \
  -v $(pwd)/notebooklm-config.json:/app/notebooklm-config.json:ro \
  -v $(pwd)/chrome_profile_notebooklm:/app/chrome_profile_notebooklm \
  notebooklm-mcp:latest
```

### With Compose

```yaml
version: '3.8'
services:
  notebooklm-mcp:
    image: notebooklm-mcp:latest
    build: .
    restart: unless-stopped
    volumes:
      - ./notebooklm-config.json:/app/notebooklm-config.json:ro
      - ./chrome_profile_notebooklm:/app/chrome_profile_notebooklm
```

Start the stack with `docker compose up -d` after running the `init` command once so
that `notebooklm-config.json` and `chrome_profile_notebooklm/` exist. The server
runs in STDIO mode by default; uncomment the HTTP/SSE ports in
`docker-compose.yml` if your client requires them.

## ⚙️ Configuration

### Config File (`notebooklm-config.json`)

```json
{
  "default_notebook_id": "your-notebook-id",
  "headless": true,
  "timeout": 30,
  "auth": {
    "profile_dir": "./chrome_profile_notebooklm"
  },
  "debug": false
}
```

### Environment Variables

```bash
export NOTEBOOKLM_NOTEBOOK_ID="your-notebook-id"
export NOTEBOOKLM_HEADLESS=true
export NOTEBOOKLM_DEBUG=false
```

## 🚀 Performance

### FastMCP v2 Benefits

- **⚡ 5x faster** tool registration with decorators
- **📋 Auto-generated schemas** from Python type hints  
- **🔒 Built-in validation** with Pydantic
- **🧪 Better testing** and debugging capabilities
- **📊 Type safety** throughout the stack

### Benchmarks

| Feature | Traditional MCP | FastMCP v2 |
|---------|----------------|------------|
| Tool registration | Manual schema | Auto-generated |
| Type validation | Manual | Automatic |
| Error handling | Basic | Enhanced |
| Development speed | Standard | 5x faster |
| HTTP support | Limited | Full |

## 🛠️ Development

### Setup

```bash
git clone https://github.com/khengyun/notebooklm-mcp
cd notebooklm-mcp

# With UV (recommended)
uv sync --all-groups

# Or with pip
pip install -e ".[dev]"
```

### Testing

```bash
# Run tests with UV
uv run pytest

# With coverage
uv run pytest --cov=notebooklm_mcp

# Integration tests  
uv run pytest tests/test_integration.py

# Or use Taskfile for development
task test
task test-coverage
```

### Code Quality

```bash
# Format code with UV
uv run black src/ tests/
uv run ruff check src/ tests/

# Type checking
uv run mypy src/

# Or use Taskfile shortcuts
task format
task lint
```

## 📚 Documentation

- **[Quick Setup Guide](docs/quick-setup-guide.md)** - Get started in 2 minutes
- **[HTTP Server Guide](docs/http-server-guide.md)** - Web testing & integration
- **[FastMCP v2 Guide](docs/fastmcp-v2-guide.md)** - Modern MCP features
- **[Docker Deployment](docs/docker-deployment.md)** - Production setup
- **[API Reference](docs/api-reference.md)** - Complete tool documentation

## 🔗 Related Projects

- **[FastMCP](https://github.com/jlowin/fastmcp)** - Modern MCP framework
- **[MCP Specification](https://spec.modelcontextprotocol.io/)** - Official MCP spec
- **[NotebookLM](https://notebooklm.google.com/)** - Google's AI notebook

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🆘 Support

- **Issues**: [GitHub Issues](https://github.com/khengyun/notebooklm-mcp/issues)
- **Discussions**: [GitHub Discussions](https://github.com/khengyun/notebooklm-mcp/discussions)
- **Documentation**: [Read the Docs](https://notebooklm-mcp.readthedocs.io)

---

**Built with ❤️ using FastMCP v2 - Modern MCP development made simple!**
