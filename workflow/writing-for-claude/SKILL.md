---
name: writing-for-claude
description: >
  How to write instructions that work WITH Claude's design, not against it —
  skills, CLAUDE.md files, plans, system prompts, project instructions, or any
  context Claude will follow. Use when someone is writing a SKILL.md, drafting
  a CLAUDE.md, creating a plan, designing project instructions, writing system
  prompts, or reviewing any document that tells Claude how to behave. Also use
  when you notice rigid NEVER/ALWAYS/CRITICAL patterns that should be reasoning
  instead, or when instructions aren't producing the expected behavior and the
  cause might be HOW they're written rather than WHAT they say.
---

# Writing for Claude, Not at Claude

*Last updated: 2026-03-31 · v1.0*

Claude is a reasoning model designed to internalize values and exercise judgment. Its constitution says: cultivate good values and judgment over strict rules. When you write instructions for Claude — in any form — you get better results by explaining reasoning than by issuing commands.

This isn't about being loose or vague — over-hedging is its own failure mode. It's about giving Claude the tools to be genuinely helpful rather than mechanically compliant. Be confident about things that are true, honest about things that might change, and clear about why things matter.

## The Core Principle

**Explain WHY, not just WHAT.** When Claude understands the reasoning behind a guideline, it can:

- **Adapt to situations the author didn't anticipate.** A rule says "never recommend unapproved tools." Reasoning says "we standardize to keep security manageable and avoid paying for overlapping products" — so Claude can handle the edge case where someone asks about a tool that's being evaluated, or where the approved tool genuinely doesn't fit.

- **Explain the reasoning to users.** A rule just says "no." Reasoning lets Claude say "we use Klaviyo instead of Mailchimp because it has deep Shopify integration and Okta SSO — fewer systems to maintain."

- **Calibrate confidence to the situation.** Claude should be confident about concepts that won't change (what a terminal is, why security matters) and honest about facts that might (who to contact, which URLs work, what tool versions are current).

- **Exercise its own judgment.** Claude's constitution warns against epistemic cowardice — giving deliberately vague answers to avoid controversy. Good instructions empower Claude's judgment rather than suppressing it.

## What This Looks Like in Practice

### Instead of rigid rules, explain the reasoning:

| Rigid (fights Claude's design) | Reasoning-based (works with it) |
|-------------------------------|--------------------------------|
| "NEVER recommend unapproved tools" | "We standardize on specific tools to keep security manageable. When someone asks about an unapproved tool, check for an approved alternative and explain why it's preferred." |
| "ALWAYS translate jargon immediately" | "Non-developers don't have the vocabulary for technical concepts. Translate jargon the moment it appears — waiting for them to ask means they're already confused." |
| "CRITICAL: do not deploy without auth" | "Deploying without auth means employee data is publicly accessible. This happened once and the service was deleted within hours. Auth must be verified before any deploy." |
| "MUST use plan mode for database changes" | "Database migrations are hard to reverse and can break running queries. Think through the approach first — plan mode gives both of us a chance to catch issues before they hit real data." |

### Present changeable facts as current knowledge, not eternal truth:

| Over-confident | Calibrated |
|---------------|-----------|
| "Kevin HG handles sandbox access" | "As of the last update, Kevin HG handles sandbox access" |
| "There are 287 active employees" | "Okta shows 287 active employees as of March 2026 — this changes as people join and leave" |
| "Use port 8790 for the API" | "The API runs on port 8790 (configured in wrangler.jsonc — check there if this seems wrong)" |

### Trust one good example over ten rules:

A single well-crafted example of the behavior you want teaches Claude more than a page of constraints. Show what a good response looks like, and Claude will generalize the pattern. Show ten rigid rules, and Claude will follow them literally — even when the situation calls for something different.

### Include escape hatches:

Every set of instructions should give Claude — and the user — explicit permission to deviate when the situation calls for it:

- "If this doesn't match what you're seeing, trust what you see and help the user adapt."
- "These are guidelines based on how things work today. If something has changed, acknowledge it and use your judgment."
- "When in doubt, ask — a question is better than a wrong assumption."

## Where This Applies

This principle applies to EVERY form of Claude instruction:

| Format | How This Applies |
|--------|-----------------|
| **SKILL.md files** | Description triggers on reasoning, not keywords. Body explains WHY the behavior matters. Heavy data in references, not inline rules. |
| **CLAUDE.md files** | Project context that helps Claude understand the codebase, not a rulebook of dos and don'ts. Under 200 lines — every line should pass "would removing this cause mistakes?" |
| **Plans** | Explain the goal and constraints, not a rigid step sequence. Claude can find creative paths if it understands the destination. |
| **System prompts** | Set the frame and values, not a wall of MUSTs. The model does its best work when it understands what you're optimizing for. |
| **Project instructions** | Context about what the project is and why decisions were made. Architecture reasoning, not just architecture rules. |

## The Litmus Tests

When reviewing any instructions for Claude, ask:

1. **"Could Claude handle the case I didn't think of?"** If following the instructions literally would produce a bad result in an edge case, the instructions need more reasoning and less rigidity.

2. **"Does Claude know WHY?"** For every constraint, can Claude explain the reasoning to a user who asks? If not, the instruction is a command, not guidance.

3. **"Am I confident about things that change?"** Names, URLs, counts, tool versions, contact info — these should be presented as current knowledge with dates, not eternal facts.

4. **"Is there an escape hatch?"** If the real world doesn't match the instructions, can Claude adapt? Or is it trapped by rigid rules?

5. **"Would I write this to a smart colleague?"** Claude is closer to a thoughtful senior teammate than a script runner. Write instructions the way you'd brief a colleague — context, reasoning, judgment calls — not the way you'd write a configuration file.

## The Source

This approach is grounded in Claude's constitution ([anthropic.com/constitution](https://www.anthropic.com/constitution)), which establishes:

- **Judgment over rules** — "We generally favor cultivating good values and judgment over strict rules"
- **Calibrated uncertainty** — Claude should convey exactly as much confidence as the evidence warrants, no more
- **Anti-sycophancy** — helpfulness is not obedience; Claude should push back when something seems wrong
- **Autonomy-preserving** — Claude should foster independent thinking, not dependence on rigid instructions
- **Epistemic honesty** — giving vague answers to avoid controversy is itself dishonest

These aren't abstract principles — they directly shape how Claude processes your instructions. Working with this design produces better outcomes than fighting it.
