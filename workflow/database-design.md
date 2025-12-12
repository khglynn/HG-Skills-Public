# Database Design Patterns

Kevin's preferred approach for analytics-friendly, human-readable database schemas.

**Created:** December 2024
**Source:** Eachie analytics schema redesign

---

## Core Principles

1. **Human-readable names** - `user_id` not `uid`, `created_at` not `createdAt`
2. **Flat tables for analytics** - Avoid JSONB for queryable data
3. **Denormalize for readability** - Store `model_name` alongside `model_id`
4. **Fact + Dimension tables** - Kimball star schema for analytics
5. **Storage is cheap** - Don't over-optimize; prioritize queryability
6. **Units in column names** - `total_cost_cents` not `cost`, `duration_ms` not `duration`

---

## Table Types

### Fact Tables (Events/Measurements)
- Each row = one event/measurement at atomic grain
- Include foreign keys to dimensions
- Include pre-computed aggregates for common queries
- Timestamp every row with `created_at`
- Examples: `research_queries`, `model_calls`, `payments`

### Dimension Tables (Descriptive Attributes)
- Reference data that changes slowly
- Include human-readable labels
- Use for filtering and grouping
- Prefix with `dim_` for clarity
- Examples: `dim_models`, `dim_dates`, `dim_users`

---

## Column Naming Conventions

| Type | Pattern | Examples |
|------|---------|----------|
| Timestamps | `*_at` | `created_at`, `updated_at`, `retired_at` |
| Foreign keys | `*_id` | `user_id`, `session_id`, `research_query_id` |
| Booleans | `is_*` or `has_*` | `is_active`, `has_attachments`, `used_default_models` |
| Counts | `*_count` | `attachment_count`, `failure_count` |
| Amounts | Include units | `total_cost_cents`, `duration_ms`, `price_per_million` |
| Arrays | Plural | `selected_model_ids`, `attachment_extensions` |

---

## Index Strategy

- Index all foreign keys
- Index columns used in WHERE clauses
- Index timestamps for time-range queries
- Use GIN indexes for array columns (PostgreSQL)
- Name indexes: `idx_{table}_{column}`

---

## Error Tracking Pattern

Always track errors with:
- `error_code` - Standardized code (maps to messages.ts)
- `error_message` - Raw text for debugging
- `error_type` - Category: `user_error`, `system_error`, `model_error`

---

## Data Retention Pattern

| Age | Granularity | Storage |
|-----|-------------|---------|
| 0-6 months | Full detail | Main fact tables |
| 6+ months | Monthly rollup | `*_monthly` aggregate tables |

Rollup tables preserve insights without storing millions of rows.

---

## Analytics-First Design

When adding features, always ask: **"What questions might we want to answer about this later?"**

### Common Question Categories

1. **Funding/Investor metrics** - MAU/DAU, growth rate, conversion, LTV, churn
2. **Product direction** - Feature popularity, user preferences, error rates
3. **User behavior** - Session patterns, drop-off points, engagement depth
4. **Revenue/costs** - Unit economics, margin by feature, cost trends

### Tool Responsibilities

| Tool | Primary Purpose |
|------|-----------------|
| **Database** | Transactional data, costs, model usage, user accounts |
| **PostHog** | In-app behavior, funnels, session replays, traffic sources (Web Analytics) |
| **Stripe** | Revenue, subscriptions, payment analytics |
| **Canny** | Feature requests, user feedback |

Note: PostHog Web Analytics covers traffic sources, referrers, UTM tracking. Only add GA4 if SEO/paid marketing becomes priority.

---

## Example: Adding Analytics for a New Feature

If adding "scheduled research" feature:

```sql
-- New fact table
CREATE TABLE scheduled_queries (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id VARCHAR(64) REFERENCES users(id),
  device_id VARCHAR(64),

  -- Feature-specific columns
  schedule_cron VARCHAR(100),           -- e.g., '0 9 * * 1' (Mondays at 9am)
  query_text TEXT NOT NULL,
  selected_model_ids TEXT[],

  -- State tracking
  is_active BOOLEAN DEFAULT TRUE,
  last_run_at TIMESTAMP WITH TIME ZONE,
  next_run_at TIMESTAMP WITH TIME ZONE,

  -- Analytics (aggregated for quick queries)
  total_runs INTEGER DEFAULT 0,
  total_cost_cents INTEGER DEFAULT 0,

  -- Standard columns
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE INDEX idx_scheduled_queries_user ON scheduled_queries(user_id);
CREATE INDEX idx_scheduled_queries_next ON scheduled_queries(next_run_at) WHERE is_active = TRUE;
```

Then add PostHog events for behavioral tracking:
- `schedule_created`
- `schedule_triggered`
- `schedule_paused`

---

