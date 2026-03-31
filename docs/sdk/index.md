---
title: SDK Overview
description: The Waulter JavaScript SDK — what it exposes and how to work with it.
status: stub
---

# SDK Overview

The Waulter SDK exposes a global `window.WaulterSDK` object after initialisation.
All public methods are documented in this section.

<!-- TODO:
  ## When is the SDK ready?
  Initialisation is asynchronous. Always guard SDK calls:
  ```js
  if (window.WaulterSDK) {
    // safe to call
  }
  ```
  Or use the polling pattern for non-critical, deferred calls.
  See: implementation/gtm/custom-html.md

  ## Available public methods (summary table)
  | Method                       | Returns  | Description                        |
  |------------------------------|----------|------------------------------------|
  | `appendDocument(el, docId)`  | void     | Render a policy document           |
  | `openPreferences()`          | void     | Open the preference centre         |
  | `getConsentState()`          | object   | Current per-purpose consent object |
  | `onConsentUpdate(callback)`  | void     | Register a consent-change listener |
  | `revokeConsent()`            | void     | Programmatically withdraw consent  |

  Link to full API reference: sdk/api-reference.md
-->
