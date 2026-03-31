---
title: Consent Strategy Checklist
description: >
  A comprehensive checklist for planning, implementing, and maintaining your
  cookie consent strategy with Waulter CMP.
---

# Consent Strategy Checklist

Use this checklist to plan, implement, and maintain your consent strategy. Each item links to the relevant documentation for details.

---

## 1. Pre-implementation audit

Before touching any configuration, understand what you're working with.

- [ ] **Inventory all cookies and tracking scripts** on your site — use browser DevTools (Application > Cookies) or a cookie scanning tool
- [ ] **Classify each cookie**: essential, analytics, marketing, personalisation — see [Purposes](../features/purposes.md) for the standard categories
- [ ] **Identify all third-party scripts** (Google Analytics, Meta Pixel, Hotjar, LinkedIn Insight, etc.) and which cookies they set
- [ ] **Check which cookies fire before consent** — this is a compliance risk. Load your site in incognito with DevTools open and note any cookies set before interaction
- [ ] **Document your legal basis** for each data processing activity (consent, legitimate interest, contractual necessity)
- [ ] **Review your Cookie Policy and Privacy Policy** for completeness — do they list all cookies, purposes, and third parties?

!!! danger "Pre-consent cookies = compliance risk"
    Any non-essential cookie that fires before the visitor has consented is a GDPR violation. This is the single most common compliance issue. Your audit should prioritise identifying these.

---

## 2. Configuration setup

Set up your Waulter environment in the [dashboard](../dashboard/index.md).

- [ ] **Create your Waulter account** and partner organisation
- [ ] **Set up DEV and PROD configurations** — see [DEV & Testing Workflow](dev-testing.md) for the recommended pattern
- [ ] **Select and configure purposes** matching your cookie inventory — see [Purposes](../features/purposes.md)
- [ ] **Map cookies to purposes** in the dashboard — see [Cookie Detection](../dashboard/cookie-detection.md)
- [ ] **Configure GCM mode**: Basic or Advanced — see [Google Consent Mode 2.0](../features/gcm2.md)
- [ ] **Set consent duration** (default 90 days) — adjust per your legal requirements
- [ ] **Whitelist your production domains** — see [Configurations](../dashboard/configurations.md)
- [ ] **Upload or configure policy documents** (Cookie Policy, Privacy Policy) — see [Policy Documents](../features/documents.md)

!!! tip "DEV before PROD"
    Always configure and test on a DEV configuration first. Only deploy to PROD after verifying everything works. See [DEV & Testing Workflow](dev-testing.md).

---

## 3. Banner and UX design

Design a consent experience that is clear, accessible, and on-brand.

- [ ] **Choose a banner template** — see [Styling & Templates](../dashboard/styling.md) for built-in options
- [ ] **Configure banner text** for each language you support — see [Texts & Translations](../dashboard/texts.md)
- [ ] **Set up the floating consent button** — or plan an alternative (footer link, settings page). See [Floating Button](../features/floating-button.md)
- [ ] **Verify colour contrast** meets WCAG 2.1 AA: 4.5:1 for text, 3:1 for UI components — see [WCAG Compliance](../accessibility/wcag.md)
- [ ] **Test banner on mobile devices** — touch targets must be at least 44x44 px. See [Accessibility](../accessibility/index.md)
- [ ] **Configure special messages** if using consent as a marketing touchpoint — see [Special Messages](../features/special-messages.md)

---

## 4. Technical implementation

Deploy the SDK and wire up your tags.

