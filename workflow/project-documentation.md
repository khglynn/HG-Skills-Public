---
title: Project Documentation
description: Rules for splitting documentation between Obsidian (for Kevin) and GitHub (for AI/code). Obsidian gets the decision journey and how-to-use details. GitHub gets minimal context for code work.
---

# Project Documentation

## When to Document

- **"Let's document this"** or **"wrap up"** - end of session or major milestone
- **Resuming after a break** - update Obsidian with what happened, check GitHub docs are current
- **Before /compact** - ideal timing; full chat history still available for richer summaries

## Where Things Go

### Obsidian → `/Users/KevinHG/Documents/HG Main/0.2 Clips + Social + AI/AI Chats/YYYY/MM mmm/MUX - {short description}.md`

Examples:
- `2025/11 Nov/MUX - Skills repo setup.md`
- `2025/12 Dec/MUX - Election helper app.md`

One file per project. Structure top-to-bottom from practical to historical:

```markdown
# {Project Name}

## What This Is
One paragraph: what it does, why it exists.

## Quick Reference
- **URL:** https://...
- **Run locally:** `npm run dev`
- **Deploy:** `vercel --prod` or push to main
- **Env vars needed:** LIST_THEM

## How to Modify
- To change X, edit `file.ts` and redeploy
- To add Y, ...
- Config lives in `config.json`

## Decisions & Context
Why we built it this way. Key tradeoffs.

## Session Log

### Nov 25, 2025
**What happened:** Built the auth system, integrated Stripe.

**Key decisions:**
- **Sessions over JWT:** Mobile apps had token refresh issues
- **Stripe over Paddle:** Better US tax handling

**Pivots & problems:**
- First approach used localStorage, broke on Safari private mode
- Switched to httpOnly cookies after 2 hours debugging

**Files changed:** `auth.ts`, `middleware.ts`, `stripe-webhook.ts`
```

### GitHub → `README.md` + optional `CONTEXT.md`

**README.md** (required):
- What this is (1 paragraph)
- How to run it
- How to deploy
- Env vars / API keys needed
- Live URL

**CONTEXT.md** (only if complex):
- Architecture decisions that affect how to write code
- Constraints future AI needs to know
- NOT: tutorials, workflow guides, Q&A docs

**Delete these patterns:**
- AI-TOOLS-GUIDE.md
- ANSWERS-TO-YOUR-QUESTIONS.md
- YOUR-ACTUAL-WORKFLOW.md
- WORKFLOW-EXAMPLES.md
- Duplicate docs across folders

## Session Summary Format

When documenting a session, capture:

1. **What happened** - 1-2 sentences, outcomes not process
2. **Key decisions** - Bold the choice, explain why in ~10 words
3. **Pivots & problems** - What broke, what we tried, what worked
4. **Files changed** - Just the list, no explanations

Aim for **useful density** - enough detail to reconstruct the reasoning, not a transcript.

## Multi-Session Projects

- Same Obsidian file, append new session sections
- Collapse old sessions with `<details>` tags when they get stale:

```markdown
<details>
<summary>Earlier sessions (Oct 2025)</summary>

### Oct 15, 2025
...old session content...

</details>

### Nov 25, 2025
...current session...
```

## Workflow

```
During session: Don't create docs mid-stream. Focus on the work.

End of session:
1. Kevin: "Let's document this"
2. AI: Create/update Obsidian file with session summary
3. AI: Consolidate GitHub to README.md + CONTEXT.md (if needed)
4. AI: Delete redundant doc files in repo
5. AI: Post breadcrumb message (see below)
6. Kevin: /compact (cmux handles AI-friendly summary)

Resuming after compaction:
1. AI: Check for MUX doc path in compaction summary
2. AI: If not found, search AI Chats folder for related "MUX - *.md" file
3. AI: Read the MUX doc for full context (richer than compaction summary)
4. AI: Update Obsidian with new session when done
```

## Breadcrumb for Compaction

After saving documentation, **always** end with this message so compaction captures the reference:

```
📋 Documentation saved: [full path to MUX file]

Read this file for full context including decision history, implementation details, and how-to guides.
```

Example:
```
📋 Documentation saved: /Users/KevinHG/Documents/HG Main/0.2 Clips + Social + AI/AI Chats/2025/11 Nov/MUX - Skills repo setup.md

Read this file for full context including decision history, implementation details, and how-to guides.
```

This breadcrumb gets included in the compaction summary, helping future sessions find the richer docs.
