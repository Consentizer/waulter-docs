---
title: Key Concepts
description: >
  Core vocabulary you need to understand before implementing Waulter:
  purposes, scenarios, configurations, and consent signals.
---

# Key Concepts

Understanding these terms will help you navigate every other section of this documentation.

## Configuration

A **Configuration** is the core entity in Waulter. In its most general form, a configuration is a managed container for content, settings, and user interactions — but in CMP mode (the most common use case), each configuration represents a **separate consent widget (cookie bot)** deployed on a website.

A CMP configuration defines:

- Which **purposes** (cookie categories) are available
- The **consent banner** text, styling, and template
- Which **legal documents** (Cookie Policy, Privacy Policy) to display
- **Google Consent Mode** settings (Basic or Advanced)
- **Whitelisted domains** where statistics are collected
- Consent duration (how many days a decision remains valid)

Each configuration is identified by a unique **CCID** (Configuration ID), such as `AG1035`. This is the ID you pass to the SDK when implementing Waulter on your site.

!!! info "Configuration is not limited to cookie consent"
    A configuration can also serve as a standalone document container — for example, rendering a Cookie Policy or Privacy Policy page via [`appendDocument`](../features/documents.md) without the consent banner. In this mode, the configuration acts purely as a versioned document management unit.

!!! abstract "Waulter as a lightweight Document Management System"
    Under the hood, Waulter behaves like a **lightweight DMS** that creates a deeply interconnected, auditable structure around every user interaction. When a visitor interacts with a cookie bot:

    - The **configuration** captures the exact banner text, styling, and purpose set that was shown
    - The **consent decision** is recorded as an immutable [Permission Transaction](../features/permission-tx.md) with a unique ID
    - The transaction links to the specific **document versions** (Cookie Policy, Privacy Policy) that were active at the time of consent
    - The visitor's choice is stored in a first-party [`vaswaulter` cookie](../features/cookies.md) for subsequent visits

    This means every consent interaction can be traced back to: which configuration was loaded, with which text and look-and-feel, referencing which exact document versions — forming a complete, GDPR-compliant audit trail.

