---
title: Whitelisted Domains
description: >
  How domain whitelisting controls where Waulter collects statistics,
  and what it does and does not affect.
---

# Whitelisted Domains

Whitelisted Domains is a per-configuration setting that controls **which domains are counted in your statistics dashboard**. It is one of the most commonly misunderstood features in Waulter — so let's start with what it does and does not do.

## What whitelisting does

- Consent events from **whitelisted domains** appear in your [Statistics](../dashboard/statistics.md) dashboard
- Only whitelisted domains contribute to your consent rate reports, banner impression counts, and per-purpose analytics

## What whitelisting does NOT do

- Whitelisting does **not** prevent the banner from appearing on non-whitelisted domains
- The SDK **still initialises** and **signals GCM** on non-whitelisted domains
- Whitelisting is **not a security mechanism** — it is a statistics filter

!!! warning "Common misconception"
    Many implementers assume that a domain must be whitelisted before Waulter will "work" on it. **This is incorrect.** The consent banner renders on any domain that loads the SDK with a valid Configuration ID (CCID). The banner works, consent is collected, and GCM signals are sent — regardless of whitelisting. The only difference is whether the interaction appears in your statistics.

## When to use whitelisting

### Production domains
Always whitelist your production domains. This ensures your statistics dashboard reflects real visitor consent data.

### Staging and development domains
**Do not whitelist** your staging or development domains in your production configuration. Dev/test traffic would pollute your consent rate statistics with non-representative data.

Instead, use a separate [DEV configuration](../good-practices/dev-testing.md) with its own whitelist that includes only your dev domains. This gives you clean statistics for both environments.

### Multiple domains under one configuration
If your configuration serves multiple domains (e.g. `example.com`, `shop.example.com`, `blog.example.com`), whitelist all of them to get combined statistics across your domain portfolio.

## Configuring whitelisted domains

In the Waulter dashboard:

1. Navigate to your configuration
2. Open the **Settings** or **Domains** section
3. Add each domain you want to track in statistics
4. Save the configuration

### Domain format

| Format | Example | Matches |
|--------|---------|---------|
| Exact domain | `example.com` | Only `example.com` |
| Subdomain | `shop.example.com` | Only `shop.example.com` |
| Wildcard | `*.example.com` | All subdomains of `example.com` |

!!! tip "Include both root and www"
    If your site is accessible on both `example.com` and `www.example.com`, add both to the whitelist (or use the wildcard pattern).

## Whitelisting and scenarios

When using [Scenarios](../dashboard/scenarios.md), each configuration referenced by a scenario rule has its own whitelist. This means:

- The **fallback configuration** may whitelist `example.com`
- A **rule-matched configuration** may whitelist `promo.example.com`
- Statistics for each configuration are tracked independently

## Whitelisting and User Sharing

If you use [User Sharing](user-sharing.md) to grant team members access to configurations, the whitelist is part of the configuration settings they can view (with `read` role) or modify (with `admin` role).

## Impact on statistics

| Domain status | Banner shows? | GCM signals? | Counted in statistics? |
|--------------|---------------|-------------|----------------------|
| Whitelisted | Yes | Yes | **Yes** |
| Not whitelisted | Yes | Yes | **No** |
| No SDK loaded | No | No | No |

This distinction is important for interpreting your [Statistics dashboard](../dashboard/statistics.md) — if you see lower-than-expected numbers, check whether all production domains are whitelisted.

## Best practices

1. **Always whitelist production domains** — otherwise you have no visibility into consent rates
2. **Never whitelist dev/staging in PROD config** — use a separate [DEV configuration](../good-practices/dev-testing.md) instead
3. **Audit your whitelist periodically** — when you add new subdomains or microsites, add them to the whitelist
4. **Use naming conventions** — pair whitelisting with [naming conventions](../good-practices/naming-conventions.md) so it's obvious which config tracks which domains

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| No statistics appearing | Domain not whitelisted | Add your domain to the configuration's whitelist |
| Banner works but no stats | Typo in whitelisted domain | Check for exact match (including `www.` vs bare domain) |
| Dev traffic in production stats | Dev domain whitelisted in PROD config | Remove dev domains from PROD whitelist; use DEV config instead |
| Statistics from unexpected domain | Wildcard whitelist too broad | Use specific domain entries instead of `*.domain.com` |
