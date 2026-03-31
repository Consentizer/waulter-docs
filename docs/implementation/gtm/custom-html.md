---
title: GTM Custom HTML Tag
description: >
  Advanced GTM setup using a Custom HTML tag — full control over SDK initialization,
  async loading patterns, and fallback logic.
---

# Custom HTML Tag

Use a GTM Custom HTML tag when you need full control over how and when the Waulter SDK loads. This approach is ideal for advanced integrations, dynamic configuration, and the `appendDocument` pattern.

## Why Custom HTML?

- **Custom timing** — control exactly when the SDK initialises
- **Dynamic configuration** — pull values from your data layer, server-side variables, or DOM elements
- **appendDocument pattern** — embed legal documents using the async-safe polling pattern
- **Integration with existing stacks** — combine with other consent or tag management logic

## Basic setup

### Step 1 — Create the tag

In GTM, go to **Tags** > **New** > **Custom HTML** and paste:

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

### Step 2 — Set the trigger

Select **Consent Initialization - All Pages** (or **All Pages** if the former is not available).

### Step 3 — Configure tag options

- **Tag firing options**: Once per page
- **Tag sequencing**: No requirements (the Consent Initialization trigger handles ordering)

## Adding custom fields

Custom fields can be populated from any JavaScript source. They are evaluated at the time the tag fires:

```html
<script>
  window.WaulterConfig = window.WaulterConfig || {};
  Object.assign(window.WaulterConfig, {
    id: "YOUR_CONFIG_ID",
    useGtm: true,

    // From data layer / GTM variables
    customField1: {{User Tier}},
    customField2: {{Page Type}},

    // From localStorage
    customField3: localStorage.getItem("campaign") || "none",

    // From DOM
    customField4: document.querySelector('meta[name="section"]')?.content || "default",

    // Static
    customField5: "v2-banner"
  });
</script>
<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
<script src="https://cdn.waulter.cz/sdk.js"></script>
```

!!! tip "GTM variable syntax"
    In a Custom HTML tag, GTM variables use double curly braces: `{{Variable Name}}`. GTM replaces these with the variable's value before the tag fires.

## The appendDocument polling pattern

The `appendDocument` method renders a legal document (Cookie Policy, Privacy Policy) into a DOM element. Because the SDK loads asynchronously, you need a **polling pattern** to call `appendDocument` safely.

### Why polling is needed

The Waulter SDK is loaded asynchronously to avoid render-blocking the page. There is no guaranteed order between:

1. The script that calls `appendDocument` (often loaded via GTM)
2. The SDK loader (`sdk.js`)

The polling pattern checks for the SDK every 100 ms and gives up after 8 seconds, balancing responsiveness with graceful degradation.

### Annotated pattern

```html
<script>
(function() {
  // Helper: call appendDocument once SDK is ready
  var callWhenReady = function() {
    // Check if SDK is already available
    if (window.WaulterSDK && typeof window.WaulterSDK.appendDocument === 'function') {
      window.WaulterSDK.appendDocument('waulterCookies', 'YOUR_DOC_ID');
      return;
    }

    // SDK not yet loaded — poll every 100 ms, give up after 8 s
    var deadline = Date.now() + 8000;
    var interval = setInterval(function() {
      if (window.WaulterSDK && typeof window.WaulterSDK.appendDocument === 'function') {
        clearInterval(interval);
        window.WaulterSDK.appendDocument('waulterCookies', 'YOUR_DOC_ID');
      } else if (Date.now() >= deadline) {
        clearInterval(interval);
        console.warn('[Waulter] Timeout: SDK not ready after 8 s');
      }
    }, 100);
  };

  // Guard: wait for DOM to be ready
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', callWhenReady);
  } else {
    callWhenReady();  // DOM already interactive or complete
  }
})();
</script>
```

| Part | Purpose |
|------|---------|
| `window.WaulterSDK` check | Detects if the SDK is already initialised |
| `setInterval` (100 ms) | Polls until the SDK becomes available |
| 8-second deadline | Prevents infinite polling if the SDK fails to load |
| `document.readyState` guard | Ensures the DOM is ready before trying to attach content |
| `DOMContentLoaded` listener | Defers execution if the tag fires very early in `<head>` |

!!! warning "Replace placeholders"
    - Replace `waulterCookies` with the `id` attribute of the HTML element where the document should render.
    - Replace `YOUR_DOC_ID` with your actual document ID from the Waulter dashboard.

### HTML container element

Your page must include a container element for the document:

```html
<div id="waulterCookies"></div>
```

The `appendDocument` method injects the document content into this element.

## The DOMContentLoaded guard

GTM fires Custom HTML tags after the HTML has been parsed, but some integrations inject tags very early in the `<head>`. The `document.readyState` check ensures the DOM is available before the SDK tries to attach UI elements.

| `document.readyState` | Meaning | Action |
|----------------------|---------|--------|
| `"loading"` | HTML is still being parsed | Wait for `DOMContentLoaded` |
| `"interactive"` | DOM is ready, sub-resources still loading | Proceed immediately |
| `"complete"` | Everything is loaded | Proceed immediately |

## Suppressing the banner on specific pages

Add `?no_waulter_cb` to any page URL to prevent the SDK from starting:

```
https://example.com/cookie-policy?no_waulter_cb
```

This is useful for:

- Cookie policy pages (to avoid circular consent prompts)
- Admin or backoffice pages
- Print views

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| `appendDocument` does nothing | SDK not yet loaded | Use the polling pattern above |
| `appendDocument` timeout | SDK failed to load (blocked, wrong URL) | Check network tab for `sdk.js` load errors |
| Banner appears but no data layer events | `useGtm` not set | Ensure `useGtm: true` in WaulterConfig |
| Tag fires multiple times | Firing option not set | Set **Tag firing options** to **Once per page** |
| DOM element not found | Tag fires before DOM is ready | Add the `document.readyState` guard |
