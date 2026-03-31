---
title: Special Messages & Marketing Offers
description: >
  How to display marketing messages, announcements, and value-exchange offers
  within the Waulter consent interaction — turning consent into a marketing touchpoint.
---

# Special Messages & Marketing Offers

Waulter can display **special messages** within the consent interaction — promotional offers, announcements, or value exchanges that appear alongside or after the consent decision. This turns the consent moment into a **marketing touchpoint** while remaining fully compliant.

!!! tip "Turn consent into a conversation"
    See [Scenario-Driven Engagement](../good-practices/scenario-engagement.md) and [Consent Strategy — Running Permissions](../good-practices/consent-strategy.md) for creative ways to use special messages with scenarios — whitepaper gates, loyalty programs, vouchers and coupons.

## What are special messages?

A special message is configurable content attached to a configuration that the SDK displays during or after the consent interaction. Instead of a plain consent banner, visitors see an additional message — an offer, announcement, or call-to-action.

**Examples:**

| Type | Example message |
|------|----------------|
| **Promotional offer** | "Accept cookies and get a 10% discount code: WELCOME10" |
| **Social proof** | "Join 85% of our visitors who trust us with their data" |
| **Content offer** | "Allow analytics and download our free industry whitepaper" |
| **Announcement** | "We've updated our privacy practices — here's what changed" |
| **Call-to-action** | "Sign up for our newsletter for exclusive insights" |

## Configuring special messages

Special messages are configured in the Waulter dashboard as part of your website configuration.

### Setup steps

1. Open your configuration in the Waulter dashboard.
2. Navigate to the **Marketing Message** section.
3. Enable the marketing message.
4. Enter your message content (supports HTML).
5. Optionally, set a **Campaign ID** to track this specific message.
6. Save the configuration.

### Configuration options

| Option | Description |
|--------|-------------|
| **Enabled** | Toggle the marketing message on or off |
| **Message content** | HTML content displayed to the visitor. Can include text, links, images, and formatting. |
| **Campaign ID** | An identifier for tracking purposes (e.g. `summer-sale-2026`, `whitepaper-offer`). Included in the analytics event. |

!!! info "HTML content"
    The message field supports HTML, so you can include formatted text, links, images, or even embedded forms. The content inherits your banner's styling.

## Conditional display

Special messages can be made **conditional** by combining them with [Scenarios](../dashboard/scenarios.md). Create different configurations with different messages, then use scenario rules to target them:

| Condition | Scenario rule | Message |
|-----------|--------------|---------|
| First-time visitor | `acceptance equals "none"` | "Welcome! Accept cookies for the best experience" |
| Returning visitor (5+ pages) | `pageview > 5` | "You're a regular! Join our loyalty program" |
| Specific page | `url contains /pricing` | "Need help choosing? Enable personalisation for tailored recommendations" |
| Post-purchase | `customField1 equals "purchased"` | "Thank you! Allow remarketing for exclusive post-purchase offers" |

See [Scenario-Driven Engagement](../good-practices/scenario-engagement.md) for detailed implementation patterns.

## Tracking with the `special_functions` event

When a special message is displayed and the visitor makes a consent decision, the SDK fires a `special_functions` event via `gtag`:

```javascript
gtag('event', 'special_functions', {
  type: 'message',
  consent_decision: 'allow',   // The visitor's consent decision
  metadata: {
    campaignID: 'summer-sale-2026'   // Your Campaign ID from the configuration
  }
});
```

### Event fields

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Always `'message'` for special messages |
| `consent_decision` | string | The visitor's decision: `'allow'`, `'mixed'`, or `'reject'` |
| `metadata.campaignID` | string | The Campaign ID you configured in the dashboard |

### Tracking in GTM

To track special message engagement in Google Tag Manager:

1. **Create a Custom Event trigger:**
   - Event name: `special_functions`

2. **Create a Data Layer Variable** for the campaign ID:
   - Variable type: Data Layer Variable
   - Data Layer Variable Name: `metadata.campaignID`

3. **Create a GA4 Event tag:**
   - Event name: `consent_marketing_message`
   - Parameters: `campaign_id` = your Data Layer Variable
   - Trigger: the Custom Event trigger from step 1

4. **Measure results:**
   - Track impression-to-consent conversion rates per campaign
   - Compare consent rates between configurations with and without special messages
   - A/B test different messages using scenario rules with the `mod` operator

## Best practices

| Practice | Why |
|----------|-----|
| **Keep messages short** | The consent banner is not a landing page. One sentence + one CTA is ideal. |
| **Offer genuine value** | Visitors respond to offers that benefit them, not to guilt or urgency tactics. |
| **Use Campaign IDs** | Track every message variant so you can measure what works. |
| **Combine with scenarios** | Target messages to specific visitor segments for higher relevance. |
| **Test before deploying** | Preview messages on your [DEV configuration](../good-practices/dev-testing.md) before going live. |
| **Respect the consent context** | The primary purpose of the banner is consent. Marketing messages should complement, not overshadow, the consent interaction. |

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Message not displayed | Marketing message not enabled | Enable it in the dashboard configuration |
| Message not displayed | Configuration not active | Check that the configuration status is active |
| `special_functions` event not firing | `useGtm` not enabled | Ensure `useGtm: true` in your WaulterConfig |
| Wrong message shown | Scenario targeting mismatch | Verify which scenario rule is matching — see [Scenarios](../dashboard/scenarios.md) |
| Message styling looks wrong | Banner CSS conflict | Use browser DevTools to inspect and adjust styles |
