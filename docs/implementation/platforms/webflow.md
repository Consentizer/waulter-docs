---
title: Webflow
description: Installing Waulter CMP on Webflow projects.
---

# Webflow

Waulter can be deployed on Webflow projects using either the **GTM approach** (recommended) or by adding custom code directly in Webflow's project settings.

## Option 1: Via Google Tag Manager (recommended)

If your Webflow project already uses GTM:

1. Follow the [GTM implementation guide](../gtm/index.md) to add Waulter to your GTM container.
2. No additional Webflow configuration is needed — GTM handles the SDK loading.

!!! tip "GTM on Webflow"
    Add your GTM container code in Webflow under **Project Settings** > **Custom Code** > **Head Code**. Then manage Waulter entirely from GTM.

## Option 2: Direct custom code

If you don't use GTM, add the SDK directly in Webflow:

### Step 1 — Add the SDK to your project

1. In Webflow, go to **Project Settings** > **Custom Code**.
2. In the **Head Code** section, paste:

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

3. Replace `YOUR_CONFIG_ID` with your Configuration ID from the Waulter dashboard.
4. Save and publish.

### Step 2 — Add a consent re-open button

Add a link or button that lets visitors change their consent:

1. Add a **Link Block** or **Text Link** to your footer.
2. Set the link to `#`.
3. Add a custom attribute: **Name:** `onclick` **Value:** `window.WaulterSDK.openWidget(); return false;`
4. Label it "Cookie Settings" or "Manage Cookies".

### Step 3 — Add a cookie policy page (optional)

To render a managed Cookie Policy document:

1. Create a new page in Webflow (e.g. `/cookie-policy`).
2. Add a **Div Block** with ID `your-element-id`.
3. In **Page Settings** > **Custom Code** > **Before `</body>` tag**, paste the [polling pattern](../direct/async-loading.md#the-polling-pattern) with your document ID.
4. Add `?no_waulter_cb` to the page URL in your cookie policy link to suppress the banner on that page.

## Webflow-specific considerations

| Consideration | Details |
|--------------|---------|
| **Webflow preview** | The consent banner may not appear in the Webflow Designer preview. Test on the published site. |
| **Custom code location** | Use **Head Code** (not Footer Code) to ensure the SDK loads before other scripts. |
| **Publishing** | After adding the code, you must **Publish** the Webflow project for changes to take effect. |
| **Webflow staging** | Add your Webflow staging domain (e.g. `your-project.webflow.io`) to the Whitelisted Domains in the Waulter dashboard for testing. |

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Banner not appearing in Webflow Designer | Test on the published site — the Designer preview does not execute custom head code |
| Banner not appearing on published site | Verify your domain is whitelisted and your Configuration ID is correct |
| Material Icons not loading | Ensure the Google Fonts `<link>` tag is in the Head Code section |
