# Skill Writing Guide — From Anthropic's Skill Creator

*Extracted from the official skill-creator at `~/.agents/skills/skill-creator/SKILL.md`. These are Anthropic's own recommendations for writing effective skills.*

## Description — The Most Important Line

The `description` field in YAML frontmatter is the **primary triggering mechanism**. It determines whether Claude activates the skill. Include both what the skill does AND specific contexts for when to use it.

Claude has a tendency to **undertrigger** — to not use skills when they'd be helpful. Combat this by making descriptions slightly "pushy":

**Too passive:** "How to build a simple dashboard to display internal data."

**Better:** "How to build a simple fast dashboard to display internal data. Make sure to use this skill whenever the user mentions dashboards, data visualization, internal metrics, or wants to display any kind of company data, even if they don't explicitly ask for a 'dashboard.'"

All "when to use" info goes in the description, not in the body.

## Progressive Disclosure — The Token Budget

Skills load in stages:
1. **Metadata** (name + description) — always in context (~100 words per skill)
2. **SKILL.md body** — loaded when the skill triggers (<500 lines ideal)
3. **Reference files** — loaded on-demand (unlimited size)

This means you can have dozens of skills available without burning context budget. Heavy content goes in `references/`, not inline.

## Anatomy of a Skill

```
skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter (name, description required)
│   └── Markdown body (instructions)
└── references/ (optional)
    ├── domain-a.md
    └── domain-b.md
```

## Writing Style — From Anthropic

> "Try to explain to the model why things are important in lieu of heavy-handed musty MUSTs. Use theory of mind and try to make the skill general and not super-narrow to specific examples."

> "If you find yourself writing ALWAYS or NEVER in all caps, or using super rigid structures, that's a yellow flag — if possible, reframe and explain the reasoning so that the model understands why the thing you're asking for is important. That's a more humane, powerful, and effective approach."

> "Today's LLMs are smart. They have good theory of mind and when given a good harness can go beyond rote instructions and really make things happen."

## Improvement Philosophy

> "Rather than put in fiddly overfitty changes, or oppressively constrictive MUSTs, if there's some stubborn issue, you might try branching out and using different metaphors, or recommending different patterns of working."

> "Try hard to explain the WHY behind everything you're asking the model to do... try to actually understand the task and why the user is writing what they wrote, and what they actually wrote, and then transmit this understanding into the instructions."

## Testing

Standard testing pattern:
- **5 positive tests:** Prompts that SHOULD trigger the skill (varied phrasings, some casual, some formal)
- **3 negative tests:** Prompts that should NOT trigger (near-misses, not obviously unrelated)
- **2 edge cases:** Ambiguous prompts near the boundary

Grade **outcomes, not paths** — agents find creative solutions.

## Key Patterns

- Reference files clearly from SKILL.md with guidance on when to read them
- For large reference files (>300 lines), include a table of contents
- When a skill covers multiple domains, organize by variant with Claude reading only the relevant reference
- Skills must not surprise the user — contents should match what the description promises
