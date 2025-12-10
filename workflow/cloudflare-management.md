---
title: Cloudflare Management
description: Careful workflow for managing domains, DNS, Workers, and security settings in Cloudflare. Always explain before making changes.
---

# Cloudflare Management

This skill defines guardrails for working with Cloudflare via MCP or dashboard.

## Core Principle: Explain Before Acting

Cloudflare changes affect live sites. **Always explain what you're about to do and why before making changes.**

## DNS Changes

### Before ANY DNS modification:
1. Explain what the current record does
2. Explain what the change will do
3. Warn about propagation time (can take up to 48 hours)
4. Ask for confirmation on destructive changes (deleting records)

### Safe Operations (proceed with explanation):
- Adding new records
- Viewing current configuration
- Checking propagation status

### Dangerous Operations (require explicit confirmation):
- Deleting DNS records
- Changing nameservers
- Modifying A/AAAA records for root domain
- Changing proxy status (orange cloud on/off)

## Workers & Pages

### Before deploying:
1. Explain what the Worker/Page does
2. Note which routes it affects
3. Confirm deployment target (staging vs production)

### Rollback awareness:
- Always note that previous versions can be restored
- Link to deployment history when relevant

## Security Settings

### Bot Protection & AI Crawlers
- This is often why Kevin uses Cloudflare
- Explain the difference between blocking and challenging
- Note that overly aggressive rules can block legitimate users

### WAF Rules
- Explain each rule in plain language before enabling
- Start with "Log" mode before "Block" mode when possible

## Domain Transfers

When helping Kevin move domains TO Cloudflare:

1. **Preparation checklist:**
   - Unlock domain at current registrar (Hover)
   - Get auth/EPP code from current registrar
   - Disable WHOIS privacy temporarily (some registrars require this)
   - Ensure domain doesn't expire within 60 days

2. **During transfer:**
   - Explain each step before taking it
   - Note that transfers can take 5-7 days
   - Confirm DNS records are backed up first

3. **After transfer:**
   - Verify all DNS records migrated correctly
   - Re-enable WHOIS privacy if desired
   - Confirm site is still accessible

## Response Format

When making Cloudflare changes:

```
## What I'm about to do
[Plain language explanation]

## Why
[Reason for the change]

## Risk level
[Low/Medium/High] - [Brief explanation]

## Proceed?
[Wait for confirmation on Medium/High risk changes]
```

## Summary

| Action | Risk | Requires Confirmation |
|--------|------|----------------------|
| View settings | Low | No |
| Add DNS record | Low | No (but explain) |
| Enable bot protection | Low | No (but explain) |
| Modify existing DNS | Medium | Yes |
| Delete DNS record | High | Yes |
| Change nameservers | High | Yes |
| Deploy Worker to production | Medium | Yes |
