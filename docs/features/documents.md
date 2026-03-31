---
title: Policy Documents
description: >
  How Waulter renders legal documents (Cookie Policy, Privacy Policy) inside the
  consent banner or on a standalone page — and how to implement them.
status: stub
---

# Policy Documents

Waulter can render versioned legal documents (Cookie Policy, Privacy Policy, Terms)
directly on your website using the `appendDocument` SDK function.

<!-- TODO:
  ## What appendDocument does
  Injects a managed, versioned document into a DOM element of your choosing.
  The document content is maintained in the Waulter dashboard — no code deploy
  needed when the legal text changes.

  ## Implementation patterns

  ### Pattern 1: Static HTML page
  ```html
  <div id="waulterCookies"></div>
  <script>
    // Assumes WaulterSDK is already initialised
    window.WaulterSDK.appendDocument('waulterCookies', 'YOUR_DOC_ID');
  </script>
  ```

  ### Pattern 2: Via GTM Custom HTML (async-safe)
  Refer to: implementation/gtm/custom-html.md — the full polling pattern.
  Use this when the SDK may not yet be initialised at the time your script runs.

  ### Pattern 3: SPA / React integration
  ```jsx
  useEffect(() => {
    if (window.WaulterSDK?.appendDocument) {
      window.WaulterSDK.appendDocument('waulterCookies', 'YOUR_DOC_ID');
    }
  }, []);
  ```

  ## Document IDs
  Each managed document has a unique ID (e.g. "AG1035"). Find it in the
  Waulter dashboard under Documents.

  ## Why the polling pattern is needed
  The Waulter SDK loads asynchronously. If you call `appendDocument` before the
  SDK script has finished executing, `window.WaulterSDK` will be undefined.
  The polling pattern (100 ms interval, 8-second timeout) solves this reliably.
  See: implementation/gtm/custom-html.md for the full annotated pattern.

  ## Document container element
  The first argument to `appendDocument` is the ID of the HTML element where the
  document will be injected. The element must exist in the DOM when the function fires.

  ## Styling documents
  Documents inherit your site's CSS. Waulter wraps content in a
  `.waulter-document` container class that you can target.
-->
