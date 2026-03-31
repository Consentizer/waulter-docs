---
title: DEV & Testing Workflow
description: >
  How to safely develop and test Waulter before deploying to production — using
  Configuration naming conventions, Scenarios, and whitelisted domains.
---

# DEV & Testing Workflow

Testing a CMP before going live requires a disciplined setup. This guide walks you through the recommended approach for developing and testing Waulter without affecting your production environment.

## The DEV / PROD configuration pair

Create **two** configurations in the Waulter dashboard:

- `[PROD] My Site` — your live configuration, serving real visitors
- `[DEV] My Site` — an identical copy used only for testing

The `[DEV]` and `[PROD]` prefixes make it impossible to confuse them in the dashboard and in GTM variables.

!!! warning "Never test on your PROD configuration"
    Testing purposes, GCM mode changes, or text modifications on your production configuration creates noise in your consent statistics and can temporarily affect live GCM signals. Always test on a separate DEV configuration.

## Setting up DEV / PROD separation

### Option 1: Scenario-based targeting

Create a scenario that routes DEV traffic to the DEV configuration and PROD traffic to PROD:

| Rule | Condition | Target |
|------|-----------|--------|
| Rule 1 | `url contains "staging.example.com"` | `[DEV]` configuration |
| Rule 2 | `customField1 equals "dev"` | `[DEV]` configuration |
| Fallback | — | `[PROD]` configuration |

This way, a single Scenario ID in your SDK deployment automatically selects the right configuration based on the environment.

### Option 2: Separate Configuration IDs

Use different Configuration IDs in your DEV and PROD environments:

```javascript
// DEV environment
window.WaulterConfig = { id: "AG0001", useGtm: true }; // DEV config

// PROD environment
window.WaulterConfig = { id: "AG0002", useGtm: true }; // PROD config
```

In GTM, use a **Lookup Table** variable to select the right ID based on the hostname:

| Input (hostname) | Output (Configuration ID) |
|-----------------|--------------------------|
| `staging.example.com` | `AG0001` (DEV) |
| `www.example.com` | `AG0002` (PROD) |

## Whitelisted domains and testing

Your DEV domain (e.g. `staging.example.com`) should be whitelisted in your **DEV configuration**. This way:

- DEV consent events appear in DEV statistics (not polluting PROD stats)
- You get real data on banner impressions and consent rates during testing

!!! info "Whitelisting does not gate banner display"
    Forgetting to whitelist your DEV domain does **not** prevent the banner from showing. It only means DEV sessions won't be counted in statistics. The banner still works — consent is collected and GCM signals fire.

## GTM Preview mode testing

1. Open GTM **Preview** for your container.
2. Navigate to your test site.
3. Verify in the Tag Assistant panel:

| Check | Expected |
|-------|----------|
| Waulter tag fires | On **Consent Initialization** trigger |
| `gtm.init_consent` event | Present in the data layer |
| Default consent state | All signals show `denied` |
| Accept All | `Waulter:Decision` with `decision: "allow"`, signals change to `granted` |
| Reject All | `Waulter:Decision` with `decision: "reject"`, signals remain `denied` |
| Mixed consent | `Waulter:Decision` with `decision: "mixed"`, only relevant signals `granted` |

4. Check the **Consent** tab in Tag Assistant to verify GCM signal states.

## Testing with Google Tag Assistant

For a deeper GCM verification:

1. Install the [Google Tag Assistant](https://tagassistant.google.com/) browser extension.
2. Open your test site.
3. Tag Assistant shows the consent state for each Google tag.
4. Verify that tags respect the consent signals (fire after consent, blocked before consent).

## Copying configuration between DEV and PROD

Once your DEV configuration is finalised:

1. In the dashboard, use the **Duplicate** action on your DEV configuration.
2. Rename the copy with the `[PROD]` prefix.
3. Update whitelisted domains to PROD domains.
4. Update the Configuration ID in your GTM PROD setup (or update the scenario to point to the new config).
5. Publish your GTM PROD container.

!!! tip "Never manually re-create PROD from scratch"
    Always duplicate from DEV. This ensures no setting is missed and both environments are identical (except for domain-specific values).

## Checklist before go-live

- [ ] PROD configuration has correct legal texts in all active languages
- [ ] PROD configuration is active
- [ ] PROD domain(s) are whitelisted
- [ ] GTM PROD container uses PROD Configuration ID (or Scenario ID)
- [ ] GCM mode is set to the agreed mode (Basic or Advanced)
- [ ] Consent banner tested in Chrome, Firefox, Safari, Edge
- [ ] Consent banner tested on mobile devices
- [ ] GTM Preview confirms correct data layer events
- [ ] Google Tag Assistant confirms GCM signals
- [ ] Consent-dependent tags fire only after consent
- [ ] `?no_waulter_cb` suppresses the banner on cookie policy pages
- [ ] Re-open button (`openWidget()`) works on all pages
- [ ] [Accessibility](../accessibility/index.md) spot check completed

## Debugging tips

| Issue | How to debug |
|-------|-------------|
| Banner not appearing | Check browser console for errors; verify Configuration ID; check whitelisted domains |
| Wrong configuration loading | Verify Scenario ID and rules; check custom field values; enable `debug: true` |
| GCM signals not updating | Verify `useGtm: true`; check GTM container is published; review Tag Assistant |
| Events not in data layer | Check `useGtm: true`; ensure the tag fires on Consent Initialization |
| Consent not persisting | Check `vaswaulter` cookie; verify consent duration settings; test in a non-incognito window |
