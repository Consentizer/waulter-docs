---
title: Async Loading & the appendDocument Pattern
description: >
  How Waulter handles asynchronous SDK initialization, the appendDocument
  polling pattern, and how to embed policy documents safely.
---

# Async Loading & appendDocument

The Waulter SDK loads asynchronously to avoid blocking page rendering. This means `window.WaulterSDK` may not be available immediately when your own scripts run. This page explains how to safely call SDK methods — particularly `appendDocument` — in an async-safe way.

## Why the SDK loads asynchronously

The SDK script (`sdk.js`) is loaded from a CDN and performs several async operations before it is ready:

1. Fetches configuration from the server
2. Checks for existing consent
3. Initialises the banner UI
4. Exposes the `window.WaulterSDK` object

Until this process completes, `window.WaulterSDK` is `undefined`. Any code that calls SDK methods must account for this.

## The polling pattern

The recommended approach is to **poll** for the SDK at a short interval:

```javascript
(function() {
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

  // Guard: wait for DOM to be ready before attaching content
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', callWhenReady);
  } else {
    callWhenReady();
  }
})();
```

### How it works

| Step | What happens |
|------|-------------|
| 1. Immediate check | If the SDK is already loaded, call the method right away |
| 2. Start polling | Every 100 ms, check if `window.WaulterSDK` exists |
| 3. Success | SDK found — clear the interval and call the method |
| 4. Timeout | After 8 seconds, stop polling and log a warning |
| 5. DOM guard | If the script runs before the DOM is ready, wait for `DOMContentLoaded` |

!!! tip "Adjusting the timeout"
    The 8-second timeout works well for most connections. On very slow networks, you may increase it to 15 seconds. The 100 ms polling interval is lightweight and should not be changed.

## Using appendDocument

The `appendDocument` method renders a managed legal document (Cookie Policy, Privacy Policy) into a DOM container on your page.

### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `containerId` | string | The `id` attribute of the HTML element where the document will be injected |
| `documentId` | string | The document ID from the Waulter dashboard (e.g. `YOUR_DOC_ID`) |

### HTML setup

Your page must include a container element:

```html
<div id="your-element-id"></div>
```

The SDK will inject the document content into this element. The container can be placed anywhere in your page body.

### Static HTML example

```html
<h1>Cookie Policy</h1>
<div id="your-element-id">
  <p>Loading cookie policy...</p>
</div>

<script>
  // Assumes SDK is loaded (e.g. in <head>)
  if (window.WaulterSDK && window.WaulterSDK.appendDocument) {
    window.WaulterSDK.appendDocument('your-element-id', 'YOUR_DOC_ID');
  }
</script>
```

!!! warning "Use the polling pattern for reliability"
    The simple `if` check above only works if the SDK has already finished loading. For production use, always use the [polling pattern](#the-polling-pattern) to handle timing reliably.

### SPA / React example

```jsx
import { useEffect, useRef } from 'react';

function CookiePolicy() {
  const containerRef = useRef(null);

  useEffect(() => {
    const checkSDK = setInterval(() => {
      if (window.WaulterSDK?.appendDocument) {
        clearInterval(checkSDK);
        window.WaulterSDK.appendDocument('your-element-id', 'YOUR_DOC_ID');
      }
    }, 100);

    const timeout = setTimeout(() => clearInterval(checkSDK), 8000);

    return () => {
      clearInterval(checkSDK);
      clearTimeout(timeout);
    };
  }, []);

  return (
    <div>
      <h1>Cookie Policy</h1>
      <div id="your-element-id" ref={containerRef}>
        <p>Loading cookie policy...</p>
      </div>
    </div>
  );
}
```

## Using the polling pattern for other SDK methods

The same pattern works for any SDK method, not just `appendDocument`:

```javascript
// Open the preference centre when SDK is ready
var check = setInterval(function() {
  if (window.WaulterSDK && window.WaulterSDK.openWidget) {
    clearInterval(check);
    window.WaulterSDK.openWidget();
  }
}, 100);
setTimeout(function() { clearInterval(check); }, 8000);
```

## Listening for SDK readiness via events

As an alternative to polling, you can listen for the `Waulter:Loaded` event:

```javascript
window.addEventListener('Waulter:Loaded', function() {
  // SDK is now ready
  window.WaulterSDK.appendDocument('your-element-id', 'YOUR_DOC_ID');
});
```

!!! info "Event may fire before your listener"
    If the SDK loads before your event listener is registered, you will miss the event. The polling pattern is more robust because it handles both cases (SDK already loaded, or not yet loaded).

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Document does not appear | Container element missing | Ensure `<div id="your-element-id"></div>` exists in the DOM |
| "SDK not ready after 8 s" | SDK blocked or failed to load | Check browser Network tab for `sdk.js` errors |
| Document appears briefly then vanishes | SPA re-render clears the container | Move the container outside the SPA's root, or re-call `appendDocument` after navigation |
| Multiple documents rendered | `appendDocument` called more than once | Use a flag or guard to prevent duplicate calls |
