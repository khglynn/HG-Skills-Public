# Code & Context Hygiene Check

*Last updated: 2025-12-15*

A periodic cleanup workflow for both code and Claude instructions. Run before major features or after time away from a project.

## Trigger Phrases

**Code hygiene:**
- "Do a codebase hygiene check"
- "Check for cruft before we begin"
- "Clean house before [feature] work"

**Instruction file hygiene:**
- "Audit my Claude instructions"
- "Check CLAUDE.md files for cruft"
- "Trim instruction file bloat"
- "Look for contradictions in my instructions"

## Tools to Run

### 1. Dependency Analysis (npm projects)

```bash
# Find unused dependencies
npx depcheck

# Find orphaned files and dead exports
npx unimported

# Check bundle bloat (optional, slow)
npx cost-of-modules
```

### 2. Large File Detection

```bash
# Images over 500KB
find . -type f \( -name "*.png" -o -name "*.jpg" -o -name "*.svg" -o -name "*.gif" \) -size +500k -not -path "./node_modules/*" -not -path "./.next/*"

# Any file over 1MB in src/
find ./src -type f -size +1M 2>/dev/null

# Large files in public/
find ./public -type f -size +500k 2>/dev/null
```

### 3. Build Artifacts & Temp Files

```bash
# Common stray artifacts
ls -la .next/ 2>/dev/null && echo "⚠️  .next/ exists (normal for dev)"
ls -la .playwright-mcp/ 2>/dev/null && echo "⚠️  .playwright-mcp/ screenshots exist"
find . -name "*.log" -not -path "./node_modules/*" 2>/dev/null
find . -name ".DS_Store" -not -path "./node_modules/*" 2>/dev/null
```

### 4. Git Status Review

```bash
# Uncommitted changes
git status

# Untracked files piling up
git status --porcelain | grep "^??" | wc -l

# Branches that might be stale
git branch -v
```

### 5. Duplicate Asset Detection

```bash
# Multiple versions of same file type in public/
ls -la public/*.png public/*.ico public/*.svg 2>/dev/null

# Check for duplicate icons (common issue)
find . -name "apple-touch-icon*" -not -path "./node_modules/*" 2>/dev/null
find . -name "favicon*" -not -path "./node_modules/*" 2>/dev/null
```

## What to Look For

| Issue | Action |
|-------|--------|
| Unused dependencies | `npm uninstall <package>` |
| Orphaned files | Delete or integrate |
| Large images (>500KB) | Archive to `~/Desktop/{project}-originals/` |
| Stray screenshots | Delete (move to Trash via Finder) |
| Build artifacts in git | Add to `.gitignore` |
| Duplicate assets | Keep one, delete others |
| Uncommitted changes | Commit or discard |

## Claude-Specific Checks

1. **Orphaned components** - Search for components not exported in index.ts or imported anywhere
2. **Dead exports** - Exports that nothing imports
3. **Outdated documentation** - CLAUDE.md references to deleted files/components
4. **Test remnants** - Leftover test pages, console.logs, debug code

## Safe Deletion (Kevin's Rule)

**Never use `rm` directly.** Always use Finder via osascript:

```bash
# Single file
osascript -e 'tell application "Finder" to delete POSIX file "/path/to/file"'

# Folder
osascript -e 'tell application "Finder" to delete POSIX file "/path/to/folder"'
```

## Archive vs Delete

| Situation | Action |
|-----------|--------|
| Source assets (PSDs, large SVGs) | Archive to `~/Desktop/{project}-originals/` |
| Test screenshots | Delete (Trash) |
| Old code (might need later) | Git history has it, delete |
| Config files | Keep unless truly orphaned |

## Output Format

After running hygiene check, report:

```
## Hygiene Check Results

### Dependencies
- [ ] X unused dependencies found
- [ ] Y orphaned files found

### Large Files
- [ ] List any files >500KB

### Git Status
- [ ] X uncommitted files
- [ ] Y untracked files

### Recommendations
1. First action item
2. Second action item
...
```

