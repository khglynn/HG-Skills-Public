# Pattern Extraction

*Created: 2026-03-01*

## Purpose

Audit how Kevin works across projects and produce Holt-readable docs for fact-checking. Holt's researcher reviews these on its own schedule and adds callout-style suggestions.

## When to Use

Run periodically (monthly or after major workflow changes) to keep Holt's understanding of Kevin's processes current.

## What It Does

1. **Scans** `.claude/` project configs, CLAUDE.md files, and skills across Kevin's projects
2. **Identifies** working patterns worth validating:
   - Prompt structures and common prompt patterns
   - Tool usage conventions (MCP configs, CLI workflows)
   - Workflow conventions (commit style, review process, deploy flow)
   - Session management patterns (breadcrumbs, plans, devlogs)
   - Communication preferences and documentation habits
3. **Writes findings** to `3.0 Holtin/Processes/` with timestamps

## How to Run

```bash
# From any project directory
# Claude Code executes this as a content task — no CLI command needed
```

Tell Claude Code: "Run the pattern extraction skill" or "Extract my working patterns for Holt"

## Steps

### 1. Gather Sources

Scan these locations for patterns:

| Location | What to Look For |
|----------|-----------------|
| `~/DevKev/*/CLAUDE.md` | Project-specific conventions, recurring instructions |
| `~/DevKev/helper/skills-public/` | Established reusable workflows |
| `~/DevKev/helper/skills-private/` | Private workflow patterns |
| `~/.claude/settings.json` | Tool permissions, allowed commands |
| `~/DevKev/helper/CLAUDE.md` | Cross-project working agreements |
| `~/DevKev/helper/KEVIN-TIPS.md` | Human-side patterns and preferences |

### 2. Identify Patterns

Look for:
- **Repeated instructions** across multiple CLAUDE.md files (indicates a pattern Kevin enforces)
- **Skill usage frequency** — which skills exist vs which are actually referenced
- **Tool preferences** — which MCPs are always-loaded vs on-demand, and why
- **Workflow conventions** — commit messages, branch strategy, review process
- **Prompt patterns** — common prompt structures Kevin uses (from Obsidian, CLAUDE.md instructions)
- **Session patterns** — how sessions start, progress, and end

### 3. Write Output

Save findings to `~/Documents/HG Main/3.0 Holtin/Processes/Working-Patterns-{YYYY-MM-DD}.md`

#### Output Format

```markdown
---
extracted_by: sync-bot pattern extraction
extracted_on: {date}
sources_scanned: {count}
patterns_found: {count}
---
# Kevin's Working Patterns — {date}

## Summary
Brief overview of key findings.

## Patterns

### {Pattern Name}
**Where seen:** {list of files/projects}
**What it is:** {description}
**Why it matters:** {context for Holt's researcher}

> [!question] For Holt's Researcher
> {Specific question or validation request — e.g., "Is this commit message convention aligned with conventional commits best practice?"}

### {Next Pattern}
...

## Observations
- {Notable gaps or inconsistencies found}
- {Patterns that seem outdated}
- {Potential improvements spotted}
```

### 4. Holt's Feedback Format

Holt's researcher adds feedback using Obsidian callouts:

```markdown
> [!suggestion] Holt Researcher ({date})
> {Observation or recommendation}
> {Reference to best practice or external source}
```

These callouts are:
- Native to Obsidian (no plugins needed)
- Visually distinct from the extracted patterns
- Easy for Kevin to review and act on

## Notes

- This is a **read-only extraction** — it doesn't modify any project files
- Output goes to `3.0 Holtin/Processes/` where Holt can access it via Relay
- Future enhancement: compare current patterns against previous extractions to track evolution
- Keep findings factual — describe what IS, not what should be. Holt's researcher handles the "should"
