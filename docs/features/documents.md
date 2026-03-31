---
title: Policy Documents
description: >
  How Waulter renders legal documents (Cookie Policy, Privacy Policy) inside the
  consent banner or on a standalone page — and how to implement them.
---

# Policy Documents

Waulter can render versioned legal documents — Cookie Policy, Privacy Policy, Terms of Service — directly on your website using the `appendDocument` SDK method. Document content is maintained in the Waulter dashboard, so updates to legal text require **no code deploy**.

## What appendDocument does

The `appendDocument` method fetches a managed document from Waulter's servers and injects its HTML content into a DOM element on your page.

- **Versioned** — each document has a unique ID and version history
- **Centrally managed** — edit text in the dashboard, changes appear on your site immediately
- **Styled** — content inherits your site's CSS and is wrapped in a `.waulter-document` container class

!!! info "The element ID is your choice"
    The first argument to `appendDocument` is the `id` of any HTML element on your page where the document will be rendered. You choose this ID — it can be anything valid (`cookie-policy`, `privacy-doc`, `terms-container`, etc.). Just make sure the `<div>` with that ID exists in the DOM when the function fires.

## Document IDs

Each managed document has a unique ID (e.g. `YOUR_DOC_ID`). Find it in the Waulter dashboard under your configuration's **Documents** section.

!!! info "Where to find your Document ID"
    In the Waulter dashboard, navigate to your website configuration > **Documents**. Each document (Cookie Policy, Privacy Policy) shows its unique ID.

## Implementation patterns

### Pattern 1: Static HTML page

The simplest approach — suitable when the SDK is loaded in `<head>` and you are confident it is ready:

```html
<h1>Cookie Policy</h1>
<div id="your-element-id">
  <p>Loading cookie policy...</p>
</div>

<script>
  if (window.WaulterSDK && window.WaulterSDK.appendDocument) {
    window.WaulterSDK.appendDocument('your-element-id', 'YOUR_DOC_ID');
  }
</script>
```

!!! warning "SDK timing"
    This simple check only works if the SDK has already finished loading. For production use, always use the async-safe pattern below.

### Pattern 2: Async-safe with polling (recommended)

The SDK loads asynchronously, so you cannot guarantee it is ready when your script runs. The polling pattern handles this reliably:

```html
<h1>Cookie Policy</h1>
<div id="your-element-id">
  <p>Loading cookie policy...</p>
</div>

<script>
(function() {
  var callWhenReady = function() {
    if (window.WaulterSDK && typeof window.WaulterSDK.appendDocument === 'function') {
      window.WaulterSDK.appendDocument('your-element-id', 'YOUR_DOC_ID');
      return;
    }

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

  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', callWhenReady);
  } else {
    callWhenReady();
  }
})();
</script>
```

This is the same pattern used for [GTM Custom HTML tags](../implementation/gtm/custom-html.md#the-appenddocument-polling-pattern).

### Pattern 3: GTM Custom HTML tag

When deploying via GTM, create a Custom HTML tag with the polling pattern. This is useful when you want to manage document rendering through your tag manager.

See the full [GTM Custom HTML guide](../implementation/gtm/custom-html.md#the-appenddocument-polling-pattern) for the annotated pattern.

### Pattern 4: SPA / React

```jsx
import { useEffect } from 'react';

function CookiePolicy() {
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
      <div id="your-element-id">
        <p>Loading cookie policy...</p>
      </div>
    </div>
  );
}

export default CookiePolicy;
```

!!! tip "SPA navigation"
    If your SPA re-renders the component on navigation, `appendDocument` will be called again via the `useEffect` cleanup and re-run cycle. This is the expected behaviour.

## Container element

The first argument to `appendDocument` is the `id` attribute of the HTML element where the document will be injected. The element **must** exist in the DOM when the function is called.

```html
<!-- This element receives the document content -->
<div id="your-element-id"></div>
```

You can use any block-level element (`div`, `section`, `article`). The element can contain placeholder content (like "Loading...") which will be replaced when the document loads.

## Styling documents

The injected document content inherits your site's CSS. Waulter wraps the content in a container with the `.waulter-document` class, which you can target for custom styling:

```css
/* Custom styles for Waulter documents */
.waulter-document {
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
  font-size: 0.95rem;
  line-height: 1.6;
}

.waulter-document h2 {
  margin-top: 2rem;
  border-bottom: 1px solid #eee;
  padding-bottom: 0.5rem;
}

.waulter-document table {
  width: 100%;
  border-collapse: collapse;
}

.waulter-document table td,
.waulter-document table th {
  padding: 0.5rem;
  border: 1px solid #ddd;
}
```

## Suppressing the consent banner on document pages

Cookie policy and privacy policy pages should not show the consent banner (to avoid a circular consent prompt). Add `?no_waulter_cb` to the page URL:

```
https://example.com/cookie-policy?no_waulter_cb
```

Or configure the URL in your site's routing so the banner suppression parameter is always present on legal document pages.

!!! warning "appendDocument still works with `?no_waulter_cb`"
    The `?no_waulter_cb` parameter only suppresses the consent banner — it does not prevent the SDK from loading. Document rendering via `appendDocument` still works.

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Document does not appear | Container element missing from DOM | Add `<div id="your-element-id"></div>` to the page |
| Document does not appear | SDK not loaded | Use the polling pattern instead of a simple `if` check |
| "SDK not ready after 8 s" | SDK blocked (ad blocker, CSP, network) | Check browser Network tab for `sdk.js` errors |
| Document appears then disappears | SPA framework re-renders the component | Ensure `appendDocument` runs after re-render (use `useEffect`) |
| Styling looks wrong | Site CSS conflicts | Use `.waulter-document` selector to scope styles |
| Wrong document displayed | Incorrect Document ID | Verify the ID in the dashboard Documents section |
