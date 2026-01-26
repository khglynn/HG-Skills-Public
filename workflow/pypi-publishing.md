# PyPI Publishing for Python Projects

*Created: 2025-12-12*

Publish a Python package to PyPI (Python Package Index) so users can `pip install` it.

---

## When to Use

- Making a Python tool distributable via `pip install package-name`
- Required for MCP Registry (official Anthropic registry requires PyPI/npm/NuGet)
- Want version management (1.0.0, 1.0.1, etc.)

---

## Prerequisites

1. **PyPI Account** - [pypi.org](https://pypi.org) - free signup
2. **API Token** - pypi.org → Account settings → API tokens → Add API token
3. **Python tools** - `pip install build twine`

---

## Project Structure

```
your-package/
├── your_package_name/        # Package folder (underscore for imports)
│   ├── __init__.py
│   └── main.py
├── pyproject.toml            # Package metadata (required)
├── README.md                 # Becomes PyPI description
├── LICENSE                   # Required for distribution
└── .gitignore                # Include dist/, *.egg-info
```

**Important:** Package folder name uses underscores (`my_package`), but PyPI name uses hyphens (`my-package`).

---

## pyproject.toml Template

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "your-package-name"
version = "0.1.0"
description = "Short description"
readme = "README.md"
license = "MIT"
authors = [
    { name = "Your Name", email = "you@example.com" }
]
keywords = ["keyword1", "keyword2"]
classifiers = [
    "Development Status :: 4 - Beta",
    "License :: OSI Approved :: MIT License",
    "Programming Language :: Python :: 3",
    "Programming Language :: Python :: 3.10",
    "Programming Language :: Python :: 3.11",
    "Programming Language :: Python :: 3.12",
]
requires-python = ">=3.10"
dependencies = [
    "some-dependency>=1.0.0",
]

[project.urls]
Homepage = "https://github.com/you/your-package"
Repository = "https://github.com/you/your-package"

[project.scripts]
your-cli-command = "your_package_name.main:main"

[tool.hatch.build.targets.wheel]
packages = ["your_package_name"]
```

---

## Publishing Steps

### 1. Build

```bash
# Clean old builds
rm -rf dist/

# Build wheel and source dist
python -m build
```

Creates `dist/` with `.whl` and `.tar.gz` files.

### 2. Upload

```bash
# Upload to PyPI
python -m twine upload dist/*

# When prompted:
# Username: __token__
# Password: pypi-AgEIcHlwaS5vcmc... (your API token)
```

### 3. Verify

Visit `https://pypi.org/project/your-package-name/` to confirm.

---

## Version Updates

1. Edit `version` in `pyproject.toml` (e.g., `0.1.0` → `0.1.1`)
2. Clean and rebuild: `rm -rf dist/ && python -m build`
3. Upload: `python -m twine upload dist/*`

**Note:** Can't overwrite versions. Each version is immutable.

---

## Common Issues

| Issue | Fix |
|-------|-----|
| `*.json` in `.gitignore` blocking files | Add exceptions: `!filename.json` |
| Package name taken | Check pypi.org first, pick unique name |
| Import errors after install | Ensure package folder matches `[tool.hatch.build.targets.wheel]` |
| README not showing on PyPI | Ensure `readme = "README.md"` in pyproject.toml |

---

## For MCP Registry

If publishing to the official MCP Registry (registry.modelcontextprotocol.io), add this hidden comment to README:

```markdown
<!-- mcp-name: io.github.yourusername/your-server-name -->
```

Then republish to PyPI so the updated README is visible.

---

## Links

- [PyPI](https://pypi.org)
- [Python Packaging Guide](https://packaging.python.org/en/latest/tutorials/packaging-projects/)
- [Hatchling Docs](https://hatch.pypa.io/latest/)
