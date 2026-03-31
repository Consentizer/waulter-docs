---
title: Cookie Detection
description: >
  How Waulter's automated crawler detects cookies, banners, GCM state, and third-party scripts —
  and how the "master check" diff view shows what changed between crawls.
---

# Cookie Detection

Waulter includes an automated cookie detection feature that crawls your website to identify cookies, consent banners, Google Consent Mode state, and third-party scripts. Results are displayed in the dashboard so you can verify your compliance posture and catch unexpected changes.

!!! info "Observable results only"
    This documentation covers the detection **results** as they appear in the dashboard. The detection process runs automatically — you interact with the findings, not the detection mechanism.

## What the crawler detects

| Detection category | What it finds |
|-------------------|---------------|
| **Cookies** | First-party and third-party cookies set by your site and its scripts |
| **Consent banners** | Whether a consent banner is present and functional on the page |
| **GCM state** | Whether Google Consent Mode signals are being sent correctly |
| **Third-party scripts** | External scripts loaded on your pages (analytics, ads, social widgets, etc.) |

### Cookie details

For each detected cookie, the dashboard shows:

| Field | Description |
|-------|-------------|
| Name | The cookie name (e.g. `_ga`, `fbp`, `vaswaulter`) |
| Domain | Which domain set the cookie (first-party vs third-party) |
| Type | First-party or third-party |
| Category | The purpose category it belongs to (analytics, marketing, etc.) |
| Expiry | How long the cookie persists |
| Source | Which script set the cookie |

## How results appear in the dashboard

Detection results are available per configuration in the dashboard:

1. Open your configuration.
2. Navigate to the **Cookie Detection** or **Compliance** section.
3. View the latest detection results.

Results are organised by purpose category, making it easy to see which cookies belong to analytics, marketing, or other categories. Items that may need attention are flagged.

## The "master check" — diff view

The master check provides a **diff view** showing what changed between consecutive detection runs. Think of it as a "before and after" comparison for your site's cookie landscape.

### What the diff shows

| Change type | Indicator | Meaning |
|------------|-----------|---------|
| **New cookie** | Added | A cookie that was not present in the previous scan |
| **Removed cookie** | Removed | A cookie that was present before but is now gone |
| **Changed cookie** | Modified | A cookie whose properties changed (domain, expiry, etc.) |
| **New script** | Added | A third-party script that was not present before |
| **GCM change** | Modified | A change in Google Consent Mode signal behaviour |

### Why the diff view matters

- **Spot unexpected tracking** — a new deployment might introduce cookies you didn't expect
- **Verify removals** — confirm that a removed third-party integration actually stopped setting cookies
- **Catch regressions** — detect if a GCM signal that was working correctly has stopped firing
- **Compliance monitoring** — track changes over time for audit purposes

## How to use detection results

### After a deployment

1. Deploy your changes.
2. Wait for the next detection run (or trigger a manual re-scan if available).
3. Check the diff view for new or changed cookies.
4. Verify that new cookies are properly categorised and included in your consent configuration.

### Before an audit

1. Review the latest detection results.
2. Verify that all detected cookies match purposes in your configuration.
3. Check for any uncategorised cookies — these need to be assigned to a purpose or removed.
4. Export or screenshot the results as evidence.

### When adding third-party integrations

1. Add the integration to your site (e.g. a new analytics tool).
2. Run a detection scan.
3. Check what new cookies and scripts were detected.
4. Add the appropriate purpose category to your Waulter configuration if needed.
5. Update your Cookie Policy to include the new cookies.

## Common findings and actions

| Finding | Action |
|---------|--------|
| Uncategorised cookie detected | Assign it to the correct purpose category in the dashboard |
| Third-party cookie from unknown source | Investigate which script sets it; consider removing the script or adding a purpose |
| GCM signals not detected | Verify `useGtm: true` is set in your WaulterConfig |
| Missing consent banner | Check that the SDK is deployed and the configuration is active |
| Cookie set before consent | Review tag firing order — ensure tags wait for consent triggers |

## Sharing results with your team

Detection results can be used in cross-functional communication:

| Audience | What to share |
|----------|--------------|
| **DPO / Legal** | Full detection report, especially any new or uncategorised cookies |
| **Development team** | Diff view highlighting changes from recent deployments |
| **Marketing team** | List of marketing-related cookies and their consent status |
| **Agency clients** | Summary report showing compliance status per domain |

!!! tip "Regular reviews"
    Schedule regular reviews of detection results (e.g. weekly or after each deployment). Catching new cookies early prevents compliance gaps from growing.
