---
title: Login Button (B2B Zone Access)
description: >
  How the Waulter consent banner's login button gives site owners and agency
  personnel quick access to the B2B self-care dashboard.
---

# Login Button

The Waulter consent banner can display a **login button** that takes users directly to the B2B self-care zone. This gives site owners, agency personnel, and client managers a fast shortcut to the Waulter dashboard — right from the banner on their website.

## Who is it for?

| User | Why they use it |
|------|----------------|
| **Site owners** | Quick access to their configuration, statistics, and consent data without navigating to the dashboard URL manually |
| **Agency personnel** | Checking a client's configuration while viewing the client's website — see the [Agency Guide](../agency/index.md) |
| **Client managers** | Reviewing their consent setup directly from the site they manage |

## Where does it appear?

The login button is rendered inside the consent banner, typically in the header or action area of the banner UI. It is visible alongside the consent options (Accept, Reject, Preferences).

!!! info "Visitors vs. managers"
    The login button is primarily useful for site managers, not regular visitors. Most visitors will never use it. If your audience is entirely end-consumers and you want a cleaner banner, you can disable it.

## Enabling or disabling the login button

The login button is controlled by a setting in your configuration:

| Setting | Default | Effect |
|---------|---------|--------|
| **Waulter Login Enabled** | Enabled | Shows the login button in the consent banner |

To change this setting:

1. Open your configuration in the Waulter dashboard.
2. Navigate to the banner settings section.
3. Toggle the **Waulter Login** option on or off.
4. Save the configuration.

!!! tip "Per-configuration control"
    The login button setting is per-configuration. You can enable it on your internal/staging configuration (where your team interacts with it) and disable it on your production configuration (where only visitors see the banner).

## The `Waulter:Login` event

When a user clicks the login button, the SDK fires a `Waulter:Login` event on both the data layer and as a DOM `CustomEvent`:

**Data layer:**

```javascript
// Pushed to window.dataLayer
{
  event: "Waulter:Login",
  customField1: "...",   // Current custom fields (if configured)
  customField2: "..."
}
```

**DOM CustomEvent:**

```javascript
// Fires on both window and document
window.addEventListener('Waulter:Login', function(e) {
  console.log('Login button clicked', e.detail);
});

// Legacy event name also supported
window.addEventListener('cb:login', function(e) {
  console.log('Login button clicked', e.detail);
});
```

### Tracking login clicks in GTM

You can create a GTM trigger to track login button usage:

1. In GTM, go to **Triggers** > **New** > **Custom Event**.
2. Set **Event name** to `Waulter:Login`.
3. Attach this trigger to a Google Analytics event tag to measure how often the login button is used.

This can help you understand whether your team is actively using the shortcut, or whether it can be safely disabled.

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Login button not visible | Setting disabled in configuration | Enable **Waulter Login** in the dashboard |
| Login button does not appear on one domain | Different configuration used | Check which Configuration ID or Scenario is deployed on that domain |
| Click does nothing | Browser popup blocker | The login opens the B2B zone in a new tab — check popup blocker settings |