## When to Run

- **Before starting major features** - Clean slate
- **After time away** - Catch accumulated cruft
- **Before releases** - Ship clean
- **Monthly maintenance** - Don't let it pile up

---

## Instruction File Hygiene

Beyond code, Claude instructions accumulate cruft too. Check these periodically for clarity and context efficiency.

### Files to Audit

| Type | Locations | Purpose |
|------|-----------|---------|
| **CLAUDE.md** | Project root, `~/.claude/`, parent dirs | Project-specific instructions |
| **Skills** | `~/.mux/src/HG-Skills-*/hg-skills/` | Reusable patterns |
| **Reference docs** | `~/DevKev/tools/helper/` | Human + bot reference |

### What to Look For

**Contradictions**
- Instructions that conflict across files (e.g., one says "always X" another says "never X")
- Outdated rules that no longer apply to current architecture
- Duplicate guidance that's drifted apart over time

**Cruft**
- References to deleted files, removed features, or old patterns
- "TODO" items that were completed but never removed
- Workarounds for bugs that were fixed
- Instructions for tools/services no longer in use

**Verbosity**
- Multi-paragraph explanations where a table or one-liner would do
- Repeated instructions across multiple files (consolidate to single source)
- Step-by-step guides for things Claude already knows
- Over-specified edge cases that rarely occur

**Outdated Prompting Patterns**
- Explicit "chain of thought" instructions (modern models do this naturally)
- "Take a deep breath" or similar outdated prompt tricks
- Overly prescriptive step sequences (Claude works better with goals than scripts)
- Unnecessary persona descriptions beyond what affects output

### Audit Process

1. **List all instruction files** in the project ecosystem:
   ```bash
   # Project CLAUDE.md files
   find ~/DevKev -name "CLAUDE.md" -type f 2>/dev/null

   # Skills
   ls ~/.mux/src/HG-Skills-*/hg-skills/**/*.md 2>/dev/null

   # Helper docs
   ls ~/DevKev/tools/helper/*.md 2>/dev/null
   ```

2. **Check for contradictions** - Read related files together, flag conflicts

3. **Measure context cost** - Long instructions eat context that could go to conversation:
   ```bash
   # Approximate token count (words ÷ 0.75)
   wc -w CLAUDE.md | awk '{print "~" int($1/0.75) " tokens"}'
   ```

4. **Propose consolidation** - If the same info appears in 3+ places, suggest single source of truth

### Trimming Guidance

| Keep | Trim |
|------|------|
| Hard-won learnings from debugging | Generic best practices Claude knows |
| Project-specific quirks and gotchas | Explanations of standard tools |
| Decisions that took research to make | Obvious instructions ("write clean code") |
| Integration details (API keys, endpoints) | Step-by-step for common tasks |
| Style/voice guidance that shapes output | Verbose rationale for simple rules |

### Preservation Principle

**Don't delete learnings, compress them.**

If something was added because of a real problem, keep the insight but trim the explanation:

```markdown
# Before (verbose)
When working with the Stripe webhook endpoint, always verify the signature
first before processing. We learned this the hard way when a bug caused
duplicate charges because we were processing webhooks without verification.
The verification happens in the webhookHandler function and uses the
STRIPE_WEBHOOK_SECRET environment variable.

# After (compressed)
Stripe webhooks: Always verify signature first (webhookHandler uses STRIPE_WEBHOOK_SECRET).
Previous bug: duplicate charges from unverified webhooks.
```

### Output Format

```
## Instruction File Audit

### Files Reviewed
- [ ] List files checked

### Contradictions Found
- [ ] File A says X, File B says Y

### Cruft Identified
- [ ] Outdated reference in File C, line N

### Consolidation Opportunities
- [ ] Topic X appears in 3 files, suggest single source

### Trimming Suggestions
- [ ] Section in File D could be compressed (current: ~500 tokens)

### Preserved Learnings
- [ ] Important insight kept, just reformatted
```
