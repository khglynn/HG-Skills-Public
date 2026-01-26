# Scrape Exercise Library

*Created: 2026-01-25*

Scrape exercise libraries from equipment manufacturer sites or fitness databases using Firecrawl.

## When to Use

- Downloading exercise lists from equipment manufacturers (Tonal, Freak Athlete, TRX, etc.)
- Building reference CSVs for workout conversion projects
- Capturing paginated content that simple web fetch can't handle

## Firecrawl Agent Approach (Recommended)

The `firecrawl_agent` tool is the most effective for paginated exercise libraries. It autonomously navigates and extracts structured data.

### Example: Tonal Movement Library

```json
{
  "name": "mcp__firecrawl__firecrawl_agent",
  "arguments": {
    "urls": ["https://tonal.com/blogs/movements"],
    "prompt": "Extract the COMPLETE list of all exercises from Tonal's movement library. The page is paginated with a 'Load More' button - load ALL exercises. For each exercise, capture: name, category (upper/lower/core), and equipment type (handles, rope, bar, ankle straps, etc).",
    "schema": {
      "type": "object",
      "properties": {
        "exercises": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "name": {"type": "string"},
              "category": {"type": "string"},
              "equipment": {"type": "string"}
            },
            "required": ["name"]
          }
        },
        "total_count": {"type": "number"}
      }
    }
  }
}
```

**Result:** 257 exercises captured (vs 24 from simple scrape)

### Example: TRX Exercises (Fitbod)

```json
{
  "name": "mcp__firecrawl__firecrawl_agent",
  "arguments": {
    "urls": ["https://fitbod.me/exercises/trx"],
    "prompt": "Extract ALL TRX exercises from this page. For each exercise, capture: name, primary muscle group, difficulty level, and any description/notes.",
    "schema": {
      "type": "object",
      "properties": {
        "exercises": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "name": {"type": "string"},
              "muscle_group": {"type": "string"},
              "difficulty": {"type": "string"},
              "notes": {"type": "string"}
            },
            "required": ["name"]
          }
        },
        "total_count": {"type": "number"}
      }
    }
  }
}
```

**Result:** 61 exercises captured

## Why Firecrawl Agent Works

| Approach | Pagination | JS Content | Result |
|----------|------------|------------|--------|
| `WebFetch` | ❌ | ❌ | First page only |
| `firecrawl_scrape` | ❌ | ✅ | First page only |
| `firecrawl_extract` | ❌ | ✅ | First page only |
| `firecrawl_agent` | ✅ | ✅ | Full library |

The agent autonomously:
- Clicks "Load More" buttons
- Navigates paginated content
- Handles JavaScript-rendered pages
- Returns structured data per your schema

## Alternative: Find Better Sources

Sometimes the official site is hard to scrape. Search for alternative sources:

1. Launch an Explore agent to find alternatives:
   ```
   "Search for comprehensive [EQUIPMENT] exercise lists online. Find sources with 50+ exercises that include names and muscle groups. Compare scrapability."
   ```

2. Common alternative sources:
   - **Fitbod** - Clean structure, includes muscle groups
   - **ExRx.net** - Comprehensive but complex structure
   - **Equipment manufacturer PDFs** - Sometimes easier than web

## Output Format

Save as timestamped CSV in `data/exercise-sources/`:

```
{equipment}-exercises-{YYYY-MM-DD}.csv
```

Columns:
- `exercise_name` - Primary identifier
- `muscle_group` or `category` - Target muscles
- `equipment` - Attachment type (handles, bar, straps)
- `difficulty` - If available
- `source` - URL for reference
- `notes` - Additional details

## Lessons Learned

1. **Start with firecrawl_agent** - Don't waste time on simpler approaches for paginated content
2. **Use specific schemas** - Tell the agent exactly what fields you want
3. **Search for alternatives** - Official sites aren't always the best source
4. **Timestamp everything** - Equipment manufacturers add exercises over time
5. **Save the source URL** - For future re-scraping
