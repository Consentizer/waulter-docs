---
title: Events & Data Layer
description: >
  All dataLayer events emitted by the Waulter SDK — Waulter:Loaded, Waulter:Open,
  Waulter:Decision, Waulter:Closed, Waulter:Login, Waulter:CustomFieldsUpdated —
  plus event schemas, lifecycle scenarios, and GTM trigger patterns.
status: stub
---

# Events & Data Layer

The Waulter SDK pushes structured events to `window.dataLayer` at key moments in the consent lifecycle. These events enable you to build GTM triggers, track consent analytics, and integrate with your existing tag management setup.

<!-- TODO:
  ## Event reference

  ### Waulter:Loaded
  - Fired when the Waulter SDK has fully initialised
  - Use this to confirm the SDK is active on the page

  ### Waulter:Open
  - Fired when the consent banner is displayed to the visitor

  ### Waulter:Decision
  - Fired when the visitor makes a consent decision (accept, reject, or mixed)
  - **Event schema:**
    ```json
    {
      "event": "Waulter:Decision",
      "decision": "allow",       // "allow" | "mixed" | "reject"
      "purposes": [
        { "name": "analytics", "granted": true },
        { "name": "marketing", "granted": true }
      ]
    }
    ```
  - `decision` values:
    - `allow` — visitor accepted all purposes
    - `mixed` — visitor customised (some accepted, some rejected)
    - `reject` — visitor rejected all non-essential purposes

  ### Waulter:Closed
  - Fired when the consent banner or preference centre is closed

  ### Waulter:Login
  - Fired when a user logs in via the Waulter SDK (if login integration is enabled)

  ### Waulter:CustomFieldsUpdated
  - Fired when custom fields are updated at runtime via `setCustomFields()`
  - Includes the updated custom field values in the event payload

  ## Event lifecycle scenarios

  ### New visitor (first visit)
  1. `Waulter:Loaded` — SDK initialises
  2. `Waulter:Open` — banner is shown
  3. `Waulter:Decision` — visitor makes a choice
  4. `Waulter:Closed` — banner closes

  ### Returning visitor (consent already given)
  1. `Waulter:Loaded` — SDK initialises, reads stored consent
  2. Banner is NOT shown (consent is still valid)
  3. GCM update signal fires with stored consent state

  ### Reject all flow
  1. `Waulter:Loaded` → `Waulter:Open` → `Waulter:Decision` (decision: "reject") → `Waulter:Closed`

  ### Mixed consent flow
  1. `Waulter:Loaded` → `Waulter:Open` → visitor opens preference centre → `Waulter:Decision` (decision: "mixed") → `Waulter:Closed`

  ## GCM default / update consent mapping
  - On page load (before interaction): `gtag('consent', 'default', { ... })` with all non-essential types set to `denied`
  - After consent decision: `gtag('consent', 'update', { ... })` with types mapped from Waulter purposes

  ## JavaScript addEventListener pattern
  ```js
  window.addEventListener('Waulter:Decision', function(event) {
    console.log('Consent decision:', event.detail.decision);
    console.log('Purposes:', event.detail.purposes);
  });
  ```

  ## Building GTM triggers from events
  - Create a Custom Event trigger in GTM with event name `Waulter:Decision`
  - Add conditions to fire tags only when specific purposes are granted
  - Example: fire analytics tag when `decision === 'allow'` or when analytics purpose is granted in a mixed decision

  ## Special messages
  - Marketing message event — fired when a marketing-related consent interaction occurs
  - How to listen for and use special message events

  ## Legacy event names
  - `cb:decision` — the legacy event name for `Waulter:Decision`
  - Legacy names are still emitted for backwards compatibility
  - New implementations should use the `Waulter:` prefixed names
-->
