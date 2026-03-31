# MCP Sync Across Machines and Accounts

**Last Updated:** 2026-01-27

Kevin uses multiple Claude accounts (personal + Tecovas) across multiple machines. MCP configurations need to stay in sync.

## Quick Reference

| Task | Command |
|------|---------|
| Run setup script | `~/DevKev/helper/setup-mcps.sh` (from any directory) |
| List installed MCPs | `claude mcp list` or `/mcp` in session |
| Check script contents | `cat ~/DevKev/helper/setup-mcps.sh` |

---

## How It Works Internally

### MCP Scopes

Claude Code has three MCP scopes:

| Scope | Flag | Config Location | Use Case |
|-------|------|-----------------|----------|
| **local** | `-s local` (default) | Project's `.claude.json` under `projects[path]` | Project-specific MCPs |
| **user** | `-s user` | Root `mcpServers` in `.claude.json` | MCPs that work from any directory |
| **project** | `-s project` | Project's `.mcp.json` file | Shared with team via git |

**The sync script uses `-s user`** so MCPs work from any directory.

### Config File Locations

| Account | Config File |
|---------|-------------|
| Personal | `~/.claude.json` |
| Tecovas | `~/.claude-tecovas/.claude.json` |

The `CLAUDE_CONFIG_DIR` environment variable controls which account's config is used.

### Key Commands

```bash
# Add MCP to user scope (works from any directory)
claude mcp add -s user <name> -- <command>

# Add with environment variable
claude mcp add -s user <name> -e API_KEY="$VALUE" -- <command>

# Add HTTP-based MCP
claude mcp add -s user --transport http <name> <url>

# Remove MCP
claude mcp remove <name>

# Target specific account
CLAUDE_CONFIG_DIR="$HOME/.claude-tecovas" claude mcp add ...
```

---

## How to Add a New MCP

### Step 1: Test it works
```bash
# Test with user scope so it works from any directory
claude mcp add -s user <name> -- <command>
```
Verify with `/mcp` or `claude mcp list`.

### Step 2: Add to setup-mcps.sh

Open `~/DevKev/helper/setup-mcps.sh` and add to the `add_mcp` calls:

```bash
# For personal account (use "default" as first arg)
add_mcp "default" <name> <name> -- <command>

# For Tecovas account (use "$TECOVAS_CONFIG" as first arg)
add_mcp "$TECOVAS_CONFIG" <name> <name> -- <command>
```

**Note:** The name appears twice - once for remove, once for add args.

For MCPs with env vars:
```bash
add_mcp "default" github github -e GITHUB_PERSONAL_ACCESS_TOKEN="$GITHUB_PERSONAL_ACCESS_TOKEN" -- npx -y @modelcontextprotocol/server-github
```

### Step 3: Run and test

```bash
# Run from neutral directory to verify user scope works
cd /tmp && ~/DevKev/helper/setup-mcps.sh
```

### Step 4: Verify (testing checklist)

```bash
# Check output says "user config" not "local config"
# Then verify:
claude mcp list | grep -E "github|firecrawl|context7"
claude-tecovas mcp list | grep -E "github|firecrawl|context7"

# Verify env vars are in config (not empty)
grep -A5 '"firecrawl"' ~/.claude.json
grep -A5 '"firecrawl"' ~/.claude-tecovas/.claude.json
```

### Step 5: Commit and push
```bash
cd ~/DevKev/helper
git add setup-mcps.sh
git commit -m "add <name> mcp"
git push
```

### Step 6: On other machines
```bash
cd ~/DevKev/helper && git pull && ./setup-mcps.sh
```

---

## Session Checks

### At Session START

Compare installed MCPs vs setup-mcps.sh:
```bash
# Check what's installed
claude mcp list

# Check what should be installed
grep "add_mcp" ~/DevKev/helper/setup-mcps.sh
```

If mismatched, run `~/DevKev/helper/setup-mcps.sh`.

### At Session END

If Kevin added any MCPs during the session:
1. Check if they're in setup-mcps.sh
2. If not, offer to add them
3. Remind Kevin to pull + run script on other machines

---

## Common Failure Modes

| Symptom | Cause | Fix |
|---------|-------|-----|
| MCP works in one folder, not another | Added with local scope (default) | Re-add with `-s user` |
| "MCP already exists" but config wrong | Add doesn't update existing | Remove first, then add |
| Script modifies wrong account | Inherited `CLAUDE_CONFIG_DIR` from env | Script now explicitly unsets for personal account |
| Env vars empty in config | Forgot `-e` flag or MCP existed before | Remove and re-add with `-e` flag |

---

## The Problem This Solves

MCP configs are stored per-account, per-machine:
- Personal Mac: `~/.claude.json` + `~/.claude-tecovas/.claude.json`
- Work Mac: same structure
- Future machines: same structure

Adding an MCP manually means N×2 updates (N machines × 2 accounts).

The sync script at `~/DevKev/helper/setup-mcps.sh`:
- Configures all MCPs for both accounts
- Uses `$HOME` so paths resolve correctly on each machine
- Lives in git-synced folder (HG-Skills-Private repo)
- Is idempotent (safe to re-run)

---

## Notes

- Paths use `$HOME` which expands at runtime for each machine
- Some MCPs (like Vercel) are HTTP-based and will prompt for auth per-workspace
- Personal-only MCPs (Stripe, Neon, PostHog, Cloudflare, etc.) are NOT in this script - Kevin adds manually if needed on specific accounts
