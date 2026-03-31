---
title: Key Concepts
description: >
  Core vocabulary you need to understand before implementing Waulter:
  purposes, scenarios, configurations, and consent signals.
---

# Key Concepts

Understanding these terms will help you navigate every other section of this documentation.

## Configuration

A **Configuration** is a named set of settings attached to a domain. It defines everything about how consent is collected on that website:

- Which **purposes** (cookie categories) are available
- The **consent banner** text, styling, and layout
- Which **legal documents** (Cookie Policy, Privacy Policy) to display
- **Google Consent Mode** settings (Basic or Advanced)
- **Whitelisted domains** where the banner is allowed to render
- Consent duration (how many days a decision remains valid)

Each configuration has a unique **Configuration ID** (e.g. `AG0000`) that you use when implementing the SDK.

!!! info "One configuration per domain set"
    Typically you create one configuration per website (or group of related domains). If you need different consent experiences on different pages, use [Scenarios](#scenario) instead of separate configurations.

## Purpose

A **Purpose** is a specific data-processing activity for which you collect consent. Each purpose represents a category of cookies or tracking technologies on your site.

Waulter ships with standard purpose categories:

| Purpose | Description | GCM Signal |
|---------|-------------|-----------|
| Essential / Technical | Security cookies, load balancing, session management | `security_storage` (always granted) |
| Web Analytics (Basic) | Traffic analysis, page views, basic statistics | `analytics_storage` |
| A/B Testing | Experiment assignment, variant tracking | `analytics_storage` |
| Advanced Analytics | Heatmaps, session recordings, funnel analysis | `analytics_storage` |
| Advertising / Marketing | Ad delivery, campaign measurement | `ad_storage` |
| Personalised Advertising | Retargeting, interest-based ad selection | `ad_personalization` |
| Remarketing | Cross-site ad retargeting | `ad_personalization` |
| Ad Measurement | Conversion tracking, attribution | `ad_storage` |

**Technical / essential purposes** are always granted and cannot be toggled by visitors. All other purposes require explicit consent.

Each purpose maps to one or more **Google Consent Mode signals** — when a visitor accepts a purpose, the corresponding GCM signals are set to `granted`. See [Purposes](../features/purposes.md) for the full mapping table.

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

## Configuration ID

The **Configuration ID** is the unique string that identifies your configuration. It is the primary parameter you pass to the SDK on initialisation.

- Configuration IDs look like `AG0000` (for direct configurations)
- Scenario IDs look like `SC00009` (for scenario-based configurations)

You find your Configuration ID in the Waulter dashboard under **Implementation**.

```javascript
// Using a Configuration ID directly
window.WaulterConfig = { id: "AG0000", useGtm: true };

// Using a Scenario ID
window.WaulterConfig = { id: "SC00009", useGtm: true };
```

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
