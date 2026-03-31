---
title: Waulter Cookies
description: >
  What cookies the Waulter SDK sets, where they are stored, their purpose and lifetime,
  consent persistence, and how User Sharing affects cookie state across domains.
status: stub
---

# Waulter Cookies

The Waulter SDK stores consent state and configuration data using first-party cookies and localStorage. This page documents exactly what is stored, where, and why.

<!-- TODO:
  ## Cookie table

  | Name | Type | Storage | Purpose | Expiry |
  |------|------|---------|---------|--------|
  | `vaswaulter` | First-party cookie | Cookie | Stores the visitor's consent state and decisions | [TBD] |
  | `notAfter` | First-party cookie | Cookie | Consent validity expiration timestamp | [TBD] |
  | [consent storage key] | localStorage | localStorage | Backup/mirror of consent state for resilience | Persistent until cleared |

  ## Where consent is stored
  - **First-party cookies**: primary storage mechanism, scoped to the domain where Waulter is deployed
  - **localStorage**: used as a fallback/mirror for consent state persistence
  - No third-party cookies are set by the Waulter SDK

  ## Consent persistence
  - Consent decisions persist across sessions until the consent validity period expires
  - Validity period is configurable in the dashboard
  - Returning visitors with valid consent are not shown the banner again

  ## What happens when cookies are cleared
  - If the visitor clears their cookies, consent state is lost
  - The banner will appear again on the next visit as if they are a new visitor
  - localStorage backup may restore state if cookies are cleared but localStorage is not
  - GCM signals will default to `denied` until new consent is given

  ## User Sharing and cookie state
  - When User Sharing is enabled, consent state can be carried across domains
  - The cookie/localStorage state on Domain B is populated from the shared consent record
  - See: [User Sharing](user-sharing.md) for the full cross-domain mechanism

  ## Privacy and compliance
  - All Waulter cookies are first-party and essential for CMP functionality
  - They are categorised as "strictly necessary" and do not require consent themselves
  - Cookie names and purposes should be listed in your Cookie Policy
-->
