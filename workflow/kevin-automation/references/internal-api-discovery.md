# Internal API Discovery

*Building your own layer on top of apps and websites by leveraging their internal APIs.*

## Table of Contents
- [When to Reach for This](#when-to-reach-for-this)
- [The Discovery Workflow](#the-discovery-workflow)
- [Auth Patterns](#auth-patterns)
- [The page.evaluate Pattern](#the-pageevaluate-pattern)
- [Building Your Layer](#building-your-layer)
- [Reference Implementation](#reference-implementation)

## When to Reach for This

Most web apps expose richer internal APIs than their public ones (if a public API even exists). The frontend needs detailed data — modifiers, defaults, conditional options, real-time state — and that data flows through internal endpoints that are just regular HTTP calls.

This approach makes sense when:
- **No public API exists** (common for smaller SaaS, local services, internal tools)
- **The public API is too limited** (missing fields, lower rate limits, no modifier/variant data)
- **You need the same data the UI shows** (if you can see it in the browser, there's an API call behind it)
- **You want to build something the app doesn't offer** (custom scoring, filtering, automation, alerts)

This is NOT reverse engineering or hacking — you're observing the HTTP calls the app already makes and scripting them. The data is yours; you're just accessing it programmatically instead of clicking.

## The Discovery Workflow

### Step 1: Open DevTools Network Tab

Open the app in Chrome/Arc, then:
- **Cmd+Opt+I** → Network tab
- Filter by **Fetch/XHR** (hides images, CSS, scripts)
- Navigate the app normally — click through pages, open modals, submit forms

### Step 2: Identify the Interesting Calls

Look for:
- **JSON responses** — these are the API calls (click a request, check the Response tab)
- **Calls that fire when you click something** — modal opens, page loads, form submits
- **Calls with rich data** — more fields than what the UI displays is a good sign
- **Consistent URL patterns** — `/api/items/{id}`, `/api/orders`, etc.

### Step 3: Document What You Find

For each useful endpoint, capture:

```markdown
## Endpoint Name
- **URL:** `GET https://app.example.com/api/items/{itemId}`
- **Returns:** Full item data including modifiers, defaults, pricing (JSON)
- **Auth:** Browser cookies (`session_id`) — no API key needed
- **Used by:** Frontend when opening item detail modal
- **Notes:** Returns more data than the list view — includes nested options
```

Save these in a `discovery/` folder in your project. They're the foundation for everything you build on top.

### Step 4: Check for Framework Clues

Many apps use frameworks that expose data in predictable places:
- **Inertia.js** (Laravel/Rails): `document.getElementById('app').dataset.page` contains JSON page props
- **Next.js**: `__NEXT_DATA__` script tag has server-side props
- **Nuxt.js**: `window.__NUXT__` has hydration data
- **React**: React DevTools can inspect component props/state

These are often richer than the API calls because they include the full page context in one payload.

## Auth Patterns

Internal APIs usually rely on the same auth the browser uses — session cookies. No API keys, no OAuth flows, just cookies.

### Persistent Playwright Profile

The cleanest pattern: use a Playwright persistent context that stores cookies between runs.

```typescript
import { chromium } from "playwright";

const PROFILE_DIR = ".playwright-profile";

// Launch with persistent profile — cookies survive between runs
const context = await chromium.launchPersistentContext(PROFILE_DIR, {
  headless: true,
});
```

**First run:** Launch headed (`headless: false`), log in manually, close the browser. The profile saves the session.

**Subsequent runs:** Launch headless — the session cookie is already there.

### Cookie Capture Script

For apps with short-lived sessions, build a login helper:

```typescript
// login.ts — run manually when session expires
const context = await chromium.launchPersistentContext(PROFILE_DIR, {
  headless: false, // Kevin logs in manually
});
const page = await context.newPage();
await page.goto("https://app.example.com/login");

console.log("Log in manually, then press Enter here...");
await new Promise(resolve => process.stdin.once("data", resolve));

// Session is now saved in the profile directory
await context.close();
```

### When Sessions Expire

- **Long-lived sessions** (days/weeks): Profile persists, rarely need to re-login
- **Short-lived sessions** (hours): Keep the login script handy, re-run as needed
- **Cookie backup:** Optionally extract the session cookie to `.env` as a fallback

## The page.evaluate Pattern

The key technique: use `page.evaluate()` to run `fetch()` inside the browser context. This automatically includes the browser's auth cookies — no manual cookie injection needed.

```typescript
const data = await page.evaluate(async (itemId) => {
  const resp = await fetch(`https://app.example.com/api/items/${itemId}`);
  return await resp.json();
}, itemId);
```

This is powerful because:
- **Auth is free** — the browser's cookies are sent automatically
- **CORS doesn't apply** — you're fetching from the same origin
- **No cookie management** — you never extract, store, or inject cookies
- **Batch multiple calls** — loop inside `page.evaluate()` to fetch many items in one context switch

### Extracting Framework Data

For apps using Inertia.js, Next.js, etc.:

```typescript
const pageProps = await page.evaluate(() => {
  // Inertia.js (Laravel)
  const el = document.getElementById("app");
  return JSON.parse(el.dataset.page);
  
  // Or Next.js
  // return JSON.parse(document.getElementById("__NEXT_DATA__").textContent);
});
```

## Building Your Layer

Once you have the internal APIs documented, the pattern is:

1. **Scrape** — Playwright fetches data on a schedule (weekly, daily, on-demand)
2. **Transform** — Normalize the raw data into your own schema
3. **Store** — Your own database (D1, SQLite, Postgres — whatever fits)
4. **Enhance** — Add your own logic: scoring, filtering, alerts, recommendations
5. **Serve** — Your own API/UI that's tailored to what you actually need

The value isn't in the raw data — it's in what you build on top that the original app doesn't offer.

## Reference Implementation

**Project:** lunch-no-drop (`~/DevKev/HGTecovas/lunch-no-drop`)

Kevin built a food intelligence app on top of LunchDrop (Austin meal delivery) by discovering three internal APIs through browser DevTools:

1. **Menu Item Modifiers API** — `GET /api/menu-items/{id}` — full modifier data the listing page doesn't show
2. **Order Submission API** — `POST /api/deliveries/{id}/order` — programmatic ordering
3. **Inertia Page Props** — extracted from DOM `data-page` attribute — item lists, delivery schedules

On top of this data, Kevin built: IBS-safe ingredient scoring (658 ingredients), macro tracking, weekly menu recommendations, and a custom UI — none of which LunchDrop offers.

**Key files to study:**
- `scripts/lunchdrop-discovery/modal-discovery.md` — the full API discovery documentation
- `scripts/lunchdrop-login.ts` — persistent profile auth pattern
- `scripts/auto-ingest.ts` — the scraping + ingestion pipeline
