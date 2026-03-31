# MCP Server Creation & Publishing

*Created: 2025-12-12*

End-to-end process for creating and distributing an MCP (Model Context Protocol) server.

---

## Overview

MCP servers are tools that AI assistants (Claude, etc.) can use. They expose functions ("tools") that the AI can call.

**Example:** A Spotify MCP lets Claude search songs, create playlists, manage libraries.

---

## Phase 1: Build the Server

### Python (using fastmcp)

```bash
# Create project
mkdir my-mcp-server && cd my-mcp-server
python -m venv venv
source venv/bin/activate
pip install fastmcp
```

```python
# server.py
from fastmcp import FastMCP

mcp = FastMCP("my-server")

@mcp.tool()
def my_tool(param: str) -> str:
    """Description shown to AI."""
    return f"Result: {param}"

if __name__ == "__main__":
    mcp.run()
```

### Test Locally

Add to `~/.claude/settings.local.json`:

```json
{
  "mcpServers": {
    "my-server": {
      "command": "/path/to/venv/bin/python",
      "args": ["/path/to/server.py"]
    }
  }
}
```

Restart Claude Code and test the tools.

---

## Phase 2: Package for Distribution

### Project Structure

```
my-mcp-server/
├── my_mcp_server/              # Package (underscores)
│   ├── __init__.py
│   ├── server.py               # Main server
│   ├── tools/                  # Tool implementations
│   │   └── __init__.py
│   └── utils/
│       └── __init__.py
├── pyproject.toml
├── README.md
├── LICENSE
├── setup_auth.py               # If auth needed
├── .env.example
└── .gitignore
```

### pyproject.toml

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "my-mcp-server"
version = "0.1.0"
description = "MCP server for X"
readme = "README.md"
license = "MIT"
requires-python = ">=3.10"
dependencies = [
    "fastmcp>=0.1.0",
    # your deps
]

[project.scripts]
my-mcp-server = "my_mcp_server.server:main"

[tool.hatch.build.targets.wheel]
packages = ["my_mcp_server"]
```

---

## Phase 3: Publish to PyPI

See skill: `workflow/pypi-publishing.md`

```bash
pip install build twine
python -m build
python -m twine upload dist/*
```

---

## Phase 4: List on Directories

### 1. mcp.so (Community Directory)

- Go to [mcp.so](https://mcp.so)
- Click "Submit Server"
- Enter GitHub repo URL
- Auto-populates from README

### 2. awesome-mcp-servers

- Fork [punkpeye/awesome-mcp-servers](https://github.com/punkpeye/awesome-mcp-servers)
- Add entry to README.md in appropriate category
- Submit PR

### 3. Official MCP Registry (Anthropic)

**Requirements:**
- Published to PyPI (or npm/NuGet)
- Verification tag in README

**Steps:**

1. Add to README (hidden comment):
   ```markdown
   <!-- mcp-name: io.github.yourusername/your-server -->
   ```

2. Republish to PyPI (so tag is visible)

3. Install mcp-publisher:
   ```bash
   curl -L "https://github.com/modelcontextprotocol/registry/releases/latest/download/mcp-publisher_$(uname -s | tr '[:upper:]' '[:lower:]')_$(uname -m | sed 's/x86_64/amd64/;s/aarch64/arm64/').tar.gz" | tar xz mcp-publisher && sudo mv mcp-publisher /usr/local/bin/
   ```

4. Create `server.json`:
   ```bash
   mcp-publisher init
   ```

5. Edit `server.json`:
   ```json
   {
     "$schema": "https://static.modelcontextprotocol.io/schemas/2025-12-11/server.schema.json",
     "name": "io.github.yourusername/your-server",
     "description": "What it does",
     "repository": {
       "url": "https://github.com/yourusername/your-server",
       "source": "github"
     },
     "version": "0.1.0",
     "packages": [
       {
         "registryType": "pypi",
         "identifier": "your-package-name",
         "version": "0.1.0",
         "transport": { "type": "stdio" }
       }
     ]
   }
   ```

6. Login & publish:
   ```bash
   mcp-publisher login github
   mcp-publisher publish
   ```

---

## MCP Directory Status Tracking

Track where your MCP is listed in README:

```markdown
## Listed On

| Directory | Link | Status |
|-----------|------|--------|
| PyPI | [link](https://pypi.org/project/X) | Live |
| mcp.so | [link](https://mcp.so/server/X) | Live |
| awesome-mcp-servers | [PR #123](link) | Pending |
| MCP Registry | [link] | Pending |
```

---

## Lessons Learned (2025-12)

1. **Glama.ai** - Has claim process via `glama.json` but frequently errors. Low priority.
2. **PyPI for MCP** - Even if users clone from GitHub (for local auth), PyPI needed for official registry.
3. **Version tag in README** - Must republish to PyPI after adding `mcp-name` tag.
4. **Package naming** - PyPI uses hyphens (`my-package`), Python imports use underscores (`my_package`).

---

## Links

- [MCP Spec](https://modelcontextprotocol.io)
- [FastMCP](https://github.com/jlowin/fastmcp)
- [MCP Registry Quickstart](https://github.com/modelcontextprotocol/registry/blob/main/docs/modelcontextprotocol-io/quickstart.mdx)
- [mcp.so](https://mcp.so)
