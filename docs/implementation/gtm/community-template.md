---
title: GTM Community Template
description: >
  Install the official Waulter GTM Community Template from the Google Tag Manager
  Template Gallery and configure it in minutes.
---

# GTM Community Template

The Waulter Community Template is available directly in the GTM Template Gallery. It wraps the SDK in a point-and-click interface — no Custom HTML code required.

## Installation

### Step 1 — Find the template

1. In your GTM container, go to **Templates** > **Search Gallery**.
2. Search for **"Waulter"**.
3. Click **Add to workspace** and confirm.

### Step 2 — Create a tag

1. Go to **Tags** > **New**.
2. Click **Tag Configuration** and select the **Waulter CMP** template from the **Custom** section.
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

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| **Configuration ID** | Text | Yes | — | Your Waulter Configuration ID (e.g. `AG0000`) or Scenario ID (e.g. `SC00009`) |
| **GCM Mode** | Select | No | Basic | Google Consent Mode: **Basic** (tags wait for consent) or **Advanced** (cookieless pings enabled) |
| **Accept Duration** | Number | No | 90 | Days the "accept all" consent remains valid |
| **Partial Accept Duration** | Number | No | 90 | Days the "mixed" consent remains valid |
| **Reject Duration** | Number | No | 90 | Days the "reject all" consent remains valid |
| **Enable GTM Integration** | Checkbox | No | Checked | Enables `dataLayer.push()` and `gtag('consent', ...)` integration |
| **Custom Field 1–10** | Text | No | — | Custom values for [scenario targeting](../../dashboard/scenarios.md) and data layer enrichment |

!!! info "GCM Mode explained"
    - **Basic Mode**: Google tags do not fire at all until consent is given. No data is collected before consent.
    - **Advanced Mode**: Google tags send aggregated, cookieless pings before consent. Google uses these for conversion modelling. Tags still wait for full consent before setting cookies.

    See [Google Consent Mode 2.0](../../features/gcm2.md) for details.

## Setting custom fields

Custom fields in the template accept any text value. You can use GTM variables to make them dynamic:

| Field | Example value | Source |
|-------|--------------|--------|
| Custom Field 1 | `{{User Tier}}` | GTM Variable from your data layer |
| Custom Field 2 | `{{Page Type}}` | GTM Variable from page metadata |
| Custom Field 3 | `summer-campaign` | Static value |

Custom fields are used for [scenario targeting](../../dashboard/scenarios.md) and appear in all [data layer events](../../features/events.md).

## Permissions declared by the template

The template declares the following permissions in its manifest. GTM shows these during installation:

| Permission | Scope |
|-----------|-------|
| Injects scripts | `cdn.waulter.cz`, `fonts.googleapis.com` |
| Reads/writes globals | `window.WaulterConfig`, `window.WaulterSDK`, `window.dataLayer` |
| Sets cookies | `vaswaulter` |
| Sends data | To `api.waulter.eu` |

## Updating the template

When a new version of the Waulter template is published to the Gallery:

1. Go to **Templates** in your GTM container.
2. You will see an **Update available** notification on the Waulter template.
3. Click **Update** and review the changes.
4. Test in **Preview** mode before publishing.

## When to use Custom HTML instead

Use the [Custom HTML approach](custom-html.md) if you need:

- Custom loading logic or timing
- Dynamic configuration from server-side rendering
- The `appendDocument` polling pattern for embedding legal documents
- Integration with an existing consent orchestration layer