!!! tip "One configuration per domain set"
    Typically you create one CMP configuration per website (or group of related domains). If you need different consent experiences on different pages, use [Scenarios](#scenario) instead of separate configurations.

## Purpose

A **Purpose** is a specific data-processing activity for which you collect consent. Each purpose represents a category of cookies or tracking technologies on your site.

Purposes are organised into **categories** (broad groups) containing **individual purposes** (specific tracking activities). Each purpose has a unique code (e.g. `PU046`) that the SDK uses internally.

| Category | Purpose Code | Purpose | GCM Signal |
|----------|-------------|---------|-----------|
| **Essential** | — | Security, session management, load balancing | `security_storage` (always granted) |
| **Analytics** | `PU046` | Basic web analytics and traffic measurement | `analytics_storage` |
| **Analytics** | `PU061` | Extended analytics and audience measurement | `analytics_storage` |
| **Testing** | `PU050` | A/B testing and experimentation | `analytics_storage` |
| **Marketing** | `PU047` | Advertising and ad network tracking | `ad_storage` |
| **Marketing** | `PU072` | Remarketing and audience building | `ad_personalization` |
| **Marketing** | `PU073` | Conversion tracking | `ad_personalization` |
| **Marketing** | `PU074` | Ad personalisation and user profiling | `ad_storage` |

**Essential purposes** are always granted and cannot be toggled by visitors. All other purposes require explicit consent.

Each purpose maps to one or more **Google Consent Mode signals** — when a visitor accepts a purpose, the corresponding GCM signals are set to `granted`. The consent banner groups purposes by category, so visitors see clear choices like "Analytics", "Marketing" rather than individual purpose codes.

See [Purposes](../features/purposes.md) for the full mapping table and [Google Consent Mode 2.0](../features/gcm2.md) for how signals flow to Google tags.

## Scenario

A **Scenario** enables dynamic consent configuration based on runtime context. Instead of always loading the same configuration, a scenario evaluates **rules** against the visitor's context and selects the appropriate configuration.

Use scenarios when you need:

- Different consent configurations for different pages or sections
- A/B testing of banner designs
- Varying consent options by user type (logged-in vs anonymous)
- Campaign-specific consent flows
- Re-consent prompts when legal text changes

**How evaluation works:**

1. Rules are evaluated **top to bottom** in the order they appear in the dashboard.
2. **First match wins** — the first rule whose conditions are satisfied determines which configuration loads.
3. If no rule matches, the **fallback configuration** is used.

Available targeting variables include `url`, `referer`, `pageview`, `acceptance`, `purposes`, and `customField1`–`customField10`.

See [Scenarios](../dashboard/scenarios.md) for a complete reference of operators and examples.

## Whitelisted Domain

**Whitelisted Domains** are the domains where the Waulter SDK is allowed to render the consent banner. The SDK validates the current page's hostname against the list of whitelisted domains configured for your configuration.

- If the domain matches, the banner renders normally.
- If the domain is not whitelisted, the banner will not appear.

Add domains in the Waulter dashboard under your configuration's settings. Include all domains and subdomains where you deploy the SDK (e.g. `example.com`, `shop.example.com`, `staging.example.com`).

!!! tip "Staging and preview domains"
    Remember to add your staging and development domains to the whitelist during implementation. You can remove them before going live.

## Configuration ID (CCID)

The **CCID** (Configuration ID) is the unique identifier for your configuration. It is the primary parameter you pass to the SDK on initialisation.

- **CCID** for direct configurations: `AG1035`, `AG2048`, etc.
- **Scenario ID** for scenario-based setups: `SC00009`, `SC00015`, etc.

You find your CCID in the Waulter dashboard under your configuration's **Implementation** tab.

```javascript
// Using a CCID directly (loads one specific configuration)
window.WaulterConfig = { id: "YOUR_CONFIG_ID", useGtm: true };

// Using a Scenario ID (evaluates rules to pick the right configuration)
window.WaulterConfig = { id: "SC00009", useGtm: true };
```

!!! info "CCID vs Scenario ID"
    When you pass a CCID, the SDK always loads that exact configuration. When you pass a Scenario ID, the SDK evaluates [scenario rules](../dashboard/scenarios.md) against the visitor's context and dynamically selects a configuration. Most production deployments use Scenario IDs for flexibility.

## Consent State

The **Consent State** is the current set of granted and denied purposes for a visitor. After a visitor makes a decision, the consent state is persisted and restored on subsequent visits.

There are three possible consent decisions:

| Decision | Meaning | Duration |
|----------|---------|----------|
| `allow` | All purposes accepted | Configurable (default: 90 days) |
| `mixed` | Some purposes accepted, others rejected | Configurable (default: 90 days) |
| `reject` | No optional purposes accepted (only essential) | Configurable (default: 90 days) |

When a returning visitor loads the page, the SDK retrieves the stored consent state and immediately updates Google Consent Mode signals — without showing the banner again.

## GCM Signal

A **GCM Signal** (Google Consent Mode signal) is a consent type sent to Google Tag Manager's consent API. The Waulter SDK manages these signals automatically.

| Signal | Controls | Set to `granted` when |
|--------|----------|----------------------|
| `analytics_storage` | Google Analytics cookies | Analytics or A/B testing purposes accepted |
| `ad_storage` | Google Ads cookies | Advertising purposes accepted |
| `ad_user_data` | Sending user data to Google for ads | Ad-related purposes accepted |
| `ad_personalization` | Personalised advertising | Personalised ads or remarketing accepted |
| `functionality_storage` | Functional cookies (e.g. language preference) | Functionality purposes accepted |
| `personalization_storage` | Personalisation cookies | Personalisation purposes accepted |
| `security_storage` | Security cookies | Always granted (essential) |

**Default state:** On page load, the SDK sets all signals to `denied`. They are updated to `granted` only after the visitor consents to the corresponding purposes.

See [Google Consent Mode 2.0](../features/gcm2.md) for a detailed guide.

## User Sharing (Cross-Domain Consent)

**User Sharing** allows a visitor to consent once and have that decision honoured across your entire domain portfolio. When the visitor navigates from Domain A to Domain B (both running Waulter under the same account), the SDK detects the existing consent and suppresses the banner.

This is useful for companies operating multiple domains (e.g. `example.com`, `shop.example.com`, `blog.example.com`) who want a seamless consent experience.

See [User Sharing](../features/user-sharing.md) for setup instructions and privacy considerations.

## Custom Fields

**Custom Fields** (`customField1` through `customField10`) let you pass arbitrary context values to the SDK. They serve two purposes:

1. **Scenario targeting** — rules can match against custom field values (e.g. show a different banner to `customField1 equals "premium"` visitors).
2. **Data layer enrichment** — custom field values are included in all `Waulter:*` data layer events, enabling segmented consent analytics in GTM.

```javascript
window.WaulterConfig = {
  id: "YOUR_CONFIG_ID",
  useGtm: true,
  customField1: "premium",        // User tier
  customField2: "en-GB",          // Language
  customField3: "campaign-summer"  // Campaign
};
```

Custom fields can be updated at runtime via `window.WaulterSDK.setCustomFields()`. See [Custom Fields](../sdk/custom-fields.md) for details.
