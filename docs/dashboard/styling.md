---
title: Styling & Templates
description: >
  Showcase of the built-in banner templates, custom colour themes, custom fonts,
  custom icons, and agency custom templating capabilities.
---

# Styling & Templates

Waulter provides multiple ways to customise the appearance of your consent banner — from choosing a built-in template to fully customising colours, fonts, and icons to match your brand.

## Built-in banner templates

Waulter ships with **multiple built-in banner templates**. Each template provides a different layout and visual style while maintaining full functionality and [accessibility compliance](../accessibility/index.md).

### Choosing a template

1. Open your configuration in the Waulter dashboard.
2. Navigate to the **Styling** or **Template** section.
3. Browse the available templates.
4. Select the one that best fits your site's design.
5. Save the configuration.

### Template characteristics

| Aspect | All templates include |
|--------|----------------------|
| **First layer** | Main consent banner with Accept All / Reject All / Customise buttons |
| **Second layer** | Preference centre with per-purpose toggles |
| **Responsive design** | Adapts to mobile, tablet, and desktop viewports |
| **Accessibility** | WCAG-compliant keyboard navigation and screen reader support |
| **Animation** | Smooth open/close transitions |

!!! tip "Preview before choosing"
    Use the **Preview** button in the dashboard to see each template with your configuration's text and purposes before going live.

## Custom colour theme

Customise the banner's colour scheme to match your brand identity using the **Custom Colour Theme** settings in the dashboard.

| Colour property | Controls | Example |
|----------------|----------|---------|
| **Primary** | Action buttons (Accept All), links, toggles | `#1a73e8` |
| **Secondary** | Secondary buttons (Reject All, Customise), borders | `#ffffff` |
| **Background** | Banner background | `#f5f5f5` |
| **Text** | Body text, descriptions | `#333333` |

### Setting custom colours

1. Open your configuration in the dashboard.
2. Navigate to **Styling** > **Custom Colours**.
3. Enter hex colour values or use the colour picker.
4. Preview the result.
5. Save.

!!! warning "Maintain contrast ratios"
    Ensure your colour choices maintain sufficient contrast for readability and accessibility:

    - **Text on background**: minimum 4.5:1 contrast ratio (WCAG AA)
    - **Buttons**: minimum 3:1 contrast ratio for large text
    - Use a contrast checker tool to verify your combinations

    Poor contrast makes the banner unreadable for visitors with visual impairments and may violate accessibility requirements.

## Custom font family

Override the banner's default font to match your site's typography:

1. Navigate to **Styling** > **Font** in the dashboard.
2. Enter a font family value (e.g. `Roboto, sans-serif`).
3. Save.

**Notes on web fonts:**

- If using a web font (e.g. Google Fonts), ensure it is loaded on your page before the banner renders.
- The banner falls back to system fonts (`sans-serif`) if the specified font is not available.
- Avoid fonts with very large file sizes — they can delay banner rendering.

```html
<!-- Load your custom font before the Waulter SDK -->
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap">
```

## Custom icon / logo

Replace the default Waulter icon with your own logo or brand mark:

1. Navigate to **Styling** > **Icon** in the dashboard.
2. Upload an image or enter a URL to your icon.
3. Save.

**Recommendations:**

| Property | Guideline |
|----------|-----------|
| Format | PNG or SVG (SVG recommended for crispness) |
| Size | 32×32 px minimum, 64×64 px recommended |
| Background | Transparent background works best |
| Placement | The icon appears in the banner header area |

## Material Icons

The consent banner UI uses **Material Icons** for interface elements (close button, toggle switches, expand/collapse indicators). The icon font is loaded from Google Fonts:

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
```

This stylesheet is included in the standard SDK setup. If you are using the [GTM Community Template](../implementation/gtm/community-template.md), it is loaded automatically.

!!! info "Material Icons are optional but recommended"
    The banner functions without Material Icons, but some interface elements (buttons, toggles) may not display correctly. Always include the stylesheet for the best visual experience.

## CSS targeting

The consent banner is rendered in your page's DOM (not in an iframe), which means your site's CSS can affect it. To target the banner specifically:

```css
/* Target Waulter banner elements */
.waulter-banner {
  /* Your custom overrides */
}

/* Target the preference centre */
.waulter-preferences {
  /* Your custom overrides */
}
```

!!! warning "CSS specificity"
    Avoid overly broad CSS selectors (e.g. `div`, `button`, `*`) in your site's stylesheets — they can unintentionally affect the banner's appearance. Use specific selectors when overriding banner styles.

## Agency custom templating

Agencies managing multiple client websites can create **custom banner templates** for consistent branding across deployments.

### How custom templating works

1. Define a base template with your agency's design system.
2. Configure per-client customisations (colours, fonts, logo) in each client's configuration.
3. Use the [template duplication workflow](copy-config.md) to efficiently roll out updates.

### What can be customised

| Level | What | How |
|-------|------|-----|
| **Template** | Layout, component structure, animation | Contact Waulter for custom template development |
| **Colours** | Full colour theme per client | Dashboard > Styling > Custom Colours |
| **Fonts** | Typography per client | Dashboard > Styling > Font |
| **Icon** | Logo per client | Dashboard > Styling > Icon |
| **Text** | All copy per client | Dashboard > [Texts](texts.md) |

See the [Agency Guide](../agency/index.md) for the complete agency workflow.
