---
title: Statistics & Reporting
description: >
  Banner impressions, consent rates, per-purpose statistics, whitelisted domains
  and their relationship to statistics, and exporting data from the Waulter dashboard.
---

# Statistics & Reporting

The Waulter dashboard provides detailed statistics on how visitors interact with your consent banner — from impressions and decision breakdowns to per-purpose consent rates.

## What is tracked

| Metric | Description |
|--------|-------------|
| **Banner impressions** | How many times the consent banner was displayed to visitors |
| **Accept All** | Number of visitors who accepted all purposes |
| **Reject All** | Number of visitors who rejected all non-essential purposes |
| **Mixed consent** | Number of visitors who customised their consent (selected some purposes) |
| **Consent rate** | Percentage of visitors who gave consent (accept all + mixed) |
| **Per-purpose acceptance** | Acceptance rate for each individual purpose category |
| **Page visits** | Total page loads where the SDK was active |

## Whitelisted domains and statistics

!!! warning "Only whitelisted domains contribute to statistics"
    Statistics are collected **only from whitelisted domains**. If you forget to add a domain to the whitelist, consent still works on that domain (the banner appears, GCM signals fire, consent is stored), but the events are **not counted** in your dashboard statistics.

| Domain status | Consent works? | Appears in stats? |
|--------------|---------------|------------------|
| Whitelisted | Yes | Yes |
| Not whitelisted | Yes | No |

If you notice unexpectedly low numbers in your statistics, check that all your production domains are in the **Whitelisted Domains** list.

## Dashboard overview

### Summary cards

The statistics overview shows summary cards with key metrics:

- **Total impressions** — banner displays in the selected period
- **Acceptance rate** — percentage of visitors who gave consent
- **Rejection rate** — percentage of visitors who rejected all purposes
- **Mixed rate** — percentage of visitors who customised consent

### Time period selection

Statistics can be viewed for different time periods:

| Period | Use case |
|--------|----------|
| **Daily** | Monitor day-to-day consent trends |
| **Weekly** | Track weekly patterns and identify anomalies |
| **Monthly** | Report on monthly consent rates for compliance |
| **Custom range** | Select specific start and end dates for targeted analysis |

### Charts and visualisations

| Chart type | What it shows |
|-----------|--------------|
| **Decision breakdown** | Bar or pie chart showing the split between accept, reject, and mixed |
| **Trend line** | Consent rate over time — useful for spotting changes after banner modifications |
| **Per-purpose bars** | Acceptance rate per purpose category — shows which purposes visitors are most/least likely to accept |

## Per-purpose statistics

The per-purpose breakdown shows how each purpose category performs:

| Purpose | Acceptance rate | Insight |
|---------|----------------|---------|
| Web Analytics (PU046) | Typically high (70-90%) | Most visitors accept analytics |
| A/B Testing (PU050) | Medium-high (60-80%) | Usually bundled with analytics |
| Marketing (PU047) | Lower (30-60%) | More visitors reject marketing cookies |
| Personalised Ads (PU072) | Lowest (20-50%) | Visitors are cautious about ad personalisation |

!!! tip "Use per-purpose stats to optimise"
    If a specific purpose has a very low acceptance rate, consider:

    - Improving the purpose description to be clearer about what it does
    - Bundling related purposes into clearer categories
    - Reviewing whether that purpose is necessary for your site

## Interpreting statistics

### Consent rate benchmarks

Consent rates vary by industry, region, and banner design:

| Factor | Impact on consent rate |
|--------|----------------------|
| **Banner design** | Clear, non-intrusive banners tend to have higher consent rates |
| **Text clarity** | Visitors consent more when they understand what they're agreeing to |
| **Number of purposes** | Fewer, well-explained purposes → higher consent |
| **Region** | EU visitors are more consent-aware; rates vary by country |
| **Traffic source** | Organic visitors may consent differently than paid traffic |

### What a drop in consent rate means

A sudden drop may indicate:

- A banner change that made it less clear or more intimidating
- A new purpose category that visitors are hesitant about
- A technical issue (SDK not loading, configuration error)
- Browser privacy feature updates (ITP, ETP) affecting consent persistence

## Exporting data

Statistics can be exported from the dashboard for external reporting:

1. Navigate to the **Statistics** section.
2. Select the time period and metrics you want to export.
3. Click **Export**.

### Use cases for exported data

| Audience | What to include |
|----------|----------------|
| **DPO / Legal** | Monthly consent rates, per-purpose acceptance, total impressions |
| **Marketing** | Marketing purpose consent rate, trend over time |
| **Management** | High-level summary: overall consent rate, visitor count, trend |
| **Auditors** | Full export with daily granularity for the audit period |

## Statistics and scenarios

When using [scenarios](scenarios.md), statistics are tracked per configuration. If a scenario routes visitors to different configurations based on rules, each configuration accumulates its own statistics.

This allows you to compare:

- Consent rates between A/B test variants
- Performance of different banner designs
- Acceptance rates by user segment (if using custom field-based scenarios)
