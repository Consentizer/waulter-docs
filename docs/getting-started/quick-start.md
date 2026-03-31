---
title: Quick Start — 5 Minutes to Live
description: Deploy Waulter on your website in five minutes using Google Tag Manager.
---

# Quick Start

The fastest path to a live consent banner is through **Google Tag Manager**. This guide takes you from zero to a working consent banner in five steps.

## Prerequisites

Before you begin, make sure you have:

- [x] A **Waulter account** — sign up at the Waulter dashboard
- [x] A **GTM container** installed on your website
- [x] Your **Configuration ID** (found in the Waulter dashboard under your website configuration)

!!! info "Where to find your Configuration ID"
    Log in to the Waulter dashboard, select your website configuration, and navigate to the **Implementation** tab. Your Configuration ID looks like `AG0000` or `SC00009` (if you are using scenarios).

## Step 1 — Open Google Tag Manager

Go to [tagmanager.google.com](https://tagmanager.google.com) and open the container for the website where you want to deploy Waulter.

## Step 2 — Create the Waulter tag

1. Navigate to **Tags** > **New**
2. Click **Tag Configuration** and select **Custom HTML**
3. Paste the following code:

```html
<script>
  window.WaulterConfig = window.WaulterConfig || {};
  Object.assign(window.WaulterConfig, {
    id: "YOUR_CONFIG_ID",
    defaultAllowDuration:  90,
    defaultMixedDuration:  90,
    defaultRejectDuration: 90,
    useGtm: true
  });
</script>
<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
<script src="https://cdn.waulter.cz/sdk.js"></script>
```

4. Replace `YOUR_CONFIG_ID` with your actual Configuration ID.
5. Set **Tag firing options** to **Once per page**.

!!! warning "Replace the placeholder"
    You must replace `YOUR_CONFIG_ID` with your real Configuration ID (e.g. `AG0000` or `SC00009`). The banner will not appear if the ID is invalid.

## Step 3 — Set the trigger

1. Click **Triggering** and choose **Consent Initialization - All Pages**.
2. This ensures the Waulter SDK loads **before** any other tags, so default consent signals are set first.

!!! tip "Why Consent Initialization?"
    The **Consent Initialization** trigger fires before all other triggers in GTM. This is essential for Google Consent Mode — analytics and ad tags must see a consent signal before they fire.

    If your GTM container does not show this trigger, use **All Pages** as a fallback.

## Step 4 — Save and publish

1. Name your tag (e.g. "Waulter CMP SDK").
2. Click **Save**.
3. Click **Submit** to publish your container.

## Step 5 — Verify it works

1. Open your website in a **new incognito/private window** (to clear any existing consent cookies).
2. The consent banner should appear.
3. Open the browser's Developer Tools console and check for:
    - `Waulter:Loaded` event in the data layer
    - `gtag('consent', 'default', ...)` setting all signals to `denied`

### Using GTM Preview mode

For a more detailed check:

1. In GTM, click **Preview** and enter your site URL.
2. The Tag Assistant panel opens alongside your site.
3. Verify that:

| Check | Expected result |
|-------|----------------|
| Waulter tag fires | Yes, on **Consent Initialization** |
| `gtm.init_consent` event | Present in the data layer |
| Default consent state | All signals show `denied` |
| After clicking "Accept All" | `Waulter:Decision` event with `decision: "allow"` |
| Consent update | Signals change to `granted` for accepted purposes |

!!! success "You're live!"
    If the banner appears and consent signals fire correctly, you're done. Returning visitors with valid consent will not see the banner again.

## What's next?

- **Customise your banner** — adjust colours, text, and layout in the [Dashboard Styling](../dashboard/styling.md) settings
- **Configure purposes** — choose which cookie categories visitors can consent to: [Purposes](../features/purposes.md)
- **Add scenarios** — serve different configurations per URL or user type: [Scenarios](../dashboard/scenarios.md)
- **Use the Community Template** — for a point-and-click setup instead of Custom HTML: [Community Template](../implementation/gtm/community-template.md)
- **Set up consent-dependent tags** — fire analytics and ad tags only after consent: [Events & Data Layer](../features/events.md)

## Troubleshooting

| Problem | Possible cause | Solution |
|---------|---------------|----------|
| Banner does not appear | Domain not whitelisted | Add your domain in the Waulter dashboard under **Whitelisted Domains** |
| Banner does not appear | Invalid Configuration ID | Double-check the ID in the dashboard **Implementation** tab |
| Banner does not appear | Consent already given | Clear the `vaswaulter` cookie or use an incognito window |
| No `Waulter:Decision` event | `useGtm` not set to `true` | Ensure `useGtm: true` is in your WaulterConfig |
| Material Icons missing | Stylesheet not loaded | Verify the Google Fonts `<link>` tag is included |
