---
title: WCAG 2.1 Compliance Statement
description: Waulter's formal WCAG 2.1 Level AA compliance statement.
---

# WCAG 2.1 Compliance Statement

## Conformance level

Waulter targets **WCAG 2.1 Level AA** conformance for all consent UI elements, including the consent banner, preference centre, and policy document rendering.

## Scope

This compliance statement covers the following Waulter components:

| Component | Description |
|-----------|-------------|
| **Consent banner** (first layer) | The initial consent prompt with Accept All, Reject All, and Customise buttons |
| **Preference centre** (second layer) | The detailed view with per-purpose toggles and descriptions |
| **Consent withdrawal trigger** | The re-open button/icon for changing consent |
| **Policy document rendering** | Documents rendered via `appendDocument` |

## WCAG 2.1 Level AA criteria

### Perceivable

| Criterion | Status | Notes |
|-----------|--------|-------|
| 1.1.1 Non-text Content | Conformant | All icons have text alternatives |
| 1.3.1 Info and Relationships | Conformant | Proper heading structure, ARIA roles, form labels |
| 1.3.2 Meaningful Sequence | Conformant | DOM order matches visual order |
| 1.4.1 Use of Colour | Conformant | Information is not conveyed by colour alone |
| 1.4.3 Contrast (Minimum) | Conformant | Default themes meet 4.5:1 for text, 3:1 for UI |
| 1.4.4 Resize Text | Conformant | Banner is readable at 200% zoom |
| 1.4.11 Non-text Contrast | Conformant | UI components meet 3:1 contrast |

!!! warning "Custom colour themes"
    When using custom colours, it is the implementer's responsibility to verify contrast ratios. Waulter's default themes are conformant, but custom themes may not be.

### Operable

| Criterion | Status | Notes |
|-----------|--------|-------|
| 2.1.1 Keyboard | Conformant | All functionality available via keyboard |
| 2.1.2 No Keyboard Trap | Conformant | Focus can be moved out of the banner (via decision or Escape) |
| 2.4.3 Focus Order | Conformant | Logical tab order through banner elements |
| 2.4.7 Focus Visible | Conformant | Visible focus indicator on all interactive elements |
| 2.3.1 Three Flashes | Conformant | No flashing content |

### Understandable

| Criterion | Status | Notes |
|-----------|--------|-------|
| 3.1.1 Language of Page | Conformant | `lang` attribute set on banner dialog |
| 3.2.1 On Focus | Conformant | No context changes on focus |
| 3.2.2 On Input | Conformant | No unexpected context changes on input |

### Robust

| Criterion | Status | Notes |
|-----------|--------|-------|
| 4.1.1 Parsing | Conformant | Valid HTML |
| 4.1.2 Name, Role, Value | Conformant | All UI components have accessible names and roles |

## Known limitations

| Area | Limitation | Mitigation |
|------|-----------|-----------|
| Custom CSS overrides | Implementers may break contrast or focus styles with CSS overrides | Documentation warns against this; testing guidance provided |
| Custom colour themes | Non-default colour combinations may not meet contrast requirements | Contrast checking tools recommended in documentation |
| Third-party font loading | Custom fonts that fail to load may cause visual inconsistency | System font fallback ensures readability |

## Reporting accessibility issues

If you encounter an accessibility barrier in any Waulter component, please report it through the Waulter dashboard support channel or contact your account manager. Include:

- A description of the issue
- The browser and assistive technology used
- Steps to reproduce
- The expected vs actual behaviour

## Review schedule

This compliance statement is reviewed and updated with each major release of the Waulter SDK.
