---
title: Texts & Translations
description: How to change consent banner texts in the dashboard, manage languages, and set up multilanguage consent banners.
---

# Texts & Translations

All consent banner texts — from the main banner message to individual purpose descriptions and button labels — are managed directly in the Waulter dashboard. Changes propagate to live banners without any code deployment.

## What you can customise

| Text element | Where it appears |
|-------------|-----------------|
| **Banner title** | The heading of the first-layer consent banner |
| **Banner description** | The main message explaining why consent is needed |
| **Accept All button** | Label for the "accept all purposes" button |
| **Reject All button** | Label for the "reject all purposes" button |
| **Customise button** | Label for the "open preferences" button |
| **Purpose names** | Category names shown in the preference centre (e.g. "Analytics", "Marketing") |
| **Purpose descriptions** | Detailed explanation of each purpose, shown when expanded |
| **Cookie Policy link text** | The link label pointing to your Cookie Policy page |
| **Privacy Policy link text** | The link label pointing to your Privacy Policy page |
| **Save preferences button** | Label for the button in the preference centre |

## Changing banner texts

1. Open your configuration in the Waulter dashboard.
2. Navigate to the **Texts** section.
3. Edit the text fields for the language you want to modify.
4. Save the configuration.

!!! info "Changes take effect immediately"
    Text changes are fetched from the server on each page load. As soon as you save, visitors loading a new page will see the updated text. No GTM publish or code deploy is required.

## Language selection

Waulter supports multilanguage consent banners. The SDK determines which language to display based on:

1. **Explicit `lang` parameter** — if set in WaulterConfig, this language is always used:
   ```javascript
   window.WaulterConfig = {
     id: "YOUR_CONFIG_ID",
     lang: "de"  // Always show German
   };
   ```

2. **Browser language** — if no `lang` is set, the SDK detects the visitor's browser language and selects the closest matching translation.

3. **Fallback** — if no matching translation is found, the configuration's primary language is used.

## Setting up multilanguage banners

### Step 1 — Add languages in the dashboard

1. Open your configuration.
2. Navigate to **Languages** or **Texts**.
3. Add the languages your site supports (e.g. English, Czech, German, Slovak).

### Step 2 — Translate each text element

For each added language, provide translations for all text elements:

| Element | English | Czech | German |
|---------|---------|-------|--------|
| Banner title | We value your privacy | Chráníme vaše soukromí | Wir schützen Ihre Privatsphäre |
| Accept All | Accept All | Přijmout vše | Alle akzeptieren |
| Reject All | Reject All | Odmítnout vše | Alle ablehnen |
| Customise | Customise | Nastavení | Anpassen |

### Step 3 — Test each language

Use the **Preview** button to test each language variant:

1. Preview the configuration.
2. Change the browser language (or set `lang` in WaulterConfig) to test each translation.
3. Verify that all text elements display correctly.
4. Check for layout issues — some languages use longer words that may affect button widths.

!!! tip "Test with the longest language"
    German and Czech translations are often significantly longer than English. Test these first to catch layout overflow issues.

## Fallback behaviour

| Scenario | Result |
|----------|--------|
| Visitor's browser language matches a configured language | That language is displayed |
| Visitor's language is `en-GB` and you have `en` configured | Matches on the base language (`en`) |
| Visitor's language has no matching translation | The configuration's primary language is used |
| `lang` is set in WaulterConfig | That language is always used, regardless of browser settings |

## How text changes propagate

Because Waulter is a hosted CMP, text content is fetched from the server on every page load:

- **No caching issues** — visitors always see the latest text
- **Instant updates** — save in the dashboard, see the change on the next page load
- **No code deploy** — marketing, legal, or DPO teams can update text without involving developers
- **Version control** — the dashboard maintains a history of text changes

This is a key advantage over self-hosted consent solutions where text changes require code changes and deployment.

## Best practices

- **Keep banner text concise** — visitors are more likely to read and interact with short, clear messages
- **Use plain language** — avoid legal jargon in the first-layer banner; save detailed legal language for the Cookie Policy document
- **Be transparent** — clearly explain what each purpose category does in the preference centre
- **Consistent terminology** — use the same terms across your banner, preference centre, and Cookie Policy
- **Review after translation** — machine translations may not capture the nuance needed for consent text; have a native speaker review
