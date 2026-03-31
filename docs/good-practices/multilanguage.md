---
title: Multilanguage Setup
description: >
  How to set up multilanguage consent: language codes, per-language text management,
  testing across languages, and common pitfalls.
---

# Multilanguage Setup

This guide covers best practices for setting up and managing consent banners in multiple languages, ensuring all visitors see the banner in their preferred language.

## Language codes

Waulter uses **ISO 639-1** language codes:

| Code | Language |
|------|----------|
| `cs` | Czech |
| `sk` | Slovak |
| `en` | English |
| `de` | German |
| `fr` | French |
| `pl` | Polish |
| `hu` | Hungarian |
| `es` | Spanish |
| `it` | Italian |

## Adding languages to your configuration

1. Open your configuration in the Waulter dashboard.
2. Navigate to the **Languages** or **Texts** section.
3. Add each language your site supports.
4. Set the **default/fallback language** — this is shown when no translation matches the visitor's browser language.

## How the SDK detects the visitor's language

The SDK determines which language to display using this priority:

| Priority | Source | Example |
|----------|--------|---------|
| 1 (highest) | `lang` parameter in WaulterConfig | `lang: "de"` always shows German |
| 2 | Visitor's browser language (`navigator.language`) | `cs-CZ` → matches `cs` |
| 3 (fallback) | Configuration's default language | Shows default if no match |

```javascript
// Force a specific language
window.WaulterConfig = {
  id: "YOUR_CONFIG_ID",
  useGtm: true,
  lang: "de"  // Always show German, regardless of browser settings
};
```

!!! tip "Synchronise with your site's language"
    If your site has a language switcher, pass the selected language to Waulter via the `lang` parameter. This ensures the banner language matches the page language:

    ```javascript
    window.WaulterConfig = {
      id: "YOUR_CONFIG_ID",
      useGtm: true,
      lang: document.documentElement.lang || "en"  // Read from <html lang="...">
    };
    ```

## Per-language text management

### What needs translation

| Text element | Translate? | Notes |
|-------------|-----------|-------|
| Banner title | Yes | Keep short — some languages are 30-50% longer than English |
| Banner description | Yes | Main consent message |
| Accept All button | Yes | Keep concise |
| Reject All button | Yes | Keep concise |
| Customise button | Yes | Keep concise |
| Purpose names | Yes | Must be clear in each language |
| Purpose descriptions | Yes | Can be longer — shown in preference centre |
| Cookie Policy link text | Yes | |
| Privacy Policy link text | Yes | |

### Translation workflow

1. Start with your primary language (e.g. Czech).
2. Complete all text fields.
3. Switch to the next language in the dashboard.
4. Translate all fields.
5. Repeat for each language.
6. Preview each language to verify.

## Testing across languages

### Method 1: Use the `lang` parameter

Set `lang` in your WaulterConfig to force a specific language:

```javascript
window.WaulterConfig = { id: "YOUR_CONFIG_ID", lang: "de" };
```

### Method 2: Change browser language

1. Open browser settings > Language.
2. Move the target language to the top of the list.
3. Reload the page.

### Method 3: Use the Preview button

The dashboard Preview button shows the banner with current settings. Switch languages in the dashboard to preview each one.

### What to check

| Check | What to look for |
|-------|-----------------|
| **Text completeness** | All fields have translations (no English mixed with Czech) |
| **Layout** | Text fits within buttons and banner area (no overflow or truncation) |
| **Grammar** | Translations are natural, not machine-translated jargon |
| **Consistency** | Same terminology used across all text elements in a language |
| **Button width** | Longer translations don't cause buttons to wrap or shrink |

## Common pitfalls

### Missing translations

**Problem:** A language is added but some text fields are not translated.

**Result:** The untranslated field falls back to the default language, creating a mixed-language banner (e.g. German banner with one Czech button).

**Fix:** Complete all text fields for every added language before going live.

### Text overflow

**Problem:** A translation is significantly longer than the original, causing layout issues.

**Result:** Buttons wrap to the next line, text overflows the banner area, or text is truncated.

**Fix:** Keep translations concise. If a direct translation is too long, rephrase to fit. Test with the longest languages first (German, Czech tend to be longer than English).

### Language mismatch

**Problem:** The banner shows in a different language than the page content.

**Result:** Visitor confusion — the page is in English but the banner is in Czech.

**Fix:** Set the `lang` parameter to match your page's language:

```javascript
window.WaulterConfig = {
  id: "YOUR_CONFIG_ID",
  lang: document.documentElement.lang  // Match <html lang="...">
};
```

### Incomplete testing

**Problem:** Only the primary language was tested; other languages were not previewed.

**Fix:** Always preview every configured language before going live. Add language testing to your [go-live checklist](dev-testing.md#checklist-before-go-live).
