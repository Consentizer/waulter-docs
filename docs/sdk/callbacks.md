---
title: Callbacks
description: Subscribe to SDK events programmatically using the WaulterSDK callback API.
---

# Callbacks

The Waulter SDK emits events at key moments in the consent lifecycle. You can subscribe to these events using JavaScript `addEventListener` on either `window` or `document`.

## Consent decision callback

The most common callback — fires when the visitor makes a consent choice (accept all, reject all, or mixed):

```javascript
window.addEventListener('Waulter:Decision', function(event) {
  var decision = event.detail.decision;   // "allow", "mixed", or "reject"
  var purposes = event.detail.purposes;   // Array of accepted purpose codes

  console.log('Consent decision:', decision);
  console.log('Accepted purposes:', purposes);

  // Example: initialise analytics only if allowed
  if (purposes.includes('PU046') || purposes.includes('PU061')) {
    initAnalytics();
  }
});
```

### Event detail schema

| Property | Type | Description |
|----------|------|-------------|
| `decision` | string | `"allow"`, `"mixed"`, or `"reject"` |
| `purposes` | string[] | Array of accepted purpose codes (empty for `"reject"`) |
| `customField1`–`customField10` | string | Custom field values (if configured) |

## SDK loaded callback

Fires when the SDK has received its configuration from the server and is ready to use:

```javascript
window.addEventListener('Waulter:Loaded', function(event) {
  console.log('Waulter SDK is ready');

  // Safe to call SDK methods now
  var categories = window.WaulterSDK.getCategories();
  console.log('Available categories:', categories);
});
```

!!! warning "Timing consideration"
    If the SDK loads before your event listener is registered, the event will be missed. For critical initialisation logic, combine the event listener with a readiness check:

    ```javascript
    function onSdkReady() {
      // Your initialisation logic
      console.log('SDK ready');
    }

    // Check if already loaded
    if (window.WaulterSDK) {
      onSdkReady();
    }
    // Also listen for future load
    window.addEventListener('Waulter:Loaded', onSdkReady);
    ```

## Banner open / close callbacks

Track when the consent banner is displayed and dismissed:

```javascript
window.addEventListener('Waulter:Open', function(event) {
  console.log('Consent banner opened');
  // Example: pause auto-play video while banner is visible
});

window.addEventListener('Waulter:Closed', function(event) {
  console.log('Consent banner closed');
  // Example: resume auto-play video
});
```

## Custom fields updated callback

Fires when custom fields are changed at runtime via `setCustomFields()`:

```javascript
window.addEventListener('Waulter:CustomFieldsUpdated', function(event) {
  console.log('Custom fields updated:', event.detail);
  // event.detail contains all current custom field values
});
```

## Login callback

Fires when the visitor clicks the Waulter login button (if login integration is enabled):

```javascript
window.addEventListener('Waulter:Login', function(event) {
  console.log('User initiated Waulter login');
});
```

## Legacy event names

For backwards compatibility, the SDK also dispatches events with `cb:` prefixed names:

| Current name | Legacy name |
|-------------|-------------|
| `Waulter:Loaded` | `cb:loaded` |
| `Waulter:Open` | `cb:open` |
| `Waulter:Decision` | `cb:decision` |
| `Waulter:Closed` | `cb:closed` |
| `Waulter:Login` | `cb:login` |

```javascript
// Legacy name — still works
window.addEventListener('cb:decision', function(event) {
  console.log('Decision:', event.detail.decision);
});
```

!!! tip "Use the `Waulter:` prefix for new code"
    Legacy `cb:` names are maintained for existing integrations. New implementations should use the `Waulter:` prefixed names.

## Event dispatch targets

All events are dispatched on **both** `window` and `document`. You can listen on either:

```javascript
// Both work identically
window.addEventListener('Waulter:Decision', handler);
document.addEventListener('Waulter:Decision', handler);
```

## Combining with data layer events

If you have `useGtm: true`, the same events are also pushed to `window.dataLayer`. You can use either approach:

| Approach | Best for |
|----------|----------|
| `addEventListener` (DOM CustomEvent) | Custom JavaScript integrations, SPAs, non-GTM setups |
| `dataLayer` + GTM triggers | GTM-managed tag firing, no custom code needed |

Both fire at the same time with the same data. Choose the approach that fits your setup. See [Events & Data Layer](../features/events.md) for the data layer approach.

## Error handling

The SDK does not throw errors for event listeners. If the SDK fails to load (network error, blocked by ad blocker, invalid configuration), events simply will not fire.

To handle SDK load failure:

```javascript
// Set a timeout to detect if SDK never loads
var sdkLoaded = false;

window.addEventListener('Waulter:Loaded', function() {
  sdkLoaded = true;
});

setTimeout(function() {
  if (!sdkLoaded) {
    console.warn('Waulter SDK did not load within 10 seconds');
    // Fallback: enable all tags, show custom consent UI, etc.
  }
}, 10000);
```
