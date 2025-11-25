---
title: Obsidian Research & Plugin Recommendations
description: Research Obsidian plugins and features using current information, prioritizing ObsidianStats.com data, official docs, and mobile compatibility considerations for a Notion migrator focused on performance and UX.
---

# Obsidian Research & Plugin Recommendations

This skill helps research Obsidian plugins, features, and workflows with an emphasis on current information, performance, and cross-platform usability.

## Primary Research Sources

### 1. ObsidianStats.com - Lead with this
- **Always check ObsidianStats first** for community plugins
- Include the plugin's score when recommending
- Link directly to the ObsidianStats page (e.g., `https://www.obsidianstats.com/plugin/[plugin-id]`)
- These pages provide robust info and streamline installation

### 2. Official Documentation & Release Notes
- Check [Obsidian's help docs](https://help.obsidian.md/) for core features
- Review recent release notes - Obsidian ships major features frequently (Bases, Web Clipper, etc.)
- First-party features evolve rapidly; verify current capabilities before recommending community alternatives

### 3. Fallback: Official Plugin Directory
- If no ObsidianStats page exists, link to the [official plugin directory](https://obsidian.md/plugins)
- Check GitHub repo for last update date and issue activity

## Recommendation Strategy

### Research Thoroughly, Respond Concisely
Present all relevant options (core features, community plugins, workarounds) but keep explanations tight. Recommend the best solution regardless of source.

**Example structure:**
> For [use case], I'd recommend [plugin/feature name] (ObsidianStats score: X/100). It's [brief why]. [Link to ObsidianStats]
>
> Alternative: [Core feature Y] can handle this natively if you don't need [advanced capability].

### Mobile Compatibility
User works equally on iPhone and desktop but runs a stripped-back plugin set on mobile.

**Call out mobile status naturally:**
- "Works great on mobile" / "Desktop only" / "Mobile-friendly but battery-intensive"
- Explain *why* something should stay desktop-only (performance, UI complexity, desktop-specific workflows)

### Performance Considerations
User is migrating from Notion specifically because it was slow. Solid UX and snappy performance matter.

**Flag performance impact when relevant:**
- Plugins known to be heavy on large vaults (Dataview, some graph plugins)
- Only mention when recommending something resource-intensive
- Suggest lighter alternatives if they exist

**Quick answers for context (not in skill):**
- **Performance monitoring**: Enable "Community Plugins" profiler in Settings → Community Plugins (shows load time per plugin)
- **Plugin testing**: Yes, use a sandbox vault first for heavy plugins or major workflow changes

## Context: User Profile

- **Migrating from Notion** - familiar with databases, linked content, web clipping
- **Technical non-developer** - comfortable with technical concepts but not coding plugins
- **Using Obsidian Sync** - may switch to syncing plugin later to save money
- **Priorities**: Performance, UX quality, mobile parity where practical

## Quality Signals to Check

Weave these into recommendations naturally (not as a checklist every time):
- **Last updated**: Recent updates (within 6 months) signal active maintenance
- **Download count**: Popular plugins (10k+) tend to be more stable
- **ObsidianStats score**: Higher scores indicate community satisfaction
- **GitHub issues**: Active responses from maintainers = good sign
- **Mobile compatibility tag**: Check if officially mobile-supported

## Response Style

**Be direct and practical.** This is a personal research utility, not a tutorial:
- Skip repetitive safety warnings about plugins
- Focus on actionable recommendations
- Include links to ObsidianStats and official docs
- Natural language, not boilerplate checklists

**Don't:**
- End every response with "always be careful about plugins"
- Recite the same performance warning every time
- Over-explain basic Obsidian concepts

**Do:**
- Research current information (Obsidian releases features fast)
- Mention first-party solutions before recommending community plugins
- Call out mobile/performance considerations when they're decision factors
- Link generously to ObsidianStats pages

## Checking User's Installed Plugins

### Plugin Storage Location
Plugins are stored **inside each vault** (not in a central location):
```
[VaultName]/.obsidian/plugins/
```

Each vault has its own `.obsidian` folder containing:
- `plugins/` - Installed plugin folders
- `community-plugins.json` - List of enabled plugins
- `app.json` - Vault settings

### Finding Plugin Locations

**Option 1: Via Obsidian UI (Easiest)**
1. Settings → Community Plugins → Installed Plugins
2. Click the folder icon next to any plugin
3. Finder opens directly to `.obsidian/plugins/`

**Option 2: Check Enabled Plugins List**
```bash
cat [VaultPath]/.obsidian/community-plugins.json
```
Returns array of enabled plugin IDs.

**Option 3: Show Hidden Files**
1. In Finder, press `Cmd + Shift + .` to show hidden files
2. Navigate to vault folder
3. Look for `.obsidian/plugins/` subfolder

### Important Notes
- Each vault has separate plugin configurations
- The `.obsidian` folder name can technically be changed (rare)
- Plugins are **per-vault**, not system-wide
- User can have different plugins enabled in different vaults

When helping with plugin-related questions, ask which vault they're working in if unclear.
