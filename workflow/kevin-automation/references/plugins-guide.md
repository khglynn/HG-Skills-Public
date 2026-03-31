---
title: Plugins and MCPs
description: Understanding Claude Code plugins, MCPs, and their context costs
---

# Plugins and MCPs

**Updated:** 2026-01-28

When working with Claude Code extensions, understand the difference between MCPs and plugins to manage context effectively.

## Core Concepts

### MCPs (Model Context Protocol)

MCPs are **running services** that give Claude tools to interact with external systems.

| Aspect | Details |
|--------|---------|
| What they provide | Tools (actions Claude can take) |
| Context cost | ~500-10k tokens depending on # of tools |
| Examples | `github`, `cloudflare`, `playwright`, `slack` |
| Config location | `~/.claude.json` or project `.mcp.json` |

### Plugins

Plugins are **packages** that can contain any combination of:

| Component | Purpose | Context cost |
|-----------|---------|--------------|
| **Skills** | On-demand knowledge/guidance | ~0 until invoked |
| **Commands** | Slash commands (`/command-name`) | ~0 until invoked |
| **Hooks** | Automation on events | Small (injected at session start) |
| **MCPs** | Tool servers | Same as standalone MCP |

## Plugin Types

### 1. MCP Installer Plugins
Just install/configure an MCP. No added knowledge.

**Examples:** `playwright`, `slack`, `github`, `context7`

**Structure:**
```
plugin/
├── .claude-plugin/plugin.json
└── .mcp.json              # Just MCP config
```

**Use when:** You want easy MCP setup without manual configuration.

### 2. Knowledge Plugins
Provide skills (on-demand guidance) and possibly hooks.

**Example:** `superpowers` (14 skills for workflows like debugging, TDD, planning)

**Structure:**
```
plugin/
├── .claude-plugin/
├── skills/                # On-demand knowledge
│   ├── debugging/SKILL.md
│   └── planning/SKILL.md
└── hooks/                 # Session automation
```

**Use when:** You want Claude to know HOW to do something.

### 3. Command Plugins
Provide slash commands with embedded workflows.

**Examples:** `code-review`, `feature-dev`

**Structure:**
```
plugin/
├── .claude-plugin/
├── commands/              # Slash commands
│   └── review/COMMAND.md
└── agents/                # Agent definitions
```

**Use when:** You want repeatable workflows via `/command`.

## Context Management

### Tool Search (Automatic)

Claude Code auto-enables lazy loading when MCP tools exceed 10% of context.

- Tools load on-demand, not upfront
- Can reduce 36k tokens → ~8k tokens
- Enabled by default (no action needed)

### Checking Context Usage

```bash
/context   # Full breakdown
/mcp       # MCP-specific usage with tool counts
```

### Avoiding Duplicates

Plugins can install MCPs you already have configured. Check for overlaps:

- `mcp__github__*` (your config) vs `mcp__plugin_github_github__*` (plugin)
- Same tools, double context cost

**Fix:** Disable either your manual MCP or the plugin-installed one.

## Decision Tree

### Need Claude to DO something? → MCP
- Call APIs, query databases, interact with services
- Tools that take actions

### Need Claude to KNOW something? → Knowledge Plugin
- Workflows, best practices, patterns
- Guidance on how to approach tasks

### Want easy MCP setup? → MCP Installer Plugin
- Same as manual MCP, just easier to install
- No added knowledge

### Want repeatable workflows? → Command Plugin
- Slash commands that orchestrate multi-step work
- Often spawn agents

## Hooks Reference

Hooks run automatically when their event fires (if plugin is enabled).

| Hook | When it fires | Common use |
|------|---------------|------------|
| `SessionStart` | Claude Code starts/resumes | Inject context, set modes |
| `PreToolUse` | Before any tool runs | Block dangerous commands |
| `PostToolUse` | After tool succeeds | Logging, follow-up |
| `PostToolUseFailure` | After tool fails | Error handling |
| `UserPromptSubmit` | User sends message | Preprocessing |

## Plugin Inspection Commands

```bash
# List installed plugins
ls ~/.claude/plugins/cache/claude-plugins-official/

# Check plugin contents
ls ~/.claude/plugins/cache/claude-plugins-official/<name>/*/

# Has skills? (knowledge plugin)
ls ~/.claude/plugins/cache/claude-plugins-official/<name>/*/skills/

# Has MCP? (MCP installer)
cat ~/.claude/plugins/cache/claude-plugins-official/<name>/*/.mcp.json

# Estimate context cost (bytes ÷ 4 ≈ tokens)
wc -c <file.md>
```

## MCP Buckets in UI

The `/mcp` view shows three sections - these are just **organization by source**, not functional differences:

| Bucket | Source |
|--------|--------|
| **User MCPs** | Your `~/.claude.json` config |
| **claude.ai** | Anthropic's pre-packaged options |
| **Built-in** | Bundled with Claude Code or plugins |

All work identically. Pick one source per service to avoid duplicates.