## Quick Reference: SQL Patterns

### Cost in cents (avoids floating point issues)
```sql
total_cost_cents INTEGER  -- Store $12.34 as 1234
```

### Timestamp with timezone
```sql
created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
```

### Boolean flags
```sql
is_active BOOLEAN DEFAULT TRUE
has_attachments BOOLEAN DEFAULT FALSE
used_default_models BOOLEAN  -- Note: not `is_*` because it's past tense
```

### Array columns (PostgreSQL)
```sql
selected_model_ids TEXT[]
attachment_extensions TEXT[]

-- Query with:
WHERE 'anthropic/claude-3.5-sonnet' = ANY(selected_model_ids)

-- Index with GIN:
CREATE INDEX idx_queries_models ON research_queries USING GIN(selected_model_ids);
```

### Date dimension join
```sql
SELECT
  d.month_name,
  d.is_weekend,
  COUNT(*) as queries
FROM research_queries rq
JOIN dim_dates d ON DATE(rq.created_at) = d.date_key
GROUP BY d.month_name, d.is_weekend;
```

---

## Privacy & Compliance Patterns

**Full guide:** `~/DevKev/tools/helper/guides/app-compliance-privacy.md`

### Privacy Tiers Pattern

When users have different billing modes, create privacy tiers:

| Tier | What's Stored | Who Gets It |
|------|---------------|-------------|
| **Full** | Everything (queries, responses) | Users you subsidize or who pay you |
| **Analytics Only** | Metadata only (counts, costs) | BYOK / bring-your-own-resource users |
| **Errors Only** | Just error tracking | Privacy-maximalist opt-in |

### When Storing User Content

1. **Disclose in privacy policy** - List exactly what you store
2. **Add `tracking_level`** - Column to track user preference: `full`, `analytics_only`, `errors_only`
3. **Respect preference** - Check before storing text content
4. **Enable deletion** - CASCADE constraints for "Delete My Data"

### Update Privacy Docs When:

- Adding new database columns with user content
- Adding analytics tools (PostHog, Sentry, etc.)
- Changing retention policies
- Adding third-party integrations

**Files to update:** `app/privacy/page.tsx`, `app/terms/page.tsx`, project CLAUDE.md

---

## Payment & Billing Patterns

### Auto Top-Up Pattern (Usage-Based Billing)

For apps that charge based on usage (like API providers), use the auto top-up pattern:

```sql
-- User billing columns
ALTER TABLE users ADD COLUMN credits_cents INTEGER DEFAULT 0;
ALTER TABLE users ADD COLUMN total_spent_cents INTEGER DEFAULT 0;
ALTER TABLE users ADD COLUMN stripe_customer_id VARCHAR(64);
ALTER TABLE users ADD COLUMN stripe_payment_method_id VARCHAR(64);
ALTER TABLE users ADD COLUMN auto_topup_enabled BOOLEAN DEFAULT FALSE;
ALTER TABLE users ADD COLUMN auto_topup_threshold_cents INTEGER DEFAULT 600;  -- $6
ALTER TABLE users ADD COLUMN auto_topup_amount_cents INTEGER DEFAULT 2400;    -- $24
```

### The Flow

1. **User saves card** → `SetupIntent` → store `stripe_payment_method_id`
2. **User makes requests** → deduct from `credits_cents`
3. **Balance drops below threshold** → auto-charge saved card via `PaymentIntent`
4. **Webhook confirms** → add credits back to balance

### Stripe Objects

| Object | When to Use |
|--------|-------------|
| `SetupIntent` | Save payment method without charging |
| `PaymentIntent` | Charge a saved payment method |
| `Customer` | Store payment methods, link to your user |

### Webhook Events to Handle

```typescript
// Webhook handler structure
switch (event.type) {
  case 'setup_intent.succeeded':
    // Save payment method to database
    // Enable auto top-up
    break;
  case 'payment_intent.succeeded':
    // Add credits (read amount from metadata)
    break;
  case 'payment_intent.payment_failed':
    // Log failure, maybe notify user
    break;
}
```

### Important: Store IDs in Metadata

When creating PaymentIntents, include your user ID in metadata so webhooks can link back:

```typescript
stripe.paymentIntents.create({
  amount: 2400,
  currency: 'usd',
  customer: customerId,
  payment_method: paymentMethodId,
  metadata: {
    userId: clerkUserId,      // Your internal user ID
    creditsCents: '2400',     // What to credit on success
    type: 'auto_topup',       // For analytics
  },
})
```

### Reference Implementation

See Eachie's Stripe setup for a complete example:
- `src/lib/stripe.ts` - SetupIntent + PaymentIntent functions
- `app/api/webhooks/stripe/route.ts` - Webhook handler
- `src/server/queries/users.ts` - Credit management functions
