---
title: Floating Consent Settings Button
description: >
  The floating button that lets visitors re-open the consent preference centre
  at any time — configuration, positioning, and when to disable it.
---

# Floating Consent Settings Button

The floating button is a small, persistent icon (typically in the bottom-left corner of the page) that lets visitors **re-open the consent preference centre** at any time after their initial consent decision.

## Why it exists

Privacy regulations including GDPR and ePrivacy require that visitors can **withdraw or change their consent at any time**. The floating button provides an always-visible entry point to the preference centre, ensuring compliance without requiring visitors to search for a settings link.

!!! warning "Regulatory requirement"
    Under GDPR, visitors must be able to withdraw consent as easily as they gave it. If you disable the floating button, you **must** provide an alternative — such as a "Manage Cookies" link in your footer or a dedicated [Preference Center](preference-center.md) page. Hiding all consent management options may violate GDPR/ePrivacy requirements.

## What it looks like

The floating button appears as a small circular icon in the **bottom-left corner** of the page. It:

- Appears after the visitor has made their initial consent decision (the banner closes)
- Remains visible on every page as the visitor navigates
- Opens the consent preference centre when clicked (equivalent to calling [`openWidget()`](../sdk/api-reference.md#openwidget))
- Uses your configuration's colour theme and icon (see [Styling](../dashboard/styling.md))

## Configuring the floating button

### Enabling or disabling

The floating button is controlled by the **Control Panel** setting in your configuration:

| Setting | Default | Effect |
|---------|---------|--------|
| **Control Panel Enabled** | Enabled | Shows the floating button after the initial consent decision |

To change this setting:

1. Open your configuration in the Waulter dashboard.
2. Navigate to the banner settings section.
3. Toggle the **Control Panel** option on or off.
4. Save the configuration.

### Styling

The floating button inherits your configuration's branding:

- **Colour** — matches your custom colour theme (see [Styling](../dashboard/styling.md))
- **Icon** — uses your custom icon if configured, or the default Material Icon

## When to disable the floating button

There are legitimate cases where you might want to disable the floating button:

| Scenario | Alternative required |
|----------|---------------------|
| You have a **native preference centre** built into your app's settings page | Your app's settings page must include a consent management option — see [Preference Center](preference-center.md) |
| You have a **"Manage Cookies" link** in your site footer on every page | The footer link must call [`openWidget()`](../sdk/api-reference.md#openwidget) |
| The floating button **conflicts with other UI elements** (chat widget, help button) | Provide an alternative consent management entry point |
| **Design requirements** demand a clean interface | Still provide an alternative — see examples below |

### Alternative: footer link

If you disable the floating button, the most common alternative is a footer link:

```html
<a href="#" onclick="window.WaulterSDK.openWidget(); return false;">
  Cookie Settings
</a>
```

Place this in your site footer so it appears on every page.

### Alternative: settings page link

For SPAs or apps with a settings section:

```javascript
// In your settings/preferences component
function openCookieSettings() {
  if (window.WaulterSDK) {
    window.WaulterSDK.openWidget();
  }
}
```

!!! tip "Always test the alternative"
    After disabling the floating button, verify that your alternative consent management path is accessible from every page on your site. A visitor should never be more than one click away from managing their consent.

## Accessibility

The floating button is designed to be accessible:

| Feature | Implementation |
|---------|---------------|
| **Keyboard navigation** | Reachable via Tab key and activatable with Enter/Space |
| **Screen readers** | Labelled with descriptive text (e.g. "Open cookie settings") |
| **Touch targets** | Minimum 44x44 px touch area for mobile devices |
| **Colour contrast** | Follows your theme's colour settings — ensure sufficient contrast (see [Accessibility](../accessibility/index.md)) |

!!! info "WCAG compliance"
    The floating button meets WCAG 2.1 AA requirements when your colour theme provides sufficient contrast. See [WCAG Compliance](../accessibility/wcag.md) for details.

## Interaction with other features

| Feature | Behaviour |
|---------|----------|
| **Banner suppression** (`?no_waulter_cb`) | The floating button is also suppressed — the SDK does not start |
| **Scenarios with `forceStartCB`** | The floating button remains available between forced banner displays |
| **SPA navigation** | The button persists across client-side route changes |
| **openWidget()** | Clicking the floating button is functionally identical to calling `openWidget()` programmatically |

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Floating button not visible | Control Panel disabled | Enable it in the dashboard configuration |
| Floating button not visible | `?no_waulter_cb` in URL | Remove the parameter — it suppresses the entire SDK UI |
| Floating button overlaps chat widget | Z-index or position conflict | Use CSS to adjust the position of one of the elements |
| Floating button appears before consent | SDK timing issue | This is a bug — the button should only appear after the initial decision. Check SDK version. |
| Floating button not keyboard-accessible | Custom CSS override breaking focus | Check that no CSS rule sets `outline: none` or `pointer-events: none` on the button |
