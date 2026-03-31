---
title: SDK Overview
description: The Waulter JavaScript SDK — what it exposes and how to work with it.
---

# SDK Overview

The Waulter SDK exposes a global `window.WaulterSDK` object after initialisation. This object provides methods to interact with the consent banner, read consent state, render legal documents, and manage custom fields.

## When is the SDK ready?

SDK initialisation is **asynchronous**. The `window.WaulterSDK` object is not available until the SDK has loaded its configuration from the server. Always guard SDK calls:

```javascript
// Simple guard
if (window.WaulterSDK) {
  window.WaulterSDK.openWidget();
}
```

For deferred or late-running code, use the [polling pattern](../implementation/direct/async-loading.md#the-polling-pattern):

```javascript
var check = setInterval(function() {
  if (window.WaulterSDK) {
    clearInterval(check);
    // SDK is ready — safe to call methods
    window.WaulterSDK.openWidget();
  }
}, 100);
setTimeout(function() { clearInterval(check); }, 8000);
```

Or listen for the `Waulter:Loaded` event:

```javascript
window.addEventListener('Waulter:Loaded', function() {
  // SDK is now ready
  console.log('Waulter SDK initialised');
});
```

!!! warning "Event listener timing"
    If the SDK loads before your event listener is registered, the event will be missed. The polling pattern handles both cases (already loaded or not yet loaded).

## Public methods

| Method | Returns | Description |
|--------|---------|-------------|
| [`openWidget()`](api-reference.md#openwidget) | `void` | Open the consent banner / preference centre |
| [`appendDocument(containerId, docId)`](api-reference.md#appenddocument) | `void` | Render a policy document into a DOM element |
| [`setCustomFields(fields)`](api-reference.md#setcustomfields) | `void` | Update custom fields at runtime |
| [`getCategories()`](api-reference.md#getcategories) | `array` | Get the current purpose categories and their consent state |
| [`getEnableGCM2()`](api-reference.md#getenablegcm2) | `boolean` | Check if Google Consent Mode v2 is enabled |
| [`getEnableGCM2Advanced()`](api-reference.md#getenablegcm2advanced) | `boolean` | Check if GCM v2 Advanced mode is enabled |

See the [API Reference](api-reference.md) for full method signatures, parameters, and examples.

## Events

The SDK emits events through two channels:

### Data layer events (GTM)

When `useGtm: true`, events are pushed to `window.dataLayer`:

| Event | When |
|-------|------|
| `Waulter:Loaded` | SDK has received configuration and is ready |
| `Waulter:Open` | Consent banner is shown to the visitor |
| `Waulter:Decision` | Visitor makes a consent choice |
| `Waulter:Closed` | Banner closes after a decision |
| `Waulter:Login` | Visitor clicks the Waulter login button |
| `Waulter:CustomFieldsUpdated` | Custom fields changed via `setCustomFields()` |

### DOM CustomEvents

The same events are also dispatched as `CustomEvent` objects on both `window` and `document`:

```javascript
window.addEventListener('Waulter:Decision', function(e) {
  console.log('Decision:', e.detail.decision);
  console.log('Purposes:', e.detail.purposes);
});
```

See [Events & Data Layer](../features/events.md) for complete schemas and lifecycle examples.

## Configuration

The SDK is configured via `window.WaulterConfig` (set before loading `sdk.js`) or via `data-*` attributes on the script tag. See the [Direct Script Tag](../implementation/direct/index.md#waulterconfig-reference) guide for the full configuration reference.

## Related pages

- [API Reference](api-reference.md) — full method documentation
- [Custom Fields](custom-fields.md) — using `customField1`–`customField10`
- [Callbacks](callbacks.md) — subscribing to SDK events programmatically
- [Events & Data Layer](../features/events.md) — data layer events and GCM integration
- [Async Loading](../implementation/direct/async-loading.md) — the polling pattern and `appendDocument`
