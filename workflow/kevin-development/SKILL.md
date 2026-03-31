---
name: kevin-development
description: >
  Building and deploying web projects — Cloudflare Workers, database design, tech stack
  setup, code quality, and solo dev workflows. Use when Kevin says "build this," "deploy,"
  "set up a new project," "create a database schema," "clean up this code," "run a hygiene
  check," "set up monitoring," "configure Cloudflare," "manage DNS," or any task involving
  writing code, deploying apps, designing databases, or maintaining code quality. Also
  activate for questions about Kevin's preferred stack (Cloudflare + D1 + Drizzle + Vite)
  or when starting a new project from scratch.
---

# Kevin's Development Skill

Patterns for building, deploying, and maintaining web projects.

## When to Use Which Reference

| Task | Reference File | Key Approach |
|------|----------------|-------------|
| **Building or deploying a web app** | `references/web-dev.md` | Solo workflow: push to main, Vercel for hosting, branch only when asked |
| **Cloudflare Workers, DNS, D1, KV, R2** | `references/cloudflare.md` | Always explain before making changes. Careful with DNS. |
| **Database schema design** | `references/database.md` | Analytics-friendly, human-readable schemas. Kevin's preferred patterns. |
| **New project setup (monitoring, auth, infra)** | `references/tech-stack.md` | Common patterns across Kevin's projects |
| **Code cleanup, dead code, cruft** | `references/code-hygiene.md` | Run `npm run knip` first. Systematic, not aggressive. |

## Universal Development Principles

- **Explain what you're building and why** before writing code. Kevin is technical but not a developer — the architecture matters as much as the implementation.
- **Solo project workflow:** Push to main unless Kevin says otherwise. No unnecessary branching ceremony.
- **Build check before sharing:** Always run the build and verify before pushing.
- **Propose plan mode** for anything touching database schema, auth, or deployment config.
