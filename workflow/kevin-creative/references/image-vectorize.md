---
title: Image Vectorization
description: Convert raster images (PNG, JPG) to vector formats (SVG). Use remove.bg for background removal, Vectorizer.ai for vectorization.
---

# Image Vectorization

Convert raster images to clean vectors. Two workflows depending on whether you need background removal.

## Prerequisites

Credentials in `~/.env`:
```bash
# Vectorizer.ai - for vectorization
export VECTORIZER_AI_ID="your_id"
export VECTORIZER_AI_SECRET="your_secret"

# remove.bg - for background removal (optional)
export REMOVE_BG_API_KEY="your_key"
```

Get credentials:
- Vectorizer.ai: https://vectorizer.ai/api
- remove.bg: https://www.remove.bg/api (free: 50 preview-res/month)

## Workflow A: Vectorize Only (keep background)

Use when the image already has a transparent background or you want to keep the background.

```bash
curl -s "https://api.vectorizer.ai/api/v1/vectorize" \
  -u "$VECTORIZER_AI_ID:$VECTORIZER_AI_SECRET" \
  -F "image=@\"/path/to/input.png\"" \
  -F "output.file_format=svg" \
  -o "/path/to/output.svg"
```

## Workflow B: Remove Background + Vectorize (two steps)

Use when you need to remove a solid background before vectorizing.

**Step 1: Remove background (remove.bg)**
```bash
curl -s "https://api.remove.bg/v1.0/removebg" \
  -H "X-Api-Key: $REMOVE_BG_API_KEY" \
  -F "image_file=@\"/path/to/input.png\"" \
  -F "size=preview" \
  -o "/path/to/input-nobg.png"
```

**Step 2: Vectorize (Vectorizer.ai)**
```bash
curl -s "https://api.vectorizer.ai/api/v1/vectorize" \
  -u "$VECTORIZER_AI_ID:$VECTORIZER_AI_SECRET" \
  -F "image=@\"/path/to/input-nobg.png\"" \
  -F "output.file_format=svg" \
  -o "/path/to/input-nobg.svg"
```

## remove.bg Size Options

| Size | Resolution | Cost |
|------|------------|------|
| `preview` | ~625x400 (0.25MP) | Free tier |
| `auto` | Up to 25MP | 1 credit |
| `full` | Original size | 1 credit |

For simple graphics (logos, icons), `preview` is usually sufficient for vectorization.

## Output Formats (Vectorizer.ai)

| Format | Use Case |
|--------|----------|
| `svg` | Web, design software (recommended) |
| `pdf` | Print, documents |
| `eps` | Legacy design software |
| `dxf` | CAD software |

## Naming Convention

- Vectorized only: `logo.png` → `logo.svg`
- Background removed + vectorized: `logo.png` → `logo-nobg.svg`

## File Path Gotcha

For paths with spaces, use escaped quotes:
```bash
-F "image=@\"/path/with spaces/file.png\""
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Exit code 26 (curl) | File path has spaces - use escaped quotes |
| 401 Unauthorized | Check credentials in ~/.env |
| Background still visible | Use Workflow B (remove.bg first) |
| Low quality output | Use `size=auto` or `size=full` in remove.bg (costs 1 credit) |

## API Documentation

- Vectorizer.ai: https://vectorizer.ai/api
- remove.bg: https://www.remove.bg/api
