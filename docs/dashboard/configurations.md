---
title: Configurations
description: What a Waulter Configuration is, active vs inactive states, the preview button, and Configuration IDs.
---

# Configurations

A **Configuration** is the core unit in Waulter — it defines everything about how consent is collected on a specific website or domain: which purposes are presented, what the banner looks like, which languages are supported, and how Google Consent Mode signals are sent.

## What is a Configuration?

A configuration is a named set of settings attached to one or more domains. It contains:

| Component | Description |
|-----------|-------------|
| **Purposes** | The cookie categories visitors can consent to (analytics, marketing, etc.) |
| **Banner text & translations** | All visible text in the consent banner and preference centre |
| **Styling** | Template selection, colours, fonts, and custom icon |
| **Legal documents** | Links to Cookie Policy and Privacy Policy |
| **GCM mode** | Google Consent Mode settings (Basic or Advanced) |
| **Whitelisted domains** | Domains where the SDK is allowed to render |
| **Consent durations** | How long each consent decision type remains valid |

Each configuration represents a single consent experience. If you need different experiences for different pages or user types, use [Scenarios](scenarios.md) to dynamically select between configurations.

## Configuration ID

Every configuration has a unique **Configuration ID** — the identifier you pass to the SDK when deploying Waulter on your site.

- Configuration IDs look like `AG0000`
- Scenario IDs look like `SC00009`

**Where to find it:**

1. Log in to the Waulter dashboard.
2. Open your website configuration.
3. Navigate to the **Implementation** tab.
4. Your Configuration ID is displayed prominently at the top.

```javascript
// Use your Configuration ID in the SDK setup
window.WaulterConfig = {
  id: "AG0000",  // Your Configuration ID
  useGtm: true
};
```

!!! tip "Configuration ID vs Scenario ID"
    If you use scenarios, pass the **Scenario ID** (e.g. `SC00009`) instead. The scenario evaluates rules at runtime and selects the appropriate configuration. See [Scenarios](scenarios.md).

## Active vs Inactive states

Configurations have two states:

| State | Badge | Description |
|-------|-------|-------------|
| **Active** | Green | The configuration is live and serving consent banners to visitors |
| **Inactive** | Grey | The configuration exists but is not serving. The SDK will not render a banner for this configuration. |

### Activating a configuration

1. Open the configuration in the dashboard.
2. Ensure all required fields are filled in (purposes, domains, etc.).
3. Toggle the state to **Active**.
4. Save the configuration.

### Deactivating a configuration

1. Open the configuration.
2. Toggle the state to **Inactive**.
3. Save.

!!! warning "Deactivating a live configuration"
    When you deactivate a configuration that is deployed on a live website, the SDK will stop rendering the consent banner. Visitors will not see a consent prompt, and Google Consent Mode signals will not be set. Only deactivate when you intend to take the banner offline.

## The "Try it" preview button

The dashboard provides a **preview button** that opens your consent banner exactly as visitors will see it — without affecting live visitors or statistics.

**How to use it:**

1. Open your configuration in the dashboard.
2. Click the **Preview** / **Try it** button.
3. A preview window opens showing your banner with the current settings.
4. Interact with the banner to test the consent flow.

**What the preview shows:**

- Banner appearance with your current styling and template
- All text and translations
- Purpose categories and their descriptions
- Button labels and behaviour
- Preference centre layout

!!! info "Preview works on inactive configurations"
    You can preview a configuration even when it is inactive. This is useful for testing changes before going live.

## Creating a new configuration

1. In the dashboard, navigate to the **Configurations** list.
2. Click **Create new configuration**.
3. Fill in the required fields:

| Field | Required | Description |
|-------|----------|-------------|
| Name | Yes | A descriptive name (e.g. "Main website — production") |
| Website URL | Yes | The primary domain (e.g. `https://www.example.com`) |
| Purposes | Yes | Select which purpose categories to include |
| Language | Yes | Primary language for banner text |
| Template | Yes | Choose a banner template |
| GCM Mode | No | Basic (default) or Advanced |

4. Save the configuration.
5. Add your domain(s) to the **Whitelisted Domains** list.
6. Optionally, adjust [styling](styling.md), [texts](texts.md), and linked [documents](../features/documents.md).
7. Activate the configuration when ready.

## Managing whitelisted domains

The SDK validates the page's hostname against the configuration's whitelisted domains. Add all domains and subdomains where you deploy the SDK:

- `example.com`
- `www.example.com`
- `shop.example.com`
- `staging.example.com` (for testing — remove before production if desired)

!!! tip "Don't forget subdomains"
    Each subdomain must be added separately. `example.com` does not automatically include `www.example.com` or `shop.example.com`.

## Configuration settings overview

| Setting | Location | Description |
|---------|----------|-------------|
| Purposes | Purposes tab | Enable/disable purpose categories |
| Banner text | [Texts & Translations](texts.md) | Customise all banner copy |
| Appearance | [Styling & Templates](styling.md) | Template, colours, fonts, icon |
| Legal documents | Documents tab | Link Cookie Policy and Privacy Policy |
| GCM mode | GCM tab | Basic or Advanced |
| Consent duration | Settings tab | Days for accept/mixed/reject decisions |
| Whitelisted domains | Domains tab | Allowed domains for SDK rendering |
| Scenarios | [Scenarios](scenarios.md) | Dynamic configuration targeting |

## Duplicating configurations

You can duplicate an existing configuration to create a new one with the same settings. This is useful for:

- Creating configurations for new domains with similar requirements
- Building a template-based workflow for agencies
- Testing changes on a copy before applying to production

See [Copying Configurations](copy-config.md) for the full workflow.
