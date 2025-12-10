---
title: Tech Stack Setup Patterns
description: Common patterns for setting up monitoring, auth, and infrastructure across Kevin's web projects.
---

# Tech Stack Setup Patterns

Reference for consistent setup of monitoring, auth, and infrastructure across projects.

## Monitoring & Error Tracking

### PostHog (Analytics + Session Replay)

**Purpose:** User behavior analytics, session recordings, feature flags

**Packages:**
```bash
npm install posthog-js posthog-node
```

**Env vars (with Vercel descriptions):**
| Variable | Value | Vercel Description |
|----------|-------|-------------------|
| `NEXT_PUBLIC_POSTHOG_KEY` | `phc_...` | PostHog project API key |
| `NEXT_PUBLIC_POSTHOG_HOST` | `https://us.i.posthog.com` | PostHog API host (US region) |

**Client-side setup (`app/providers.tsx`):**
```typescript
import posthog from 'posthog-js'
import { PostHogProvider } from 'posthog-js/react'

if (typeof window !== 'undefined' && process.env.NEXT_PUBLIC_POSTHOG_KEY) {
  posthog.init(process.env.NEXT_PUBLIC_POSTHOG_KEY, {
    api_host: process.env.NEXT_PUBLIC_POSTHOG_HOST || 'https://us.i.posthog.com',
    person_profiles: 'identified_only',
    capture_pageview: true,
    capture_pageleave: true,
  })
}
```

**Server-side setup (`src/lib/posthog-server.ts`):**
```typescript
import { PostHog } from 'posthog-node'

let posthogClient: PostHog | null = null

export function getPostHogClient(): PostHog | null {
  if (!process.env.NEXT_PUBLIC_POSTHOG_KEY) return null
  if (!posthogClient) {
    posthogClient = new PostHog(process.env.NEXT_PUBLIC_POSTHOG_KEY, {
      host: process.env.NEXT_PUBLIC_POSTHOG_HOST || 'https://us.i.posthog.com',
      flushAt: 1,
      flushInterval: 0,
    })
  }
  return posthogClient
}
```

**Clerk user sync:**
```typescript
function PostHogUserIdentifier() {
  const { user, isLoaded } = useUser()
  useEffect(() => {
    if (!isLoaded) return
    if (user) {
      posthog.identify(user.id, {
        email: user.primaryEmailAddress?.emailAddress,
        name: user.fullName,
      })
    } else {
      posthog.reset()
    }
  }, [user, isLoaded])
  return null
}
```

**Dashboard:** https://us.posthog.com

---

### Sentry (Error Tracking + Performance)

**Purpose:** Application errors, stack traces, performance monitoring

**Package:**
```bash
npx @sentry/wizard@latest -i nextjs
```

If wizard fails (TTY issues), manually install:
```bash
npm install @sentry/nextjs
```

**Env vars (with Vercel descriptions):**
| Variable | Value | Vercel Description |
|----------|-------|-------------------|
| `NEXT_PUBLIC_SENTRY_DSN` | `https://...@sentry.io/...` | Sentry DSN for error reporting |
| `SENTRY_AUTH_TOKEN` | `sntrys_...` | Sentry auth token for source maps |

**Files to create:**

1. `sentry.client.config.ts`:
```typescript
import * as Sentry from "@sentry/nextjs";
Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  tracesSampleRate: 1,
  replaysSessionSampleRate: 0.1,
  replaysOnErrorSampleRate: 1.0,
  integrations: [
    Sentry.replayIntegration({ maskAllText: true, blockAllMedia: true }),
  ],
});
```

2. `sentry.server.config.ts` and `sentry.edge.config.ts` - similar but no replay

3. `instrumentation.ts`:
```typescript
export async function register() {
  if (process.env.NEXT_RUNTIME === 'nodejs') {
    await import('./sentry.server.config');
  }
  if (process.env.NEXT_RUNTIME === 'edge') {
    await import('./sentry.edge.config');
  }
}
```

4. `app/global-error.tsx` - React error boundary with Sentry capture

5. Update `next.config.js`:
```javascript
const { withSentryConfig } = require("@sentry/nextjs");
module.exports = withSentryConfig(nextConfig, {
  org: "khg-y1",
  project: "project-name",
  widenClientFileUpload: true,
  hideSourceMaps: true,
  disableLogger: true,
});
```

**Dashboard:** https://sentry.io (org: khg-y1)

---

## Why Different Error Tools?

Each tool catches errors at a different layer:

| Tool | Layer | Catches |
|------|-------|---------|
| **Sentry** | Application code | JS errors, unhandled exceptions, API failures, stack traces |
| **PostHog** | User experience | Session recordings when errors occur, user journey to error |
| **Vercel** | Infrastructure | Build failures, serverless timeouts, edge errors |
| **Neon** | Database | Query errors, connection issues, slow queries |

They overlap intentionally - Sentry for fixing bugs, PostHog for understanding user impact.

---

## Authentication & Database

### Clerk (Auth)

**Package:**
```bash
npm install @clerk/nextjs
```

**Env vars (with Vercel descriptions):**
| Variable | Value | Vercel Description |
|----------|-------|-------------------|
| `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` | `pk_...` | Clerk publishable key (safe for client) |
| `CLERK_SECRET_KEY` | `sk_...` | Clerk secret key (server only) |

**Dashboard:** https://dashboard.clerk.com

### Neon (Postgres)

**Package:**
```bash
npm install @neondatabase/serverless
```

**Env vars (with Vercel descriptions):**
| Variable | Value | Vercel Description |
|----------|-------|-------------------|
| `DATABASE_URL` | `postgres://...@...neon.tech/...` | Neon Postgres connection string |

**Dashboard:** https://console.neon.tech

---

## Hosting & Infrastructure

### Vercel
- Auto-deploys from GitHub
- Preview deployments on PRs
- Add env vars in Project Settings > Environment Variables

### Cloudflare
- DNS management for most domains
- Workers for edge compute
- R2 for object storage

### Squarespace
- DNS for kevinhg.com

### Hover
- DNS for specialty domains (khg.llc, etc.)

---

## Standard Event Names

When tracking with PostHog, use consistent event names:

| Event | When | Key Properties |
|-------|------|----------------|
| `research_started` | User submits query | query_length, model_count |
| `research_completed` | Results returned | duration_ms, total_cost |
| `follow_up_submitted` | Follow-up query | conversation_length |
| `download_started` | Export action | format, item_count |
| `session_reset` | New conversation | conversation_length |
| `error_occurred` | Caught error | error_type, context |

---

## Checklist for New Projects

- [ ] Add PostHog (client + server)
- [ ] Add Sentry (client + server + edge + error boundary)
- [ ] Add Clerk (if auth needed)
- [ ] Add Neon (if database needed)
- [ ] Set env vars locally (`.env.local`)
- [ ] Set env vars in Vercel dashboard (with descriptions)
- [ ] Create Sentry project (one per app)
- [ ] Verify Slack alerts connected in Sentry

---

## Documenting New Tools

When adding a new tool to the stack:

1. **Update this skill** with setup patterns, env vars (including Vercel descriptions), and dashboard links
2. **Update KEVIN-TIPS.md** with quick link in Service Dashboards table
3. **Update project CLAUDE.md** if tool is project-specific

Keep this skill as the detailed reference; KEVIN-TIPS stays brief (just links and one-liners).
