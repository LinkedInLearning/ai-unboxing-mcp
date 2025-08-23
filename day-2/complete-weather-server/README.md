# Weather MCP Server Powered by the Open-Meteo API

This MCP server interfaces with the [Open-Meteo API](https://open-meteo.com/en/docs) and offers tools to fetch current weather conditions and weather forecasts for any location worldwide.

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

## Development

### 1. Set up the uv environment

In terminal:
```bash
uv sync
```
### 2. Start the virtual environment

In terminal:
```bash
source .venv/bin/activate
```

NOTE: To stop the virtual environment:
```bash
deactivate
```

### 3. Set the VS Code python environment

* Install the Python extension (if not already installed):
 Go to Extensions (Ctrl+Shift+X), search for "Python", and install the one by Microsoft.

* Open the Command Palette (Ctrl+Shift+P).

* Type and select: Python: Select Interpreter.

* Choose your environment: Select the interpreter that points to your .venv (it may show as .venv\Scripts\python.exe or similar).

### 4. Run MCP server in dev mode with the [MCP Inspector](https://github.com/modelcontextprotocol/inspector)

```bash
uv run mcp dev mcp_open_meteo/server.py
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
          "/absolute/path/to/weather-server",
          "mcp-open-meteo"
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
uv run mcp install mcp_open_meteo/server.py
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

