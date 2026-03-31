# CLAUDE.md Best Practices

*Synthesized from Anthropic's official docs, their blog posts, HumanLayer's research, and the skills blog. Sources linked at bottom.*

## The Core Idea

CLAUDE.md is a **configuration file**, not documentation. It loads into every session's context. Every line costs tokens and competes for Claude's attention. Start minimal, expand from actual friction.

## Size and Structure

| Source | Recommendation |
|--------|---------------|
| Anthropic docs | Under 200 lines per file |
| HumanLayer | Under 60 lines at root, heavy content in agent_docs/ |
| Practical consensus | Every line must pass: "Would removing this cause Claude to make mistakes?" |

**Instruction budget reality:** Frontier LLMs follow ~150-200 instructions with reasonable consistency. Claude Code's system prompt uses ~50. As instruction count increases, adherence degrades **uniformly across ALL instructions** — not just later ones. Every extra line weakens the entire file.

**Positional bias:** LLMs weight instructions at the beginning and end of the prompt more heavily. Put critical info first and last, not buried in the middle.

## What to Include

| Content | Why It Earns Its Place | Example |
|---------|----------------------|---------|
| Project summary | Orients Claude immediately | "Inventory tracker: Cloudflare Workers + D1 + React" |
| Key directories | Maps the codebase | "API routes in `src/routes/`, UI in `src/components/`" |
| Essential commands | Exact strings Claude will use | `pnpm dev`, `pnpm test`, `pnpm build` |
| Non-default conventions | Prevents mistakes | "Use pnpm, not npm. Use Drizzle, not raw SQL." |
| Common gotchas | Avoids repeated errors | "Drizzle schema + migrations must stay in sync" |
| Workflow rules | Can't be inferred from code | Branch naming, PR conventions, deploy process |

## What to Exclude

| Content | Why It Doesn't Belong | Alternative |
|---------|----------------------|-------------|
| Standard language conventions | Claude already knows TypeScript patterns | Let code be the teacher |
| Linter-enforced rules | Deterministic tools are faster and reliable | Use hooks (ESLint, Prettier, Biome) |
| Full API documentation | Too large, changes too often | Link with `@path/to/docs.md` or reference files |
| File-by-file descriptions | Claude can read the code | Use key files table for entry points only |
| Task-specific instructions | Irrelevant to most sessions | Move to skills or `.claude/rules/` with path scoping |
| Generic advice | "Write clean code" wastes a slot | Actionable: "PEP 8, 100 char lines" |
| Frequently changing data | Goes stale, actively misleads | Reference source files with `@path` |
| Credentials/API keys | Security risk | Use `.env` and environment variables |

## Progressive Disclosure Pattern

Don't put everything in CLAUDE.md. Use the loading hierarchy:

```
CLAUDE.md (always loaded — universal context only)
├── @imports (expanded inline at session start)
├── .claude/rules/*.md (loaded when matching files are opened)
├── .claude/skills/*/SKILL.md (loaded on-demand when relevant)
└── agent_docs/ or references/ (loaded when Claude reads them)
```

**Path-scoped rules** via `.claude/rules/` with YAML frontmatter:
```yaml
---
paths:
  - "src/api/**/*.ts"
---
API routes must return JSON. Use Hono's c.json() helper.
```
These only load when Claude touches matching files.

## The WHY / WHAT / HOW Framework

A good CLAUDE.md addresses three dimensions:
- **WHAT:** Project stack, architecture, codebase topology
- **WHY:** Business context, purpose of each subsystem, why decisions were made
- **HOW:** Build commands, test execution, deploy steps, verification

Most CLAUDE.md files over-index on HOW and under-index on WHY. Claude can figure out HOW from the code. It can't figure out WHY without context.

## Anti-Patterns

**The accumulation trap:** Teams append fixes for every undesired behavior. The file grows to 500+ lines. Important rules get buried. Claude starts ignoring instructions. The instinct to add bolder formatting makes it worse.

**The completeness trap:** Trying to document everything on day one. Better: start with 30 lines, expand only when Claude makes a mistake that instructions would have prevented.

**The linter trap:** Writing prose about code style when deterministic tools handle it perfectly. Every formatting rule in CLAUDE.md is a wasted instruction slot.

**The copy trap:** Embedding code snippets that go stale. Use `file:line` references instead: "See `apps/api/src/auth.ts:14-45`" stays current.

**The auto-generate trap:** `/init` produces generic output. CLAUDE.md is the highest-leverage configuration point — each line compounds across all sessions. Hand-craft deliberately.

## Brevity Standard

Instead of:
> "The project uses a package management tool called Bun, which is a faster alternative to npm. You should always use Bun for installing dependencies and running scripts."

Write:
> "Uses Bun (not npm). Install: `bun install`."

## File Hierarchy

| Scope | Location | Shared? |
|-------|----------|---------|
| Managed policy | `/Library/Application Support/ClaudeCode/CLAUDE.md` | All org users via MDM |
| Project root | `./CLAUDE.md` | Team via git |
| User global | `~/.claude/CLAUDE.md` | Just you, all projects |
| Local override | `./.claude.local.md` | Just you, gitignored |
| Subdirectory | `./packages/api/CLAUDE.md` | Team, loads on demand |

Most specific wins. Subdirectory files load only when Claude reads files there.

## Key Tensions

- Anthropic recommends `/init` as a starting point; HumanLayer warns against it (bad context → bad code at scale)
- Anthropic says 200 lines max; practitioners say aim for 60 at root
- All sources agree: **specific, concise, verifiable > comprehensive**

## Sources

- [How Claude remembers your project — Anthropic Docs](https://code.claude.com/docs/en/memory)
- [Using CLAUDE.md Files — Anthropic Blog](https://claude.com/blog/using-claude-md-files)
- [Writing a Good CLAUDE.md — HumanLayer](https://www.humanlayer.dev/blog/writing-a-good-claude-md)
- [Equipping Agents with Skills — Anthropic Blog](https://claude.com/blog/equipping-agents-for-the-real-world-with-agent-skills)
