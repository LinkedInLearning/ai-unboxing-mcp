# Creating a baseline MCP Server using the MCP Python SDK

Follow this tutorial to create a simple MCP server with an example resource, tool, and prompt.

For more baseline examples see the [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk)

## Requirements

- [uv](https://docs.astral.sh/uv/):
   - macOS via Homebrew:
   ```bash
   brew install uv
   ```
   - Windows via WinGet:
   ```bash
   winget install --id=astral-sh.uv  -e
   ```
- [Visual Studio Code](https://code.visualstudio.com/) or another code editor
- For testing in Claude:
  - [Claude.ai account](https://claude.ai) (MCP support is available for all account types)
  - [Claude Desktop app](https://claude.ai/download), available for macOS and Windows

## Build an MCP Server from Scratch

### Step 1: Create a UV-managed project

```bash
uv init simple-mcp-server
cd simple-mcp-server
```

Add the MCP Python SDK

```bash
uv add "mcp[cli]"
```

Start the virtual environment

In terminal:
```bash
source .venv/Scripts/activate
```

NOTE: To stop the virtual environment:
```bash
deactivate
```

Set the VS Code python environment

1. Open the Command Palette Shift + CMD/CTRL + P
2. Select "Python: Set Project Environment
3. Choose `simple-mcp-server` venv

###  Step 2: Set up the main file

Rename `main.py` to `server.py` and replace the content with the following:

```python
from mcp.server.fastmcp import FastMCP

# Create an MCP server
mcp = FastMCP("Simple MCP Server")

def main():
    mcp.run()

if __name__ == "__main__":
    main()
```

### Step 3: Register the server

In `pyproject.toml`:

```json
[project.scripts]
simple-mcp-server = "server:main"
```

Add a blank `__init__.py` file.

### Step 4: Add Tools, Resources, and Prompts

#### Tools

Tools are used by the LLM to perform actions that have side-effects. 

Official spec: https://modelcontextprotocol.io/docs/concepts/tools 
SDK docs: https://github.com/modelcontextprotocol/python-sdk?tab=readme-ov-file#tools

Add a Tool:

```python
# Add an addition tool
@mcp.tool()
def add(a: int, b: int) -> int:
    """Add two numbers"""
    return a + b
```

#### Resources

Official spec: https://modelcontextprotocol.io/docs/concepts/resources 
SDK docs: https://github.com/modelcontextprotocol/python-sdk?tab=readme-ov-file#resources

Add a Resource:

```python
# Add a dynamic greeting resource
@mcp.resource("greeting://{name}")
def get_greeting(name: str) -> str:
    """Get a personalized greeting"""
    return f"Hello, {name}!"
```

#### Prompts

Official spec: https://modelcontextprotocol.io/docs/concepts/prompts 
SDK docs: https://github.com/modelcontextprotocol/python-sdk?tab=readme-ov-file#prompts

Add a Prompt:

```python
@mcp.prompt()
def greet_user(name: str, style: str = "friendly") -> str:
    """Generate a greeting prompt"""
    styles = {
        "friendly": "Please write a warm, friendly greeting",
        "formal": "Please write a formal, professional greeting",
        "casual": "Please write a casual, relaxed greeting",
    }

    return f"{styles.get(style, styles['friendly'])} for someone named {name}."

```

### 5. Run MCP server in dev mode with the [MCP Inspector](https://github.com/modelcontextprotocol/inspector)

```bash
uv run mcp dev server.py
```

## Run MCP server in VS Code

1. Open the Command Palette Shift + CMD/CTRL + P

2. Select "MCP: Open User Configuration". This opens `mcp.json`

3. In `mcp.json`:

  ```json
  {
    "servers": {
      "weather-server": {
        "type": "stdio",
        "command": "uv",
        "args": [
          "run",
          "--directory",
          "/absolute/path/to/simple-server/simple-mcp-server",
          "simple-mcp-server"
        ]
      }
    },
    "inputs": []
  }
  ```

## Run MCP server in Claude Desktop

### Automatic install

In terminal:
```bash
uv run mcp install server.py
```

### Manual install

1. Open `claude_desktop_config.js` in an editor:
 
  File location:
  - MacOS / Linux `~/Library/Application/Support/Claude/claude_desktop_config.json`
  - Windows `AppData\Claude\claude_desktop_config.json`

2. Find the full path to `uv`:
  
  - MacOS / Linux:
  ```bash
  which uv
  ```
  - Windows:
  ```bash
  where uv
  ```

2. In `claude_desktop_config.js`

  ```json
  {
    "mcpServers": {
      "Open-Meteo Weather": {
        "command": "/opt/homebrew/bin/uv",
        "args": [
          "run",
          "--with",
          "mcp[cli]",
          "mcp",
          "run",
          "/absolute/path/to/weather-server/mcp_open_meteo/server.py"
        ]
      }
    }
   }
   ```
