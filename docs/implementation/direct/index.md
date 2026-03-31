---
title: Direct Script Tag Integration
description: >
  Integrate Waulter directly in your HTML without a tag manager — suitable for
  headless CMS, server-side rendered apps, and fully custom setups.
---

# Direct Script Tag

Add the Waulter SDK directly to your HTML when you don't use a tag manager or need full control over the loading process. This approach works with any website — static HTML, server-rendered apps, headless CMS, or single-page applications.

## Basic installation

The simplest implementation requires two elements: a configuration block and the SDK script.

```html
<script>
  window.WaulterConfig = { id: "YOUR_CONFIG_ID" };
</script>
<script src="https://cdn.waulter.cz/sdk.js"></script>
```

Replace `YOUR_CONFIG_ID` with your Configuration ID (e.g. `AG0000`) or Scenario ID (e.g. `SC00009`) from the Waulter dashboard.

## Recommended production setup

For production, use the full configuration with consent durations, GTM integration, and Material Icons:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Website</title>

    <!-- Waulter CMP SDK — place as early as possible in <head> -->
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
    <!-- End Waulter CMP SDK -->

</head>
<body>
    <!-- Your website content -->

    <!-- Optional: re-open consent dialog via button -->
    <button onclick="window.WaulterSDK.openWidget()">
      Cookie Settings
    </button>
</body>
</html>
```

## Placement: why `<head>` matters

The SDK must be placed in the `<head>` section, as early as possible. This ensures:

1. **Google Consent Mode signals fire before analytics tags** — GCM requires that `gtag('consent', 'default', ...)` runs before any Google tag initialises. Placing the SDK late in the page risks tags firing without consent signals.
2. **Banner renders immediately** — visitors see the consent prompt before interacting with the page.
3. **No flash of unconsented content** — third-party scripts are blocked until consent is given.

!!! warning "Do not place the SDK at the bottom of `<body>`"
    Placing the SDK at the end of the page means Google Analytics, ad tags, and other trackers may fire before the default consent state is set — violating GCM requirements.

## Configuration via `window.WaulterConfig`

Set the `window.WaulterConfig` object **before** loading `sdk.js`. Use `Object.assign` to safely merge with any existing configuration:

```javascript
window.WaulterConfig = window.WaulterConfig || {};
Object.assign(window.WaulterConfig, {
  id: "YOUR_CONFIG_ID",
  useGtm: true
  // ... additional options
});
```

### WaulterConfig reference

| Property | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `id` | string | Yes | — | Configuration ID (e.g. `AG0000`) or Scenario ID (e.g. `SC00009`) |
| `useGtm` | boolean | No | `false` | Enable `dataLayer.push()` and `gtag('consent', ...)` integration |
| `defaultAllowDuration` | number | No | `90` | Days the "accept all" consent remains valid |
| `defaultMixedDuration` | number | No | `90` | Days the "partial" consent remains valid |
| `defaultRejectDuration` | number | No | `90` | Days the "reject all" consent remains valid |
| `lang` | string | No | — | Language code for banner localisation (e.g. `cs`, `en`, `de`) |
| `debug` | boolean | No | `false` | Enable console debug logging |
| `customField1`–`customField10` | string | No | — | Custom values for [scenario targeting](../../dashboard/scenarios.md) |

## Configuration via `data-*` attributes

As an alternative to `window.WaulterConfig`, the SDK reads configuration from `data-*` attributes on the script tag:

```html
<script
  src="https://cdn.waulter.cz/sdk.js"
  data-id="YOUR_CONFIG_ID"
  data-use-gtm="true"
  data-default-allow-duration="90"
  data-default-mixed-duration="90"
  data-default-reject-duration="90"
  data-custom-field-1="premium"
  data-lang="en"
></script>
```

Attribute names use **kebab-case** (e.g. `data-use-gtm`) which maps to **camelCase** config properties (e.g. `useGtm`).

!!! tip "When to use `data-*` attributes"
    Use `data-*` attributes when you cannot easily inject a `<script>` block before the SDK — for example, in CMS platforms that only allow adding a single script tag.

## Material Icons

The consent banner UI uses Material Icons for buttons and toggles. Include the stylesheet in your `<head>`:

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
```

The banner will still function without Material Icons, but some UI elements may not display correctly.

## Suppressing the SDK

Add `?no_waulter_cb` to any page URL to prevent the SDK from starting:

```
https://example.com/cookie-policy?no_waulter_cb
```

Useful for:

- Cookie policy pages (avoids circular consent prompts)
- Admin or backoffice pages
- Print views

## Domain whitelisting

The SDK validates the current page's hostname against your configuration's whitelisted domains. The banner only renders if the domain is allowed.

If the banner does not appear on your site, check that your domain is listed in the Waulter dashboard under **Whitelisted Domains**.

## Re-opening the consent dialog

Provide a link or button that lets visitors change their consent at any time:

```html
<a href="#" onclick="window.WaulterSDK.openWidget(); return false;">
  Manage Cookie Preferences
</a>
```

Or as a footer button:

```html
<button onclick="window.WaulterSDK.openWidget()">
  Cookie Settings
</button>
```

!!! info "SDK readiness"
    The `window.WaulterSDK` object is available after the SDK has initialised. If you call `openWidget()` before the SDK is ready, it will have no effect. For deferred calls, use the [async polling pattern](async-loading.md).

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Banner does not appear | Domain not whitelisted | Add your domain in the Waulter dashboard |
| Banner does not appear | Invalid Configuration ID | Verify the ID in the dashboard **Implementation** tab |
| Banner does not appear | Consent already given | Clear both the `vaswaulter` cookie (first-party and third-party) **and** localStorage, then reload. Or use incognito. |
| Banner does not appear | `?no_waulter_cb` in URL | Remove the query parameter |
| Material Icons missing | CSS not loaded | Add the Google Fonts `<link>` tag |
| `Waulter:Decision` not firing | `useGtm` is `false` | Set `useGtm: true` in WaulterConfig |
| Custom fields not in events | Fields set after SDK loads | Set fields in WaulterConfig before `sdk.js` |
