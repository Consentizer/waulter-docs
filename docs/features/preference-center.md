---
title: Native Preference Center
description: >
  Embed a fully managed consent preference center directly into your application —
  no widget development needed. Waulter handles the UI, logic, and audit trail.
---

# Native Preference Center

Most consent management platforms force you into a popup — a floating overlay that sits on top of your application. For many products, especially SaaS platforms, dashboards, and member portals, this feels foreign and disconnected from the user experience.

Waulter offers a different approach: a **native preference center** that you embed directly into your application's settings or account pages, making consent management feel like a natural part of your product.

## What is the native preference center?

Instead of a popup widget, Waulter renders a **consent management panel** inside a DOM element you choose — just like [Policy Documents](documents.md). The panel includes:

- All configured purposes with descriptions
- Toggle controls for each purpose category
- Current consent state (what the user has accepted or rejected)
- One-click "Accept All" / "Reject All" actions
- Consent history summary (when consent was last given or updated)
- Link to the active Cookie Policy and Privacy Policy documents

The panel inherits your application's CSS, so it looks and feels like a native part of your product.

## Why go native?

| Aspect | Popup Widget | Native Preference Center |
|--------|-------------|------------------------|
| **User experience** | Interrupts the user flow | Lives in the app's settings — visited intentionally |
| **Brand consistency** | Styled overlay on top of your app | Inherits your app's design system |
| **User trust** | Feels like a legal obligation | Feels like a product feature |
| **Accessibility** | Focus traps, z-index conflicts | Standard page content, fully accessible |
| **Mobile** | Often cramped on small screens | Flows naturally within your responsive layout |
| **Repeat management** | User must find the floating button | User goes to Settings > Privacy (intuitive) |

## Ideal for

- **SaaS products** — embed the preference center in your account settings page
- **Member portals** — place consent management alongside notification preferences and profile settings
- **Mobile web apps** — integrate into your existing settings navigation
- **Intranets and internal tools** — where popup overlays are disruptive to workflow
- **Healthcare and finance apps** — where trust and control are paramount, and the consent UI must feel like part of the secure environment

## How it looks in practice

Imagine your application has a Settings page with tabs: Profile, Notifications, Security, **Privacy**. The Privacy tab contains the Waulter preference center — rendered inline, styled to match your UI:

```
┌─────────────────────────────────────────────────┐
│  Settings                                       │
│  ┌──────┬───────────┬──────────┬─────────┐      │
│  │Profile│Notifications│Security │ Privacy │      │
│  └──────┴───────────┴──────────┴─────────┘      │
│                                                  │
│  Privacy & Consent Preferences                   │
│  ─────────────────────────────                   │
│  Last updated: 15 March 2026                     │
│                                                  │
│  ☑ Essential cookies          Always active      │
│  ☑ Analytics                  [Toggle ON ]       │
│  ☐ Marketing                  [Toggle OFF]       │
│  ☐ Personalisation            [Toggle OFF]       │
│                                                  │
│  [Save preferences]  [Accept all]  [Reject all]  │
│                                                  │
│  📄 Cookie Policy (v3.1) · 📄 Privacy Policy     │
│                                                  │
└─────────────────────────────────────────────────┘
```

Every interaction — toggling a purpose, saving preferences, accepting or rejecting all — generates a [Permission Transaction](permission-tx.md) with the same audit trail as the standard consent banner.

## How to get started

The native preference center is an **assisted integration** — it requires configuration on Waulter's side to enable the embedded mode and adapt it to your application's structure.

!!! info "Contact us for setup"
    The native preference center is available for all Waulter accounts. To enable it:

    1. **Contact the Waulter team** — reach out to us directly at [waulter.eu/contact](https://waulter.eu) or through your account manager
    2. **We enable the embedded mode** on your configuration
    3. **We assist with integration** — our team helps you place the preference center in your application, match your design system, and verify the audit trail

    Setup typically takes 1-2 working days depending on your application's complexity.

## What you don't have to build

By using the Waulter native preference center, you avoid building and maintaining:

- **Consent toggle UI** — all purpose controls are managed by Waulter
- **State management** — consent state persistence, cookie handling, expiry logic
- **GCM signal updates** — Google Consent Mode integration happens automatically
- **Audit trail** — every interaction is recorded with full traceability
- **Document versioning** — policy documents are linked to each consent transaction
- **Cross-domain consent** — [User Sharing](user-sharing.md) works with the native center too
- **Multilanguage support** — the panel respects your configuration's [language settings](../dashboard/texts.md)

You focus on placing the `<div>` in your settings page. Waulter handles everything inside it.

## Combining with the consent banner

The native preference center does not replace the initial consent banner — first-time visitors still see the banner to collect initial consent. The preference center provides an **always-accessible** way for users to review and update their preferences after the initial decision.

A typical setup:

1. **First visit** — standard consent banner appears (popup or bottom bar)
2. **Subsequent visits** — no banner (consent is stored)
3. **User navigates to Settings > Privacy** — the native preference center shows current consent state and allows changes
4. **Floating "Manage cookies" button** — optional, links directly to the Privacy settings page instead of opening a popup

This gives users two clear paths to manage consent: the initial banner and the permanent settings page.

## Privacy by design

Embedding consent management into your application's settings page sends a strong signal to your users: **privacy is a first-class feature, not an afterthought.** For industries where trust is the product differentiator — healthcare, finance, legal — this matters more than any banner design.
