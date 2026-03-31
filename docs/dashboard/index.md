---
title: Dashboard Overview
description: Orientation to the Waulter B2B dashboard — what you see when you log in and how to navigate it.
---

# Dashboard Overview

The Waulter B2B dashboard is your central hub for managing consent configurations, viewing statistics, and monitoring compliance across all your web properties.

## What you see when you log in

After logging in, the dashboard shows:

- **Configuration list** — all your website configurations with status badges (active/inactive), sharing indicators, and quick actions
- **Navigation sidebar** — links to all dashboard sections
- **Quick actions** — create new configuration, view recent activity

## Key sections

| Section | What it does | Guide |
|---------|-------------|-------|
| **Configurations** | Create and manage consent configurations for your websites | [Configurations](configurations.md) |
| **Scenarios** | Set up dynamic targeting rules that select different configurations based on visitor context | [Scenarios](scenarios.md) |
| **Statistics** | View consent rates, banner impressions, and per-purpose acceptance data | [Statistics](statistics.md) |
| **Cookie Detection** | Review automated crawl results showing cookies, scripts, and GCM state on your sites | [Cookie Detection](cookie-detection.md) |
| **Compliance** | Monitor compliance status and act on findings | [Compliance](compliance.md) |
| **Styling** | Customise banner appearance: template, colours, fonts, icon | [Styling](styling.md) |
| **Texts** | Manage all banner text and translations | [Texts & Translations](texts.md) |

## Configuration detail view

Clicking on a configuration opens its detail view with tabs for:

| Tab | Contents |
|-----|----------|
| **Overview** | Configuration name, status, Configuration ID, primary domain |
| **Purposes** | Enabled cookie categories and their descriptions |
| **Implementation** | Configuration ID, SDK code snippets, GTM setup instructions |
| **Styling** | Template selection, custom colours, fonts, icon |
| **Texts** | All banner text and translations per language |
| **Documents** | Linked Cookie Policy and Privacy Policy |
| **Domains** | Whitelisted domains where the SDK is allowed to render |
| **GCM** | Google Consent Mode settings (Basic/Advanced) |
| **Sharing** | Users with access to this configuration and their roles |
| **Statistics** | Consent metrics for this configuration |
| **Detection** | Cookie detection results for this configuration's domains |

## Common workflows

| Task | Where to start |
|------|---------------|
| Deploy Waulter on a new site | Create a configuration → get your Configuration ID → follow the [Quick Start](../getting-started/quick-start.md) |
| Change banner text | Configuration detail → [Texts](texts.md) tab |
| Customise banner appearance | Configuration detail → [Styling](styling.md) tab |
| Add a new language | Configuration detail → [Texts](texts.md) → Add language |
| Check consent rates | [Statistics](statistics.md) section |
| Review detected cookies | [Cookie Detection](cookie-detection.md) section |
| Share with a colleague | Configuration detail → Sharing tab → see [Agency Sharing](../agency/sharing.md) |
| Set up A/B testing | Create a [Scenario](scenarios.md) with targeting rules |
| Duplicate for a new domain | Configuration detail → Duplicate → see [Copying Configurations](copy-config.md) |

## Dashboard and the visitor experience

The dashboard configures what visitors see. Changes you make are reflected on your live site:

| Dashboard setting | Visitor sees |
|------------------|-------------|
| Purposes enabled | Purpose categories in the preference centre |
| Banner text | The consent message and button labels |
| Styling | Banner colours, fonts, layout |
| GCM mode | How Google tags behave before/after consent |
| Active/Inactive status | Whether the banner appears at all |

!!! info "Changes are immediate"
    Most dashboard changes take effect on the next page load — no code deploy or GTM publish required. The SDK fetches configuration from the server on every page load.
