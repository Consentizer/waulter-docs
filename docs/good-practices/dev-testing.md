---
title: DEV & Testing Workflow
description: >
  How to safely develop and test Waulter before deploying to production — using
  Configuration naming conventions, Scenarios, and whitelisted domains.
status: stub
---

# DEV & Testing Workflow

Testing a CMP before going live requires a disciplined setup. This guide walks you
through the recommended approach used by Waulter's own implementation team.

<!-- TODO:
  ## The DEV / PROD configuration pair

  Create **two** configurations in the Waulter dashboard:
  - `[PROD] My Site` — your live configuration
  - `[DEV] My Site` — an identical copy used only for testing

  The naming convention `[DEV]` and `[PROD]` prefixes (or `DEV_` and `PROD_`)
  makes it impossible to confuse them in the dashboard and in GTM variables.

  !!! warning "Never test on your PROD configuration"
      Testing purposes or GCM mode changes on your production configuration
      creates noise in your consent statistics and can temporarily affect
      live GCM signals.

  ## Scenario-based targeting for DEV traffic

  Create a DEV Scenario that targets:
  - A specific subdomain (e.g. `dev.mysite.com`), OR
  - A URL pattern (e.g. `/staging/`), OR
  - A GTM Preview mode flag

  Map this Scenario to your `[DEV]` configuration so DEV traffic always
  loads DEV config, and PROD traffic always loads PROD config.

  ## Whitelisted domains and testing

  Your DEV domain (e.g. `dev.mysite.com`) should be whitelisted in your DEV
  configuration. This way:
  - DEV consent events appear in your DEV statistics (not polluting PROD stats)
  - You get real data on banner impression and consent rates during testing

  !!! info "Whitelisting does not gate banner display"
      Forgetting to whitelist your DEV domain does NOT prevent the banner from
      showing. It only means DEV sessions won't be counted in statistics.
      See: [Scenarios & Whitelisted Domains](../features/scenarios.md)

  ## GTM Preview mode testing

  1. Open GTM Preview for your container
  2. Navigate to your test site
  3. Verify: Waulter tag fires on Consent Initialization trigger
  4. Verify: `waulter_consent_default` appears in dataLayer
  5. Accept / Reject consent in the banner
  6. Verify: `waulter_consent_update` appears in dataLayer with correct purpose values
  7. Check GTM's Consent State tab to confirm GCM signals

  ## Copying configuration between DEV and PROD

  Once your DEV configuration is finalised:
  1. In the Waulter dashboard, use the **Duplicate Configuration** action
  2. Rename the copy with the `[PROD]` prefix
  3. Update the Configuration ID in your GTM PROD variable
  4. Publish your GTM PROD container

  Never manually re-create PROD from scratch — duplication ensures nothing
  is missed.

  ## Checklist before go-live

  - [ ] PROD configuration has correct legal texts in all active languages
  - [ ] PROD configuration is mapped to a PROD Scenario
  - [ ] PROD domain is whitelisted
  - [ ] GTM PROD container uses PROD Configuration ID
  - [ ] GCM mode is set to the agreed mode (Basic or Advanced)
  - [ ] Consent banner tested in Chrome, Firefox, Safari, mobile
  - [ ] GTM Preview confirms correct dataLayer events
  - [ ] Google Tag Assistant confirms GCM signals
  - [ ] Accessibility check completed (see [Accessibility](../accessibility/index.md))
-->
