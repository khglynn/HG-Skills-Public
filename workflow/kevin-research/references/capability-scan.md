---
title: Capability Discovery
description: Think through the right tool for external services and automation tasks
---

# Capability Discovery

When a task involves external services (email, calendar, project tools, APIs, etc.), think through these questions before diving in.

## Questions to Ask

### 1. Does Claude already have access?

**Check CLAUDE.md** for Kevin's current MCP servers. If one covers the service, use it.

### 2. Is there an easier path Kevin could take?

**Claude web app (claude.ai)** has built-in connectors for many services (Google, Slack, Notion, GitHub, etc.). These are often simpler than setting up MCPs or automating browsers.

If you suspect a connector might exist:
- Search: "claude.ai connectors [service name]"
- Or ask Kevin: "The Claude web app might have a connector for this - want to check there first?"

Don't assume you know what connectors exist - they change. When in doubt, search or ask.

### 3. What's the scope?

| Scope | Good Options |
|-------|--------------|
| One-time task | Playwright (browser automation), manual assist |
| Recurring need | MCP server, hook, or direct API integration |
| Complex multi-step | Agents, workflows |

### 4. Does Kevin need to see it happening?

- **Yes**: Playwright (Kevin watches the browser), manual assist
- **No**: MCP, API, background agent

### 5. Does it require Kevin's credentials?

- **Already logged in via Playwright profile**: Can automate it
- **Needs fresh OAuth/login**: Ask Kevin how to proceed
- **API key based**: Check if MCP exists or could be added

## Approach

Rather than following a fixed priority order, **match the tool to the situation**:

- **Simplicity wins** - If Kevin can do it faster in the web app, point there
- **Use what's set up** - Check CLAUDE.md for existing MCPs before suggesting new ones
- **Ask before building** - Don't set up new MCPs without checking if it's worth the effort
- **Be honest about limitations** - If Claude Code can't do something well, say so

## When to Search

If the task involves a service you're unsure about:
1. Search for "claude.ai [service] connector" to see if web app handles it
2. Search for "[service] MCP server" to see if one exists
3. Check if the service has a public API that Playwright could interact with

Don't guess - verify.
