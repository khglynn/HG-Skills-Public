---
title: Audio-Friendly Document Export
description: Convert docs into educational audio content - TTS-readable AND worth listening to. Explains the why, digs into concepts, teaches rather than skims.
triggers:
  - "make this listenable"
  - "audio version"
  - "speechify export"
  - "read-to-me version"
  - "listening format"
---

# Audio-Friendly Document Export

Create educational audio content from technical docs. Two goals:
1. **TTS-readable** - No tables, ASCII art, or formatting that sounds weird
2. **Worth listening to** - Teaches concepts, explains why, doesn't skim the surface

## Philosophy

Every doc is an opportunity to reinforce ideas that get glossed over. Don't just convert - expand and educate. Explain:
- **Why** things work the way they do
- **History** and context behind tools/approaches
- **Tradeoffs** and debates in the community
- **Underlying mechanics** that most docs skip
- **Connections** to broader concepts in coding, AI, the landscape

Default to depth. Kevin is listening to learn, not to skim.

## What to Remove

These don't work in audio:
- Tables → convert to prose
- ASCII diagrams/boxes → describe or skip
- Code blocks → describe what it does, or reference the file
- Markdown formatting (`**bold**`, `## headers`, bullets)
- Inline links

## What to Convert

| From | To |
|------|-----|
| `~/.claude/CLAUDE.md` | "tilde-slash-dot-claude-slash-CLAUDE.md" |
| Abbreviations | Spell out first use ("MCP, or Model Context Protocol") |
| Lists | Flowing prose with transitions |
| Tables | Narrative ("First... Second... Finally...") |

## Structure

### Opening
- What this doc covers
- Quick roadmap of sections

### Body
- Clear verbal section breaks ("Now let's talk about...")
- After explaining a concept, go deeper - the why, the history, the tradeoffs
- Shorter paragraphs (2-4 sentences)
- Explicit structure ("This has three parts. First...")

### Closing
- Key takeaways
- Clear ending

## Going Deeper (Default Behavior)

After each main concept, consider adding depth on:
- **Design decisions** - Why was it built this way?
- **Tradeoffs** - What are the alternatives? What did they sacrifice?
- **History** - Where did this come from? What problem was it solving?
- **Debates** - What do people disagree about?
- **Bleeding edge** - What's changing? What's coming?
- **Underlying mechanics** - How does it actually work?

Not every concept needs all of these, but look for opportunities to teach.

## Output

- Save alongside original with `-audio` suffix
- Note estimated listen time (rough: 150 words = 1 minute)
- Keep original doc intact

## Example: Surface vs Deep

**Surface (don't do this):**
"Auto-compaction triggers at 95% context usage and summarizes your conversation."

**Deep (do this):**
"Auto-compaction triggers when you hit about 95% of your context window. You might wonder why 95% and not 80% or 99%. This threshold represents a tradeoff. Compact too early and you lose context you had room for. Compact too late and you risk running out mid-thought. The 95% gives Claude about 10,000 tokens of headroom to generate a summary and handle the next few exchanges. Earlier versions had noticeable delays during compaction - the current implementation is much faster, but the threshold choice is still somewhat arbitrary. Some users argue for configurable thresholds, but that's not available yet."

## Workflow

1. Read source doc
2. Convert section by section - remove formatting, expand to teach
3. Look for opportunities to explain why, add history, note tradeoffs
4. Save with `-audio` suffix
