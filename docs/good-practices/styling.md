---
title: Styling & Brand Customization
description: >
  How to apply brand colours consistently, test colour contrast for accessibility,
  handle dark mode, integrate Material Icons, and work with CSS overrides.
---

# Styling & Brand Customization

This guide covers best practices for customising the Waulter consent banner's appearance to match your brand while maintaining accessibility and visual consistency.

## Applying brand colours

Use the [custom colour theme](../dashboard/styling.md#custom-colour-theme) settings in the dashboard to apply your brand colours:

| Colour property | What it controls | Tips |
|----------------|-----------------|------|
| **Primary** | Accept All button, links, active toggles | Use your primary brand colour |
| **Secondary** | Reject All button, Customise button, borders | Use a neutral or complementary colour |
| **Background** | Banner background | Use white or a light neutral for readability |
| **Text** | Body text, descriptions, labels | Use dark colours for maximum readability |

### Colour pairing guidelines

| Do | Don't |
|----|-------|
| High contrast between text and background | Light text on light background |
| Distinct primary and secondary colours | Primary and secondary that are too similar |
| Test on both light and dark pages | Assume your colours work everywhere |

## Testing colour contrast for accessibility

WCAG 2.1 Level AA requires minimum contrast ratios:

| Element | Minimum ratio | How to check |
|---------|--------------|-------------|
| Normal text (< 18px) | 4.5:1 | Text colour vs background colour |
| Large text (≥ 18px bold or ≥ 24px) | 3:1 | Heading colour vs background colour |
| UI components (buttons, toggles) | 3:1 | Button colour vs surrounding area |

### Recommended tools

| Tool | Use |
|------|-----|
| **Chrome DevTools colour picker** | Hover over colours to see contrast ratio |
| **WebAIM Contrast Checker** | Enter foreground/background colours to calculate ratio |
| **axe DevTools extension** | Automated accessibility audit that flags contrast issues |
| **Lighthouse Accessibility audit** | Scans the page for contrast failures |

### Common contrast failures

| Problem | Fix |
|---------|-----|
| Light brand blue on white background | Darken the blue or use a tinted background |
| Grey text on light background | Use darker text (minimum `#595959` on white) |
| White text on light primary button | Darken the button colour or switch to dark text |

## Dark mode considerations

If your site supports dark mode (`prefers-color-scheme: dark`), verify that the banner is readable in both modes:

1. Set your dashboard colours for light mode (default).
2. Test the banner in your site's dark mode.
3. If the banner is hard to read in dark mode, consider:
    - Using a fixed background colour that works in both modes (e.g. white banner always)
    - Providing a dark-mode colour override via CSS
    - Testing with the Waulter preview in both modes

```css
/* Example: override banner background for dark mode */
@media (prefers-color-scheme: dark) {
  .waulter-banner {
    background-color: #1a1a2e;
    color: #e0e0e0;
  }
}
```

## Material Icons

The banner uses Material Icons for interface elements. The icon font is loaded via:

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
```

- If your site already loads Material Icons, there is no conflict (the font is only loaded once).
- If you use a different icon library, Material Icons co-exists without issues.
- The icon font is ~42 KB (woff2) — loaded once and cached by the browser.

## CSS override tips

The banner renders in your page's DOM, so your CSS can affect it. Use specific selectors when overriding:

### Safe overrides

```css
/* Adjust font size */
.waulter-banner {
  font-size: 14px;
}

/* Round button corners */
.waulter-banner button {
  border-radius: 8px;
}

/* Add spacing */
.waulter-banner .waulter-content {
  padding: 1.5rem;
}
```

### Overrides to avoid

| Override | Risk |
|----------|------|
| `* { ... }` selectors | Affects banner unintentionally |
| `!important` on everything | Makes future updates difficult |
| `display: none` on banner elements | Breaks accessibility (hidden but still in tab order) |
| Overriding focus styles | Breaks keyboard navigation |
| Changing `position` property | Can cause CLS issues |

### Testing overrides

1. Apply your CSS overrides.
2. Test using the dashboard **Preview** button.
3. Verify in all three built-in templates (if you might switch templates).
4. Check [accessibility](../accessibility/index.md) — run an axe audit after applying overrides.
5. Test on mobile viewports — overrides that work on desktop may break mobile layout.

## Template-specific considerations

Each built-in template has a different layout structure. CSS overrides may apply differently across templates:

- Test your overrides with your current template **and** at least one alternative
- Use template-specific class selectors if your override should only apply to one template
- Avoid hard-coded pixel values that may not work across templates — use relative units (`rem`, `em`, `%`)

## Checklist

- [ ] Brand colours set in dashboard > Styling
- [ ] Contrast ratios checked (4.5:1 for text, 3:1 for UI components)
- [ ] Banner previewed in light mode
- [ ] Banner previewed in dark mode (if applicable)
- [ ] Material Icons stylesheet loaded
- [ ] CSS overrides tested across viewports (desktop, tablet, mobile)
- [ ] Accessibility audit passed after customisation
- [ ] Custom font loaded before SDK (if using custom font)
