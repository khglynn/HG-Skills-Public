---
name: backlog-management
description: >
  Manage project idea parking lots using BACKLOG.md files. Use when someone says
  "add this to the backlog," "I had an idea," "let's come back to this later,"
  "park this," "triage the backlog," "what's on the backlog," "what ideas do we
  have," or when an idea comes up mid-session that isn't current focus. Also use
  at session end to capture ideas that surfaced during work. Do NOT use for
  current-session task management — that's NOW.md and the Tasks system.
---

# Backlog Management

*Last updated: 2026-03-27 · v1.0*

A project's backlog is its idea parking lot — low-friction capture, periodic triage, no ceremony. Claude maintains it; the user reviews it.

## The File: BACKLOG.md

One flat markdown file per project. Four sections:

```markdown
# BACKLOG

*Last triaged: YYYY-MM-DD*

## Blocked
- [ ] Items waiting on someone or something external
- [ ] Include WHO/WHAT is blocking and since when

## Ideas
- [ ] Things worth doing eventually
- [ ] No priority labels — if it's urgent, it belongs in NOW.md
- [ ] Include enough context that a future session understands the idea

## Polish
- [ ] Small improvements, UI tweaks, quality-of-life fixes
- [ ] The "while you're there" list

## Research
- [ ] Questions to investigate, spikes to run, tools to evaluate
```

## How It Fits the System

| File | Purpose | Maintained By | Lifespan |
|------|---------|---------------|----------|
| `NOW.md` | Current session focus (doing + next) | Claude, rewritten each session | Ephemeral |
| `BACKLOG.md` | Idea parking lot | Claude, triaged periodically | Ongoing |
| `DIRECTION.md` | Strategic priorities + north star | Kevin + Claude | Updated when priorities shift |
| `DEVLOG.md` | What happened (chronological) | Claude, append-only | Permanent |

BACKLOG.md replaces:
- The "Parking Lot" section in NOW.md (NOW stays focused on current work)
- "Deferred Features" sections in CLAUDE.md files (those bloat always-loaded context)
- GitHub Issues used for vague ideas (Issues stay for bugs and items needing external tracking)

## When Claude Touches BACKLOG.md

**During a session:** When an idea comes up that's not the current focus, add it to BACKLOG.md rather than losing it. Keep entries short — one line with enough context for a future session to understand.

**At session end:** Check if anything was implicitly completed and remove it. Propose new items from things that surfaced during work. Don't force it — only add genuinely useful ideas, not every passing thought.

**At session start:** Scan BACKLOG.md for items related to current work. If a Polish item is in the exact area you're working, mention it: "There's a backlog item about [X] in the code we're touching — want to handle it while we're here?" Don't overwhelm — mention at most 1-2 relevant items.

**On "triage the backlog":** Full review. For each item:
- Still relevant? Keep it.
- Done? Remove it (DEVLOG records history).
- Ready to work on? Promote to NOW.md.
- Stale or no longer interesting? Cut it. Ideas are cheap — don't hoard them.
- Blocked? Move to Blocked section with WHO/WHAT is blocking.

Update the "Last triaged" date.

## Design Principles

**Ideas are cheap, not commitments.** The backlog is a parking lot, not a promise. Deleting items is healthy — it means priorities are clear.

**No metadata ceremony.** No YAML frontmatter, no task IDs, no priority labels, no assignees. If an item needs that level of structure, it should be a GitHub Issue or a claude-plans/ file.

**Claude maintains, Kevin reviews.** The goal is zero manual bookkeeping. Claude adds items during sessions, proposes triage, and removes completed work. Kevin reviews during triage to make judgment calls (cut vs. keep vs. promote).

**Four sections, not more.** Blocked / Ideas / Polish / Research covers the taxonomy of "not now" work. If a section is empty, omit it. If you're tempted to add a fifth section, reconsider whether the item belongs somewhere else.

**Items get cut, not archived.** Unlike COMPLETED.md, there's no graveyard. When something gets done or dropped, it just disappears. DEVLOG.md already records what happened in sessions. The backlog should only contain forward-looking items.

## Creating a BACKLOG.md for a Project

When starting with a new project, or when migrating from an older system:

1. Check if the project's CLAUDE.md has a "Deferred Features" or "Future Work" section — move those items into BACKLOG.md
2. Check if there are GitHub Issues that are really ideas (never getting closed, no PR linked) — move those into Ideas
3. Check NOW.md for a "Parking Lot" section — move those items, then remove the section from NOW.md
4. Set "Last triaged" to today's date

## When NOT to Use BACKLOG.md

- **Simple projects** (under 5 sessions of work) — don't add files for the sake of it
- **Shipped products** (recordOS, ynai) — use CHANGELOG.md's "Unreleased" section instead
- **Items that need external tracking** — use GitHub Issues for bugs with reproduction steps, features tied to PRs, or cross-repo coordination
