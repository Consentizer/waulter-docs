---
title: Accessibility
description: >
  How Waulter is built to be accessible to all users, including those using
  assistive technologies.
---

# Accessibility

Waulter is designed to meet **WCAG 2.1 Level AA** accessibility requirements. The consent banner and preference centre are built to be usable by all visitors, including those who rely on keyboards, screen readers, and other assistive technologies.

## Keyboard navigation

All interactive elements in the consent banner are reachable and operable via keyboard:

| Key | Action |
|-----|--------|
| **Tab** | Move focus to the next interactive element |
| **Shift + Tab** | Move focus to the previous interactive element |
| **Enter / Space** | Activate the focused button or toggle |
| **Escape** | Close the preference centre (returns to the first layer) |

### Focus management

- When the banner opens, focus moves to the banner's first interactive element
- Focus is **trapped** within the banner while it is open — Tab cycles through banner elements only
- When the banner closes, focus returns to the element that triggered it (or to the page body)
- The focus trap prevents visitors from accidentally interacting with page content behind the banner

## Screen reader support

The banner uses proper ARIA attributes for screen reader compatibility:

| Element | ARIA attribute | Purpose |
|---------|---------------|---------|
| Banner container | `role="dialog"` | Announces the banner as a dialog |
| Banner | `aria-label` | Provides a descriptive label for the dialog |
| Preference centre | `role="dialog"` | Announces the preference centre as a dialog |
| Purpose toggles | `role="switch"` | Announces as on/off switches |
| Buttons | Native `<button>` | Properly announced by screen readers |
| Banner content | `aria-live="polite"` | Dynamic content changes are announced |
| Language | `lang` attribute | Matches the banner's display language |

### Screen reader experience

A screen reader user will hear:

1. "Consent dialog" (when the banner opens)
2. The banner title and description
3. "Accept All, button" / "Reject All, button" / "Customise, button"
4. In the preference centre: "Analytics, switch, off" / "Marketing, switch, off"

## Colour contrast

The default Waulter templates meet WCAG 2.1 AA contrast requirements:

| Element | Requirement | Default compliance |
|---------|------------|-------------------|
| Normal text | 4.5:1 contrast ratio | Met |
| Large text (headings) | 3:1 contrast ratio | Met |
| UI components (buttons, toggles) | 3:1 contrast ratio | Met |
| Focus indicators | Visible focus ring | Met |

!!! warning "Custom colours may break contrast"
    If you customise colours via the dashboard, verify that your colour combinations still meet contrast requirements. Use the tools listed in [Styling — Testing colour contrast](../good-practices/styling.md#testing-colour-contrast-for-accessibility).

## Touch targets

All interactive elements meet the minimum **44 × 44 pixel** touch target size recommended by WCAG. This ensures visitors using touch screens (mobile, tablet) can accurately tap buttons and toggles.

## Reduced motion

Banner entrance and exit animations respect the `prefers-reduced-motion` media query:

- When `prefers-reduced-motion: reduce` is set, animations are disabled or minimised
- The banner appears and disappears without animation
- This benefits visitors who experience motion sickness or vestibular disorders

## Implementer responsibilities

When implementing Waulter, ensure you maintain accessibility:

### Do

- Keep the consent withdrawal trigger (re-open button) visible and reachable
- Ensure the DOM element used for `appendDocument` is in the natural tab order
- Include the Material Icons stylesheet for proper icon rendering
- Test with a screen reader after deployment

### Don't

- Hide the banner or re-open button with `display: none` or `visibility: hidden`
- Override focus styles (`outline: none`) without providing an alternative
- Break contrast ratios with custom CSS overrides
- Remove `aria-*` attributes via JavaScript

## Testing accessibility

### Recommended tools

| Tool | What it tests |
|------|--------------|
| **axe DevTools** (browser extension) | Automated WCAG audit — finds contrast, ARIA, and structure issues |
| **Lighthouse Accessibility audit** | Built into Chrome DevTools — scores accessibility out of 100 |
| **NVDA** (Windows) | Free screen reader — test the full keyboard/screen reader experience |
| **VoiceOver** (macOS/iOS) | Built-in screen reader — test on Apple devices |
| **Chrome Accessibility Tree** | DevTools > Elements > Accessibility pane — inspect ARIA roles and properties |

### Manual testing checklist

- [ ] **Tab through the banner** — all buttons and toggles are reachable in logical order
- [ ] **Focus trap works** — Tab cycles within the banner, not to page content behind it
- [ ] **Escape closes** — pressing Escape closes the preference centre
- [ ] **Screen reader announces** — dialog opens, buttons and toggles are announced correctly
- [ ] **Contrast passes** — no contrast warnings in axe DevTools audit
- [ ] **Touch targets** — buttons are large enough to tap on mobile
- [ ] **Reduced motion** — banner animation is disabled with `prefers-reduced-motion`
- [ ] **Re-open button** — the consent withdrawal trigger is keyboard-accessible

See [WCAG 2.1 Compliance Statement](wcag.md) for Waulter's formal compliance statement.
