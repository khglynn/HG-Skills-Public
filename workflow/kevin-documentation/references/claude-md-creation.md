---
title: Create Project CLAUDE.md
description: Create a minimal project CLAUDE.md that grows over time.
---

# Create Project CLAUDE.md

Create a minimal CLAUDE.md that grows over time. Ask followups if you need more context.

## Template

```markdown
# [Project Name] - Agent Instructions

*Inherits from ~/DevKev/CLAUDE.md*

## About This Project
[Brief description from conversation]

## Always-Allowed (project-specific)
[Leave empty until identified]

## Project-Specific Notes
[Fill in as we learn]

## Relevant Docs & Links
[Add as discovered]
```

## Project Setup Checklist

When creating a new project:

1. **Create CLAUDE.md** (template above)
2. **Create project-specific Playwright instance** if the project needs browser automation:
   - Add entry to `~/.claude.json` under `mcpServers`:
     ```json
     "playwright-{projectname}": {
       "command": "npx",
       "args": ["@anthropic-ai/mcp-playwright", "--user-data-dir", "~/.playwright-{projectname}/"]
     }
     ```
   - Document in CLAUDE.md which Playwright instance to use
   - First-time login required per profile (sessions are isolated)
3. **Create tech stack file** at `~/DevKev/tools/helper/project-stacks/{project-name}.md`
4. **Add to _index.md** in project-stacks folder

## When to update later

- Confirm a tool/API we're using
- Discover a gotcha worth remembering
- Complete a workflow worth documenting
