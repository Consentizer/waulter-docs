---
title: GTM Community Template
description: >
  Install the official Waulter GTM Community Template from the Google Tag Manager
  Template Gallery and configure it in minutes.
---

# GTM Community Template

!!! warning "Template Gallery — approval in progress"
    The Waulter GTM Community Template is currently **under the approval process** in the Google Tag Manager Template Gallery. It is not yet searchable in the Gallery. In the meantime, you can manually import the template — see [Manual Template Import](#manual-template-import) below.

The Waulter Community Template wraps the SDK in a point-and-click interface — no Custom HTML code required.

## Installation

### Step 1 — Add the template

**From the Community Template Gallery (when available):**

1. In your GTM container, go to **Templates** > **Search Gallery**.
2. Search for **"Waulter consent management (cookie and other consents management)"**.
3. Click **Add to workspace** and confirm.

**Manual import (recommended while Gallery approval is pending):**

1. Download `template.tpl` from the [Waulter GTM GitHub repository](https://github.com/Consentizer/Waulter-CMP-Google-Tag-Manager).
2. In GTM, go to **Templates** > **Tag Templates** > **New**.
3. Click the three-dot menu (top right) > **Import**.
4. Select the downloaded `template.tpl` file.
5. Click **Save**.

### Step 2 — Create a tag

1. Go to **Tags** > **New**.
2. Click **Tag Configuration** and select **Waulter consent management (cookie and other consents management)** from the **Custom** section.
3. Fill in the required fields (see [Field Reference](#field-reference) below).

### Step 3 — Set the trigger

Select **Consent Initialization - All Pages** as the trigger.

!!! warning "Trigger order matters"
    The Waulter tag **must** fire before any analytics or advertising tags. The **Consent Initialization** trigger guarantees this. If it is not available in your container, use **All Pages** and set tag priority to a high value (e.g. 100).

### Step 4 — Preview and test

1. Click **Preview** in GTM.
2. Enter your site URL and open the preview.
3. Verify that:
    - The consent banner appears for new visitors
    - The `gtm.init_consent` event fires first
    - Default consent signals are all `denied`
    - After clicking "Accept All", `Waulter:Decision` fires with `decision: "allow"`
    - Consent-dependent tags fire only after consent is granted

### Step 5 — Publish

Once verified, click **Submit** to publish your container.

## Field Reference

| Field | Parameter | Type | Required | Default | Description |
|-------|-----------|------|----------|---------|-------------|
| **Waulter ID (CCID or Scenario ID)** | `waulterId` | Text | Yes | — | Your Scenario ID (e.g. `SC00009`) or Configuration ID |
| **SDK URL** | `sdkUrl` | Text | No | `https://cdn.waulter.cz/sdk.js` | SDK script URL. Change only for dev/staging environments. |
| **Wait for Update** | `waitForUpdate` | Number | No | `500` | Milliseconds to wait for a consent update before firing tags with default values. Improves accuracy for returning visitors. |
| **Ads Data Redaction** | `adsDataRedaction` | Checkbox | No | Checked | Redacts ad click identifiers when `ad_storage` is `denied`. |
| **URL Passthrough** | `urlPassthrough` | Checkbox | No | Unchecked | Appends ad click info to URLs for cross-domain measurement. |
| **Debug Mode** | `debugMode` | Checkbox | No | Unchecked | Enable SDK debug logging to the browser console. |
| **Custom Field 1–10** | `customField1`–`customField10` | Text | No | Scaffold DLV | Custom values for [scenario targeting](../../dashboard/scenarios.md). Defaults to scaffold Data Layer Variables for auto-lookup (see below). |

!!! info "Consent duration note"
    Consent duration settings (`defaultAllowDuration`, `defaultMixedDuration`, `defaultRejectDuration`) are managed **server-side** in your Waulter configuration, not in the template.

## Setting custom fields

Custom fields in the template accept any text value. You can use GTM variables to make them dynamic:

| Field | Example value | Source |
|-------|--------------|--------|
| Custom Field 1 | `{{User Tier}}` | GTM Variable from your data layer |
| Custom Field 2 | `{{Page Type}}` | GTM Variable from page metadata |
| Custom Field 3 | `summer-campaign` | Static value |

### Auto-lookup with the scaffold

When the [companion scaffold](scaffold.md) is imported, custom fields work **automatically** without additional GTM configuration. Each template field defaults to a scaffold Data Layer Variable (`{{Waulter - Custom Field 1}}` through `{{Waulter - Custom Field 10}}`).

Your website sets a value in `localStorage` or a cookie using the naming convention `waulter_cf1` through `waulter_cf10`. The scaffold's Custom Fields Loader tag reads the value, pushes it to the data layer, the DLV picks it up, and the template passes it to `WaulterConfig` — all automatically.

```javascript
// On your website — set a custom field value
localStorage.setItem('waulter_cf1', 'premium');
// That's it — the value flows automatically to scenario rules
```

| Field | localStorage / Cookie Key | Scaffold DLV |
|-------|--------------------------|--------------|
| Custom Field 1 | `waulter_cf1` | `{{Waulter - Custom Field 1}}` |
| Custom Field 2 | `waulter_cf2` | `{{Waulter - Custom Field 2}}` |
| ... | ... | ... |
| Custom Field 10 | `waulter_cf10` | `{{Waulter - Custom Field 10}}` |

**Priority:** If you replace a field's default DLV reference with a static value or different GTM variable, that override takes highest priority. Otherwise: localStorage > cookie.

Custom fields are used for [scenario targeting](../../dashboard/scenarios.md) and appear in all [data layer events](../../features/events.md).

## Google Consent Mode v2 mapping

The template maps Waulter consent decisions to Google consent signals automatically:

| Google Consent Signal | Granted when | Description |
|----------------------|-------------|-------------|
| `analytics_storage` | Analytics purposes accepted (PU046, PU050, PU061) | Enables storage for analytics |
| `ad_storage` | Marketing purposes accepted (PU047, PU074) | Enables storage for advertising |
| `ad_user_data` | Marketing purposes accepted (PU047, PU074) | Allows sending user data to Google for advertising |
| `ad_personalization` | Personalisation purposes accepted (PU072, PU073) | Enables ad personalisation |
| `functionality_storage` | Full consent (`allow` decision only) | Enables storage for site functionality |
| `personalization_storage` | A/B testing purpose accepted (PU050) | Enables storage for personalisation |
| `security_storage` | Always `granted` | Essential security features (always on) |

!!! warning "GCM signal aggregation"
    Multiple Waulter purposes can map to the **same** GCM signal. For example, PU046 (Web Analytics), PU050 (A/B Testing), and PU061 (Advanced Analytics) all map to `analytics_storage`. If **any one** of these purposes is accepted, `analytics_storage` becomes `granted`. See [Why purpose-level control matters](custom-html.md#why-purpose-level-control-matters) for compliance implications.

    See [Google Consent Mode 2.0](../../features/gcm2.md) for details on Basic vs Advanced mode.

## Permissions declared by the template

The template declares the following permissions in its manifest. GTM shows these during installation:

| Permission | Scope |
|-----------|-------|
| Injects scripts | `cdn.waulter.cz`, `fonts.googleapis.com` |
| Reads/writes globals | `window.WaulterConfig`, `window.WaulterSDK`, `window.dataLayer` |
| Sets cookies | `vaswaulter` |
| Sends data | To `api.waulter.eu` |

## Updating the template

**Gallery updates:** When a new version is published to the GTM Community Template Gallery, GTM will show an **Update available** notification. Review the changes and click **Update**, then test in Preview mode before publishing.

**Manual updates:** Download the latest `template.tpl` from the [GitHub repository](https://github.com/Consentizer/Waulter-CMP-Google-Tag-Manager) and re-import via **Templates** > select the template > three-dot menu > **Import**.

## Companion scaffold

The template works best with the **Waulter GTM Scaffold** — a pre-built container export with 79 variables, 17 triggers, and 2 tags covering all Waulter purposes and categories.

[:octicons-arrow-right-24: GTM Scaffold Guide](scaffold.md)

## When to use Custom HTML instead

Use the [Custom HTML approach](custom-html.md) if you need:

- Full control over which tags fire for which specific purposes (beyond GCM signal aggregation)
- Custom loading logic or timing
- Dynamic configuration from server-side rendering
- The `appendDocument` polling pattern for embedding legal documents
- Integration with an existing consent orchestration layer
- Strict regulatory compliance that requires purpose-level (not signal-level) tag control
