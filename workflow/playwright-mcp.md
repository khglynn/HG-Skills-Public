# Playwright MCP Server

**Updated:** 2025-12-12

## When to Use Playwright

**Kevin's preference: Use it liberally.** Don't just describe what to do - open the window and do it.

| Instead of... | Do this |
|--------------|---------|
| "Go to Sentry and configure..." | Open Sentry, navigate to the right page, guide Kevin through it |
| "Check if the CSS looks right" | Take a screenshot of the page |
| "The user reported a console error" | Open the page, check console messages, screenshot the issue |
| "Try refreshing the page" | Actually refresh and verify |

**Proactive usage:**
- After frontend changes → screenshot to verify
- Debugging user-reported issues → replicate in browser
- Build completes → check the deployed URL
- UI/UX questions → show, don't describe
- GitHub deploys/PRs → screenshot for documentation
- Project docs → capture current state visually

## Basic Setup

For general use, `playwright-generic` is the default:

```json
"playwright-generic": {
  "type": "stdio",
  "command": "npx",
  "args": ["@playwright/mcp@latest", "--user-data-dir", "/Users/KevinHG/.playwright-generic"],
  "env": {}
}
```

The `--user-data-dir` stores cookies/sessions. Kevin logs in once, future sessions stay authenticated.

## Project-Specific Instances (Recommended)

Each project gets its own Playwright MCP with isolated browser profile. This prevents login/session conflicts when working across multiple projects simultaneously.

**Current setup:**
| Instance | Profile | Use For |
|----------|---------|---------|
| `playwright-eachie` | `~/.playwright-eachie/` | Eachie project |
| `playwright-recordos` | `~/.playwright-recordos/` | recordOS project |
| `playwright-generic` | `~/.playwright-generic/` | Everything else |

**Which to use:** Check the project's CLAUDE.md for which instance to use. If not specified, use `playwright-generic`.

### Adding a New Project Instance

1. **Add to `~/.claude.json`** under `mcpServers`:
```json
"playwright-{project}": {
  "type": "stdio",
  "command": "npx",
  "args": ["@playwright/mcp@latest", "--user-data-dir", "/Users/KevinHG/.playwright-{project}"],
  "env": {}
}
```

2. **Create profile directory:**
```bash
mkdir -p ~/.playwright-{project}
```

3. **Add to project's CLAUDE.md:**
```markdown
## Playwright for Visual Testing

Use `playwright-{project}` MCP for this project (not generic playwright).

This keeps browser sessions/logins isolated from other projects. Profile stored at `~/.playwright-{project}/`.
```

4. **Update global CLAUDE.md** (`~/DevKev/CLAUDE.md`) - add to the Playwright instances table

5. **Restart Claude Code** to load the new MCP

**Trade-off:** Each profile needs its own login sessions. Logins in `playwright-eachie` don't exist in `playwright-recordos`.

### Throwaway Sessions

For temporary work that doesn't need persistent logins:
```json
"playwright-temp": {
  "args": ["@playwright/mcp@latest", "--isolated", "--user-data-dir", "/tmp/playwright-temp"]
}
```

## Browser Engines

Playwright supports multiple browsers via the `--browser` flag:

| Engine | Tests Like | Flag |
|--------|-----------|------|
| `chromium` | Chrome, Edge | (default) |
| `firefox` | Firefox | `--browser firefox` |
| `webkit` | Safari | `--browser webkit` |

**Example - Safari testing:**
```json
"playwright-safari": {
  "args": ["@playwright/mcp@latest", "--browser", "webkit", "--user-data-dir", "~/.playwright-safari-profile"]
}
```

Or just ask: "Take a screenshot of localhost:5173 in webkit"

## Mobile Emulation

Playwright's mobile emulation is **viewport + user agent**, not a real device. It:
- Resizes to phone dimensions (e.g., iPhone 14: 390x844)
- Sets mobile user agent
- Simulates touch events
- Can set device pixel ratio

**What it tests well:**
- Responsive CSS, media queries
- Mobile-specific JS (touch events, viewport checks)
- User agent sniffing
- Layout at specific breakpoints

**What it doesn't test:**
- Actual iOS/Android browser quirks
- Safari-specific CSS bugs (webkit engine helps but isn't identical to real Safari)
- Real touch performance
- Native browser features (PWA install prompts, etc.)

**Verdict:** Good for responsive layout testing. Use real devices for Safari-specific bugs.

## Dev Server Workflow (Important!)

When working with Next.js/React dev servers:

**After editing code:**
- **DON'T** navigate to the page (causes 500 errors during recompilation)
- **DO** just take a screenshot - HMR already pushed the changes to the open browser

**Why:** Hot Module Reload (HMR) instantly updates the page. A fresh `browser_navigate` triggers a full page request that can hit the server mid-recompile → 500 error.

**Workflow:**
1. Edit code
2. `browser_snapshot` or `browser_take_screenshot` (no navigation)
3. Only navigate if you need a different page/route

**When to navigate:**
- First time opening a page
- Changing routes (e.g., `/` → `/settings`)
- After server restart
- Testing fresh page load behavior

## Common Commands

```
"Navigate to https://example.com"
"Take a screenshot"
"Click the Login button"
"Type 'hello' in the search box"
"Take a screenshot at 375x667 (mobile)"
"Test this in webkit"
"Check the console for errors"
"Resize to 1920x1080"
```

## Troubleshooting

**"Browser is already in use"** - Another Claude instance has the profile locked. Options:
1. Use the project-specific instance (check project CLAUDE.md)
2. Close the other instance's browser
3. Use `playwright-generic` if your project doesn't have a dedicated instance

**Tabs keep opening** - Normal behavior. Each navigation opens in a tab. Playwright manages cleanup, but can accumulate during long sessions. Not harmful, just visual clutter.

## Reference

- Playwright MCP: https://github.com/microsoft/playwright-mcp
- Multi-instance solution: GitHub issue #893
