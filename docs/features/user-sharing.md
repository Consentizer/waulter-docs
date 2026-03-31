---
title: User Sharing (Cross-Domain Consent)
description: >
  How Waulter's User Sharing feature carries a visitor's consent across multiple
  domains without prompting them again.
status: stub
---

# User Sharing

User Sharing allows your visitors to consent once and have that decision honoured
across your entire domain portfolio — without being shown the banner again on every site.

<!-- TODO:
  ## How it works
  - Consent is stored against a persistent user identifier (not a third-party cookie)
  - When the user visits Domain B, Waulter checks for an existing consent record
    linked to that identifier
  - If found and within the consent validity period: banner is suppressed
  - If not found or expired: new consent collection begins

  ## Setting up User Sharing
  Step-by-step: enable in dashboard, configure participating domains.

  ## The consent handoff mechanism
  Explain the URL parameter / localStorage approach used.
  DO NOT DISCLOSE: internal cryptographic details, token signing,
  or server-side session architecture.

  ## Privacy considerations
  - Consent decisions are scoped to the user's browser session / identifier
  - The mechanism is GDPR-compliant (no cross-site tracking beyond consent signal)
  - Users can always withdraw consent, which propagates across shared domains

  ## Limitations
  - Requires all participating domains to have Waulter deployed
  - Works within the same Waulter account's domain portfolio
  - Browser privacy modes (Safari ITP, Firefox ETP) may affect persistence

  ## User Sharing and statistics
  Explain how shared consents are counted in the dashboard (which domain
  "owns" the consent event).
-->