- [ ] **Choose your implementation method**: [GTM Template](../implementation/gtm/community-template.md), [GTM Custom HTML](../implementation/gtm/custom-html.md), or [Direct Script](../implementation/direct/index.md)
- [ ] **Import the GTM Scaffold file** if using GTM — see [GTM Scaffold](../implementation/gtm/scaffold.md)
- [ ] **Configure purpose-level triggers** — do not rely solely on GCM signals. See [Why purpose-level control matters](../implementation/gtm/custom-html.md#why-purpose-level-control-matters)
- [ ] **Set up exception triggers** for tags that need granular consent — see [Triggers and exception control](../implementation/gtm/custom-html.md#triggers-and-exception-control)
- [ ] **Verify all non-essential tags are gated** behind consent triggers
- [ ] **Test with GTM Preview mode** — verify consent state transitions in the Consent tab

!!! warning "GCM signals are not enough"
    Multiple purposes map to the same GCM signal. A tag gated only by `analytics_storage` will fire if *any* analytics purpose is accepted — even if the visitor rejected the specific purpose that tag requires. Use purpose-level triggers for strict compliance.

---

## 5. Consent flow verification

Test every consent scenario before going live.

- [ ] **Fresh visitor**: banner appears, all GCM signals default to `denied` — see [Events](../features/events.md)
- [ ] **Accept All**: all signals flip to `granted`, all consent-dependent tags fire
- [ ] **Reject All**: signals stay `denied`, no optional tags fire
- [ ] **Mixed consent**: only accepted purpose signals become `granted`
- [ ] **Returning visitor**: banner suppressed, stored consent applied on page load
- [ ] **Consent withdrawal**: visitor can change preferences via [floating button](../features/floating-button.md) or your alternative
- [ ] **Clear consent test**: delete `vaswaulter` cookie + localStorage entries, verify banner reappears — see [Waulter Cookies](../features/cookies.md)

!!! tip "Clear all Waulter storage"
    When testing as a new visitor, delete **both** the `vaswaulter` cookie and any Waulter entries in `localStorage`. The SDK checks both — deleting only the cookie is not enough.

---

## 6. Scenarios (if using)

If you use [Scenarios](../dashboard/scenarios.md) for rule-based configuration selection:

- [ ] **Define your scenario rules** (URL, custom fields, pageview count, etc.)
- [ ] **Set a fallback configuration** — this loads when no rules match
- [ ] **Test rule evaluation order** — first match wins. See [Scenarios](../dashboard/scenarios.md)
- [ ] **Test `forceStartCB`** if using re-consent or engagement flows — see [Scenario-Driven Engagement](scenario-engagement.md)
- [ ] **Verify scenario targeting** with [Custom Fields](../sdk/custom-fields.md) if using dynamic values

---

## 7. Multilanguage (if applicable)

If your site serves multiple languages:

- [ ] **Configure translations** for all active languages in the dashboard — see [Texts & Translations](../dashboard/texts.md)
- [ ] **Set the `lang` parameter** in WaulterConfig or rely on the configuration's default language — see [Multilanguage Setup](multilanguage.md)
- [ ] **Test each language variant** — load the site with different language settings
- [ ] **Verify policy documents exist in all languages** — see [Policy Documents](../features/documents.md)

---

## 8. Agency setup (if managing clients)

If you manage multiple client configurations:

- [ ] **Create template configurations** for quick client onboarding — see [Template Configs](../agency/templates.md)
- [ ] **Set up User Sharing** for client access (local or global) — see [User Sharing](../features/user-sharing.md)
- [ ] **Apply naming conventions** (DEV/PROD prefixes, client codes) — see [Naming Conventions](naming-conventions.md)
- [ ] **Document the handoff process** for ownership transfer — see [Transfer of Ownership](../agency/transfer.md)

---

## 9. Go-live verification

Final checks before publishing to production.

- [ ] **Run Waulter's automated compliance monitoring** — see [Compliance Reports](../dashboard/compliance.md)
- [ ] **Review the master check diff** — no cookies firing before consent. See [Cookie Detection](../dashboard/cookie-detection.md)
- [ ] **Verify GCM signals** in Google Tag Assistant (GTM Preview > Consent tab)
- [ ] **Test on Chrome, Firefox, and Safari** — Safari's ITP and Firefox's ETP may affect cookie behaviour. See [Waulter Cookies](../features/cookies.md)
- [ ] **Test on mobile** — both iOS Safari and Android Chrome
- [ ] **Accessibility check**: keyboard navigation, screen reader, colour contrast — see [Accessibility](../accessibility/index.md)
- [ ] **Verify statistics are being collected** — ensure domains are whitelisted. See [Statistics](../dashboard/statistics.md)
- [ ] **Confirm Permission Transaction IDs** are being generated — see [Permission Transaction ID](../features/permission-tx.md)

!!! tip "Browser diversity matters"
    Safari and Firefox apply privacy protections that can affect cookie persistence. Always test on these browsers, not just Chrome.

---

## 10. Ongoing maintenance

Consent is not a one-time setup — it requires ongoing attention.

- [ ] **Monitor consent rates** in the [Statistics dashboard](../dashboard/statistics.md) — sudden drops may indicate UX or technical issues
- [ ] **Review compliance reports** after each site deployment — new scripts may introduce unconsented cookies
- [ ] **Update your cookie inventory** when adding new integrations (new analytics tools, chat widgets, ad pixels)
- [ ] **Re-review your consent strategy** when purposes or legal requirements change
- [ ] **Keep the GTM Scaffold file updated** with new releases from the [GitHub repository](https://github.com/Consentizer/Waulter-CMP-Google-Tag-Manager)
- [ ] **Audit User Sharing access** periodically — remove users who no longer need access. See [User Sharing](../features/user-sharing.md)
- [ ] **Check for banner text updates** when your Privacy Policy or Cookie Policy changes

!!! info "Schedule regular reviews"
    Set a quarterly reminder to review your consent setup. Cookie landscapes change — new integrations, new regulations, and new browser privacy features all affect your compliance posture.
