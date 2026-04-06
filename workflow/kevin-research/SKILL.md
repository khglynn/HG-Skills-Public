---
name: kevin-research
description: >
  Research anything — software, tools, products, Obsidian plugins, or general capability
  questions. Use when Kevin says "research this," "find me options for," "compare these,"
  "what's the best tool for," "look into," "evaluate," "search my Obsidian vault," "find
  a plugin for," "help me shop for," "where can I buy," or any task that requires searching
  external sources, comparing options, or evaluating capabilities. Also activate when Kevin
  needs to figure out which tool or service to use for a task, or when a recommendation
  requires current information rather than training data.
---

# Kevin's Research Skill

Consolidated research patterns for different domains. Claude reads the relevant reference file based on what's being researched.

## When to Use Which Reference

| Research Type | Reference File | Key Approach |
|--------------|----------------|-------------|
| **Obsidian plugins, features, workflows** | `references/obsidian.md` | Lead with ObsidianStats.com data, check mobile compatibility |
| **Software evaluation, tool comparison** | `references/software-eval.md` | Always search live — software changes fast. Prioritize current docs, reviews, forums |
| **Which tool or service to use** | `references/capability-scan.md` | Check existing MCPs first, then evaluate MCP vs API vs Playwright vs manual |
| **Shopping, product research** | `references/shopping.md` | Verified purchase links, prioritize Amazon (6-month financing), local Austin stores |
| **Search Apple Notes** | `references/apple-notes.md` | Scratch notes, ideas, captures. Use Apple Notes MCP for live search. |

## Universal Research Principles

- **Search first, answer second.** Never rely on training data for software capabilities, pricing, availability, or compatibility. A 10-second search beats a confident wrong answer.
- **Use tools as research instruments.** CLIs, MCPs, and APIs aren't just for executing tasks — they're rich information sources. `npx skills find` reveals skills web search misses. `ollama list` shows what's actually installed. Gemini CLI's google-workspace extension bundles curated gmail and calendar skills alongside MCP tools, giving richer structured output than hitting the raw MCPs. When a tool has a CLI or API available, USE it rather than guessing from training data. Claude's instinct is often "we don't need that" — and it's often wrong. A 30-second CLI call beats a confident assumption.
- **Show your sources.** Link to where you found information. Kevin wants to verify and dig deeper.
- **Compare honestly.** When evaluating options, present tradeoffs — not just the "winner." Kevin makes his own decisions based on the full picture.
- **Current over comprehensive.** A focused answer with current data beats an encyclopedic answer from training data.

## Related Skills

Research often leads to building or automating:
- **kevin-automation** → when research reveals an internal API or automation opportunity (see `references/internal-api-discovery.md` for the pattern)
- **kevin-skills** → when research uncovers a repeatable workflow that should become a skill (search `npx skills find` before building)
- **local-ai-on-mac** → when the task could use a local model instead of a cloud API
