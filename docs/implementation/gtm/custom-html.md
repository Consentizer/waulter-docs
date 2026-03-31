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

## Why purpose-level control matters

Google Consent Mode v2 uses **aggregated signals** — multiple Waulter purposes map to the same GCM signal. This creates a compliance gap if you rely solely on GCM signals for tag firing.

### The problem: signal aggregation

Consider these three purposes that all map to `analytics_storage`:

| Purpose | Code | GCM Signal |
|---------|------|-----------|
| Web Analytics | PU046 | `analytics_storage` |
| A/B Testing | PU050 | `analytics_storage` |
| Advanced Analytics | PU061 | `analytics_storage` |

If a visitor accepts **only PU046** (Web Analytics) but explicitly rejects PU050 (A/B Testing), the GCM signal `analytics_storage` becomes `granted`. Any tag gated only by `analytics_storage` will now fire — **including your A/B testing tag**, which the visitor explicitly rejected.

!!! danger "Regulatory risk"
    Firing a tag against a visitor's explicit purpose-level rejection is a **compliance violation** under GDPR. The visitor made a specific choice, and your implementation overrode it because of signal aggregation. This can lead to regulatory fines and loss of trust.

### The solution: purpose-level triggers

Instead of gating tags by GCM signals alone, check the **specific purpose code** in the `Waulter - Purposes` array:

```javascript
// GTM Custom JavaScript Variable: "Waulter - AB Testing Allowed?"
function() {
  var purposes = {{Waulter - Purposes}};
  if (Array.isArray(purposes)) {
    return purposes.indexOf("PU050") !== -1;
  }
  return false;
}
```

Use this variable as a trigger condition for your A/B testing tag. Now it only fires when PU050 is specifically accepted — regardless of what `analytics_storage` says.

!!! tip "Use the scaffold"
    The [GTM Scaffold](scaffold.md) includes pre-built purpose-level variables for all 50 Waulter purposes, plus category-level triggers with exception pairs. Import it to get this control without writing custom JavaScript.

### When to use which approach

| Approach | Use when |
|----------|---------|
| **GCM signals only** | All your tags are Google tags (GA4, Google Ads) that natively respect GCM signals, and you don't need purpose-level granularity |
| **Category-level triggers** (scaffold) | You want to ensure all purposes in a category are accepted before firing category-related tags |
| **Purpose-level variables** (scaffold or custom) | You need to control individual tags based on specific purpose acceptance — the strictest compliance approach |

## Triggers and exception control

For maximum compliance control, set up **exception triggers** that block tags when specific purposes are not accepted.

### How exception triggers work

In GTM, every tag can have:

- **Firing triggers** — conditions that cause the tag to fire
- **Blocking triggers (exceptions)** — conditions that prevent the tag from firing, even if a firing trigger matches

The scaffold provides paired triggers for each category: a **grant trigger** (fires when the category is accepted) and an **exception trigger** (blocks when it's not).

### Example: Block A/B testing without PU050

1. Set your A/B testing tag's firing trigger to `Waulter:Decision` or All Pages.
2. Add **Waulter - AB Testing Exception** as a blocking trigger.
3. Result: the tag fires only when the visitor has specifically accepted PU050.

### Example: Block Facebook Pixel without full marketing consent

1. Set the Facebook Pixel's firing trigger to All Pages.
2. Add **Waulter - [PC008] Marketing Category Exception** as a blocking trigger.
3. Result: the Pixel only fires when **all** marketing purposes are accepted, not just when `ad_storage` is `granted` from a single marketing purpose.

### Building custom exception triggers

If the scaffold doesn't cover your exact use case:

1. Create a **Custom JavaScript Variable** that checks `{{Waulter - Purposes}}` for the specific purpose codes your tag requires.
2. Create a **Custom Event Trigger** on `Waulter:Decision` with a condition on your variable.
3. Create a second trigger with the **inverse** condition as your exception.
4. Assign the grant trigger as firing, the exception trigger as blocking.

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
      window.WaulterSDK.appendDocument('your-element-id', 'YOUR_DOC_ID');
      return;
    }

    // SDK not yet loaded — poll every 100 ms, give up after 8 s
    var deadline = Date.now() + 8000;
    var interval = setInterval(function() {
      if (window.WaulterSDK && typeof window.WaulterSDK.appendDocument === 'function') {
        clearInterval(interval);
        window.WaulterSDK.appendDocument('your-element-id', 'YOUR_DOC_ID');
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
    - Replace `your-element-id` with the `id` attribute of the HTML element where the document should render.
    - Replace `YOUR_DOC_ID` with your actual document ID from the Waulter dashboard.

### HTML container element

Your page must include a container element for the document:

```html
<div id="your-element-id"></div>
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
