---
title: GTM Custom HTML Tag
description: >
  Advanced GTM setup using a Custom HTML tag — full control over SDK initialization,
  async loading patterns, and fallback logic.
status: stub
---

# Custom HTML Tag

Use a GTM Custom HTML tag when you need full control over how and when the
Waulter SDK fires, or when the Community Template does not cover your use case.

<!-- TODO:
  ## Why Custom HTML?
  - Custom timing or loading strategy
  - Integration with existing consent stacks
  - Special trigger requirements

  ## Anatomy of the appendDocument pattern
  Explain the code pattern found in production deployments (document.readyState
  guard + polling loop), WHY it exists (async SDK initialisation), and when it
  fires. Include a clean annotated version of the pattern:

  ```html
  <script>
  (function() {
    // Helper: call appendDocument once SDK is ready
    var callWhenReady = function() {
      if (window.WaulterSDK && typeof window.WaulterSDK.appendDocument === 'function') {
        // SDK already ready at this moment
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

    // Guard for document.readyState
    if (document.readyState === 'loading') {
      document.addEventListener('DOMContentLoaded', callWhenReady);
    } else {
      callWhenReady();   // DOM already interactive or complete
    }
  })();
  </script>
  ```

  ## Why the polling pattern exists
  The Waulter SDK is loaded asynchronously to avoid render-blocking the page.
  There is no guaranteed order between the script that calls `appendDocument` (often
  loaded via GTM, which itself loads after the main HTML) and the SDK loader.
  The polling interval (100 ms) and 8-second timeout are chosen to balance
  responsiveness against graceful degradation.

  ## DOMContentLoaded guard — why it matters
  GTM fires after the HTML has been parsed but some integrations inject Custom HTML
  tags very early in the `<head>`. The `document.readyState` check ensures the DOM
  is available before the SDK tries to attach UI elements.
-->
