---
title: Scenario-Driven User Engagement
description: >
  How to use Waulter Scenarios creatively — not just for consent collection,
  but for whitepapers, loyalty programs, sales offers, and meaningful
  visitor conversations.
---

# Scenario-Driven User Engagement

Waulter's [Scenarios](../dashboard/scenarios.md) are designed for consent collection — but the same targeting engine can power **creative engagement strategies** that turn the consent interaction into a genuine conversation with your visitors.

## The core idea

The `forceStartCB` flag in a scenario rule forces the consent banner to appear **even when the visitor already has valid stored consent**. Combined with scenario targeting rules (URL, custom fields, pageview count, etc.), this lets you trigger the banner under specific conditions you define.

This means you can use the banner not just for cookie consent, but as a **contextual touchpoint** that adapts to what the visitor is doing on your site.

## Use case 1: Whitepaper or resource download gate

**Scenario:** A visitor lands on a blog post and has already accepted cookies. When they click a whitepaper download link, you want to offer them additional content in exchange for opting into marketing communications.

**How to implement:**

1. Create a scenario rule targeting `url contains /resources/whitepaper`
2. Set `forceStartCB: true`
3. Configure the matched configuration with a custom banner text:
   *"You're about to download our industry report. Would you also like to receive insights and updates from us?"*
4. Map the "Marketing" purpose to this interaction

**Result:** The visitor sees a contextual prompt exactly when they're most engaged — downloading valuable content. This is far more effective than a generic banner on page load.

## Use case 2: Loyalty program opt-in

**Scenario:** After a visitor has viewed 5+ pages (indicating genuine interest), prompt them to join your loyalty program or newsletter.

**How to implement:**

1. Create a scenario rule: `pageview > 5`
2. Set `forceStartCB: true`
3. Use a configuration with tailored text:
   *"You seem to be enjoying our content! Join our loyalty program for exclusive offers and early access."*
4. Link the "Personalisation" purpose to loyalty tracking

**Result:** Instead of bombarding first-time visitors, you engage only those who have demonstrated interest through their browsing behaviour.

## Use case 3: Post-purchase upsell consent

**Scenario:** A customer completes a purchase and lands on the thank-you page. Prompt them to opt into remarketing and personalised recommendations.

**How to implement:**

1. Create a scenario rule: `url contains /thank-you` or `customField1 equals "purchased"`
2. Set `forceStartCB: true`
3. Configure banner text:
   *"Thank you for your order! Would you like personalised product recommendations and exclusive deals?"*
4. Enable "Remarketing" (`PU072`) and "Ad Personalisation" (`PU074`) purposes

**Result:** You capture marketing consent at the moment of highest satisfaction — right after a successful purchase.

## Use case 4: Seasonal campaign or policy update

**Scenario:** You've updated your privacy policy or launched a seasonal campaign and need existing visitors to re-consent.

**How to implement:**

1. Create a scenario rule that targets all pages (or specific campaign URLs)
2. Set `forceStartCB: true`
3. Update the banner text to reference the change:
   *"We've updated our cookie policy for the holiday season. Please review and confirm your preferences."*

**Result:** Compliant re-consent collection with a branded, campaign-relevant message rather than a generic legal prompt.

## Use case 5: A/B test different value propositions

**Scenario:** Test whether visitors respond better to a privacy-focused message or a benefits-focused message.

**How to implement:**

1. Create two configurations with different banner texts:
   - **Config A:** *"We respect your privacy. Choose which cookies you'd like to allow."*
   - **Config B:** *"Allow cookies to get personalised recommendations and faster checkout."*
2. Create a scenario with a `mod` rule: `pageview mod 2 equals 0` → Config A, else → Config B
3. Track consent rates per configuration in [Statistics](../dashboard/statistics.md)

**Result:** Data-driven optimisation of your consent messaging, directly measured through Waulter's built-in analytics.

## Key principles

!!! tip "Respect the visitor"
    The power of scenario-driven engagement comes with responsibility. Every prompt should offer **genuine value** to the visitor — a resource, a benefit, a better experience. Never use `forceStartCB` to nag visitors into changing their consent decision.

!!! info "Every interaction is auditable"
    Even when using scenarios creatively, every visitor interaction is recorded as a [Permission Transaction](../features/permission-tx.md) with full traceability — which configuration was shown, which text, which purposes, and what the visitor chose. This audit trail is maintained regardless of how the banner was triggered.

## Technical setup

For each use case above, you need:

1. **A dedicated configuration** with the appropriate banner text and purposes
2. **A scenario rule** with `forceStartCB: true` and your targeting conditions
3. **Statistics monitoring** to measure the impact

See [Scenarios](../dashboard/scenarios.md) for the full rule syntax and [DEV & Testing Workflow](dev-testing.md) for how to test scenarios safely before going live.
