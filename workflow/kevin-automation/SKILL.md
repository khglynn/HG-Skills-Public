---
name: kevin-automation
description: >
  Browser automation, MCP servers, plugins, and integration patterns. Use when Kevin says
  "automate this," "use Playwright," "open a browser," "create an MCP server," "build a
  plugin," "sync MCP configs," "what MCPs do I have," "how do plugins work," "set up
  browser testing," or any task involving browser control, MCP creation/management, or
  understanding how plugins and skills connect. Also activate when Kevin needs to interact
  with a website that requires login or when evaluating whether to use an MCP vs API vs
  Playwright for a task.
---

# Kevin's Automation Skill

Patterns for browser automation, MCP server creation, and understanding the plugin/skill ecosystem.

## When to Use Which Reference

| Task | Reference File | Key Approach |
|------|----------------|-------------|
| **Browser automation, screenshots, testing** | `references/playwright.md` | Use Playwright MCP. Save screenshots to `screenshots/`. Guide Kevin through auth parts. |
| **Building a new MCP server** | `references/mcp-creation.md` | Package for npm, test locally first, publish to PyPI or npm |
| **Syncing MCP configs across machines** | `references/mcp-sync.md` | Use `setup-mcps.sh` script. Add new MCPs to the script, not just locally. |
| **Understanding plugins, MCPs, skills, context costs** | `references/plugins-guide.md` | MCPs eat ~650 tokens per tool. Progressive disclosure. Skills vs plugins vs connectors. |

## Decision Tree: MCP vs API vs Playwright

When Kevin needs to interact with an external service:
1. **Already have an MCP?** → Use it (check `~/.claude-tecovas/.claude.json`)
2. **Official MCP available?** → Install it (`claude mcp add`)
3. **API available + straightforward?** → Call the API directly
4. **Login required + no API?** → Playwright (browser automation)
5. **One-time task?** → Playwright is fine. Recurring? → Build an MCP.
