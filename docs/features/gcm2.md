---
title: Google Consent Mode 2.0
description: >
  A complete guide to implementing Google Consent Mode v2 with Waulter — including
  Basic Mode, Advanced Mode, and the required consent types.
status: stub
---

# Google Consent Mode 2.0

Google Consent Mode v2 (GCM2) became mandatory for all Google Ads advertisers
using EU traffic from **March 2024**. Waulter supports both Basic and Advanced
modes out of the box.

<!-- TODO:
  ## GCM2 overview
  - What changed from v1 to v2
  - The 7 consent types: analytics_storage, ad_storage, ad_user_data,
    ad_personalization, functionality_storage, personalization_storage, security_storage
  - New in v2: ad_user_data and ad_personalization

  ## Basic Mode vs Advanced Mode
  | Mode     | Description                                      | Modelling |
  |----------|--------------------------------------------------|-----------|
  | Basic    | No tags fire until consent is given              | No        |
  | Advanced | Tags fire in consent-denied state for modelling  | Yes       |

  ## Configuring GCM mode in Waulter
  - Dashboard setting (Basic / Advanced toggle)
  - GTM template field

  ## Default consent state
  Waulter sets default consent state to `denied` for all non-essential types
  on page load, before the banner has been interacted with.

  ## Consent signal flow diagram (Mermaid)
  ```mermaid
  sequenceDiagram
    participant Browser
    participant GTM
    participant WaulterSDK
    participant Google Tags
    Browser->>GTM: Page loads, GTM fires
    GTM->>WaulterSDK: Waulter tag fires (Consent Init trigger)
    WaulterSDK->>GTM: gtag('consent','default',{...denied...})
    Note over Google Tags: Tags wait in denied state
    Browser->>WaulterSDK: User clicks "Accept All"
    WaulterSDK->>GTM: gtag('consent','update',{...granted...})
    GTM->>Google Tags: Consent updated → tags fire
  ```

  ## Testing GCM2
  Use GTM Preview + Google's Tag Assistant to verify consent signals.
  Link to: good-practices/dev-testing.md
-->
